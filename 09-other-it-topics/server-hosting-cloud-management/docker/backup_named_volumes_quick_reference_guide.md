# Backup Named Volumes: Quick Reference Guide

## Method 1: Backup to Another Server

### Using Docker + SSH

```bash
# Backup volume to remote server
docker run --rm \
  -v source_volume:/source:ro \
  -v ~/.ssh:/root/.ssh:ro \
  alpine:3.18 \
  sh -c "tar -czf - -C /source . | ssh user@remote-server 'cat > /backup/volume_$(date +%Y%m%d_%H%M%S).tar.gz'"
```

### Using rsync

```bash
# Create temporary container to expose volume
docker run -d --name temp_backup \
  -v source_volume:/data \
  alpine:3.18 sleep 3600

# Copy volume to staging area
docker cp temp_backup:/data /tmp/volume_backup

# Sync to remote server
rsync -avz --progress /tmp/volume_backup/ user@remote-server:/backup/volumes/

# Cleanup
docker rm -f temp_backup
rm -rf /tmp/volume_backup
```

## Method 2: Backup to DigitalOcean Spaces

### Setup s3cmd

```bash
# Configure s3cmd for DigitalOcean Spaces
cat > ~/.s3cfg << EOF
[default]
access_key = YOUR_SPACES_KEY
secret_key = YOUR_SPACES_SECRET
host_base = nyc3.digitaloceanspaces.com
host_bucket = %(bucket)s.nyc3.digitaloceanspaces.com
use_https = True
EOF
```

### Backup Command

```bash
# Backup volume directly to Spaces
docker run --rm \
  -v source_volume:/source:ro \
  -v ~/.s3cfg:/root/.s3cfg:ro \
  alpine:3.18 \
  sh -c "apk add --no-cache python3 py3-pip && \
         pip3 install s3cmd && \
         tar -czf - -C /source . | \
         s3cmd put - s3://your-bucket/backups/volume_$(date +%Y%m%d_%H%M%S).tar.gz"
```

## Method 3: Backup to AWS S3

### Using AWS CLI

```bash
# Backup to S3
docker run --rm \
  -v source_volume:/source:ro \
  -e AWS_ACCESS_KEY_ID=your_key \
  -e AWS_SECRET_ACCESS_KEY=your_secret \
  -e AWS_DEFAULT_REGION=us-east-1 \
  amazon/aws-cli:latest \
  sh -c "tar -czf - -C /source . | aws s3 cp - s3://your-bucket/backups/volume_$(date +%Y%m%d_%H%M%S).tar.gz"
```

## Automated Backup Script

```bash
#!/bin/bash
# backup-volumes.sh

VOLUMES=("mysql_data" "uploads_data" "logs_data")
BACKUP_DATE=$(date +%Y%m%d_%H%M%S)
DESTINATION=${1:-"spaces"}  # spaces, s3, or server

backup_to_spaces() {
    local volume=$1
    docker run --rm \
      -v ${volume}:/source:ro \
      -v ~/.s3cfg:/root/.s3cfg:ro \
      alpine:3.18 \
      sh -c "apk add --no-cache python3 py3-pip && \
             pip3 install s3cmd && \
             tar -czf - -C /source . | \
             s3cmd put - s3://your-bucket/backups/${volume}_${BACKUP_DATE}.tar.gz"
}

backup_to_server() {
    local volume=$1
    docker run --rm \
      -v ${volume}:/source:ro \
      -v ~/.ssh:/root/.ssh:ro \
      alpine:3.18 \
      sh -c "tar -czf - -C /source . | \
             ssh user@backup-server 'cat > /backup/${volume}_${BACKUP_DATE}.tar.gz'"
}

for volume in "${VOLUMES[@]}"; do
    echo "Backing up $volume..."
    case $DESTINATION in
        "spaces") backup_to_spaces $volume ;;
        "server") backup_to_server $volume ;;
    esac
done
```

## Verification Commands

```bash
# Verify backup integrity
tar -tzf backup_file.tar.gz > /dev/null && echo "Backup OK" || echo "Backup corrupted"

# Check backup size
ls -lh backup_file.tar.gz

# List backup contents
tar -tzf backup_file.tar.gz | head -20
```

## Scheduled Backups with Cron

```bash
# Add to crontab (crontab -e)
# Daily backup at 2 AM
0 2 * * * /path/to/backup-volumes.sh spaces

# Weekly backup to remote server
0 2 * * 0 /path/to/backup-volumes.sh server
```