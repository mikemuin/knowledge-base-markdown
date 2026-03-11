# Restore Named Volumes: Quick Reference Guide

## Method 1: Restore from Another Server

### Using SSH + Docker

```bash
# Create target volume if it doesn't exist
docker volume create target_volume

# Restore from remote server
ssh user@backup-server 'cat /backup/volume_20241206_020000.tar.gz' | \
docker run --rm -i \
  -v target_volume:/target \
  alpine:3.18 \
  sh -c "tar -xzf - -C /target"
```

### Using rsync

```bash
# Download from remote server
rsync -avz --progress user@backup-server:/backup/volumes/ /tmp/restore/

# Restore to volume
docker run --rm \
  -v target_volume:/target \
  -v /tmp/restore:/source:ro \
  alpine:3.18 \
  sh -c "rm -rf /target/* && cp -a /source/* /target/"
```

## Method 2: Restore from DigitalOcean Spaces

### Direct Restore

```bash
# Create target volume
docker volume create target_volume

# Restore from Spaces
docker run --rm \
  -v target_volume:/target \
  -v ~/.s3cfg:/root/.s3cfg:ro \
  alpine:3.18 \
  sh -c "apk add --no-cache python3 py3-pip && \
         pip3 install s3cmd && \
         s3cmd get s3://your-bucket/backups/volume_20241206_020000.tar.gz - | \
         tar -xzf - -C /target"
```

## Method 3: Restore from AWS S3

```bash
# Restore from S3
docker volume create target_volume

docker run --rm \
  -v target_volume:/target \
  -e AWS_ACCESS_KEY_ID=your_key \
  -e AWS_SECRET_ACCESS_KEY=your_secret \
  -e AWS_DEFAULT_REGION=us-east-1 \
  amazon/aws-cli:latest \
  sh -c "aws s3 cp s3://your-bucket/backups/volume_20241206_020000.tar.gz - | \
         tar -xzf - -C /target"
```

## Safe Restore Process

### 1. Stop Services First

```bash
# Stop containers using the volume
docker-compose stop service_name

# Or stop all services
docker-compose down
```

### 2. Backup Current Volume (Safety)

```bash
# Create safety backup before restore
docker run --rm \
  -v current_volume:/source:ro \
  alpine:3.18 \
  tar -czf - -C /source . > safety_backup_$(date +%Y%m%d_%H%M%S).tar.gz
```

### 3. Clear and Restore

```bash
# Clear current volume and restore
docker run --rm \
  -v target_volume:/target \
  alpine:3.18 \
  sh -c "rm -rf /target/* /target/.[^.]*"

# Restore from backup
docker run --rm -i \
  -v target_volume:/target \
  alpine:3.18 \
  tar -xzf - -C /target < backup_file.tar.gz
```

### 4. Restart Services

```bash
# Start services after restore
docker-compose up -d
```

## Batch Restore Script

```bash
#!/bin/bash
# restore-volumes.sh

BACKUP_DATE=${1:-$(date +%Y%m%d)}
SOURCE=${2:-"spaces"}  # spaces, s3, or server
VOLUMES=("mysql_data" "uploads_data" "logs_data")

restore_from_spaces() {
    local volume=$1
    echo "Restoring $volume from Spaces..."

    docker volume create ${volume} 2>/dev/null

    docker run --rm \
      -v ${volume}:/target \
      -v ~/.s3cfg:/root/.s3cfg:ro \
      alpine:3.18 \
      sh -c "apk add --no-cache python3 py3-pip && \
             pip3 install s3cmd && \
             s3cmd get s3://your-bucket/backups/${volume}_${BACKUP_DATE}.tar.gz - | \
             tar -xzf - -C /target"
}

restore_from_server() {
    local volume=$1
    echo "Restoring $volume from server..."

    docker volume create ${volume} 2>/dev/null

    ssh user@backup-server "cat /backup/${volume}_${BACKUP_DATE}.tar.gz" | \
    docker run --rm -i \
      -v ${volume}:/target \
      alpine:3.18 \
      tar -xzf - -C /target
}

echo "Stopping services..."
docker-compose down

for volume in "${VOLUMES[@]}"; do
    case $SOURCE in
        "spaces") restore_from_spaces $volume ;;
        "server") restore_from_server $volume ;;
    esac
done

echo "Starting services..."
docker-compose up -d
```

## Verification Commands

```bash
# Check volume contents after restore
docker run --rm -v restored_volume:/check alpine:3.18 ls -la /check

# Compare file counts
docker run --rm -v restored_volume:/check alpine:3.18 find /check -type f | wc -l

# Check specific files exist
docker run --rm -v restored_volume:/check alpine:3.18 test -f /check/important_file.txt && echo "File exists"
```

## Point-in-Time Restore

### List Available Backups

```bash
# List backups in Spaces
s3cmd ls s3://your-bucket/backups/ | grep volume_name

# List backups on server
ssh user@backup-server 'ls -la /backup/volume_*'
```

### Restore Specific Backup

```bash
# Restore specific backup by date/time
RESTORE_FILE="volume_20241205_143000.tar.gz"

docker run --rm \
  -v target_volume:/target \
  -v ~/.s3cfg:/root/.s3cfg:ro \
  alpine:3.18 \
  sh -c "apk add --no-cache python3 py3-pip && \
         pip3 install s3cmd && \
         s3cmd get s3://your-bucket/backups/${RESTORE_FILE} - | \
         tar -xzf - -C /target"
```

## Emergency Restore Commands

```bash
# Quick restore without stopping services (risky but fast)
docker run --rm \
  -v target_volume:/target \
  alpine:3.18 \
  sh -c "cd /target && tar -xzf - --overwrite" < backup.tar.gz

# Restore to new volume name
docker volume create emergency_restore_volume
# ... then restore to emergency_restore_volume instead of original
```