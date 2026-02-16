# Linux Filesystem Hierarchy Best Practices

## Linux Standard Directory Structure

Understanding the Filesystem Hierarchy Standard (FHS) is crucial for proper system administration and application deployment. Here's a comprehensive guide to common directories and their intended purposes.

### Core System Directories

#### `/var` - Variable Data

**Purpose**: Files that are expected to change during normal system operation.

**Common subdirectories**:

- `/var/log` - System and application log files
- `/var/lib` - Variable state information (databases, package manager data)
- `/var/cache` - Application cache files
- `/var/spool` - Spool directories (mail, print queues)
- `/var/tmp` - Temporary files that persist across reboots
- `/var/www` - Web server document root (common but not standard)
- `/var/run` - Runtime variable files (process IDs, sockets)
- `/var/backups` - System and application backups

**Best practices**:

- Use `/var/log` for all application logs with proper subdirectories
- Store application data in `/var/lib/[application-name]`
- Implement log rotation for files in `/var/log`
- Monitor disk usage as `/var` can grow rapidly
- Use `/var/backups` for system backup files
- Separate different types of variable data into appropriate subdirectories

**Docker integration**: When mounting Docker volumes, follow these patterns:

- Database data: `/var/lib/[database-name]/`
- Application data: `/var/lib/[app-name]/`
- Container logs: `/var/log/[service-name]/`

#### `/opt` - Optional Software

**Purpose**: Add-on application software packages that are not part of the default installation.

**Structure**:

- `/opt/[package]` - Self-contained software packages
- `/opt/[vendor]` - Vendor-specific software collections

**Best practices**:

- Install third-party commercial software in `/opt`
- Each package should be self-contained in its own subdirectory
- Include version numbers in directory names for multiple versions
- Store configuration in `/etc/opt/[package]` if system-wide
- Use symbolic links for current versions: `/opt/app -> /opt/app-1.2.3`

**Docker integration**:

- Mount `/opt/[application]` for self-contained applications
- Use for applications that don't fit standard Linux directory patterns

#### `/usr` - User Binaries and Data

**Purpose**: Read-only user data; the majority of (multi-)user utilities and applications.

**Key subdirectories**:

- `/usr/bin` - User command binaries
- `/usr/local` - Local hierarchy (locally installed software)
- `/usr/share` - Architecture-independent data
- `/usr/lib` - Libraries for binaries in `/usr/bin`
- `/usr/src` - Source code

**Best practices**:

- Use `/usr/local` for locally compiled software
- Never modify files directly in `/usr` (except `/usr/local`)
- Store shared application data in `/usr/share/[application]`
- Follow the same hierarchy under `/usr/local` as under `/usr`

**Docker integration**:

- Mount `/usr/local` for custom binaries and libraries
- Use `/usr/share/[app]` for read-only application assets

#### `/etc` - Configuration Files

**Purpose**: System-wide configuration files and shell scripts.

**Structure**:

- `/etc/[application]` - Application-specific configuration
- `/etc/systemd/system` - Custom systemd service files
- `/etc/nginx` - NGINX configuration
- `/etc/ssl` - SSL certificates and keys
- `/etc/opt/[package]` - Configuration for `/opt` packages

**Best practices**:

- Keep configuration files version controlled
- Use descriptive names for custom configuration files
- Implement proper file permissions (644 for configs, 600 for secrets)
- Create backups before modifying system configurations
- Use `.conf` extension for configuration files
- Group related configurations in subdirectories

**Docker integration**:

- Mount configuration directories as read-only when possible
- Use `/etc/[service-name]` for service-specific configurations
- Store secrets in `/etc/ssl/private` with restricted permissions

#### `/home` - User Home Directories

**Purpose**: User personal directories and files.

**Structure**:

- `/home/[username]` - Individual user directories
- `/home/[username]/.config` - User-specific configuration files
- `/home/[username]/.local` - User-specific data and binaries

**Best practices**:

- Each user gets `/home/[username]`
- Store user-specific application data in `~/.config` or `~/.local`
- Implement regular backups of important user data
- Set appropriate quotas to prevent disk space abuse
- Use proper permissions (700 for home directories)

#### `/tmp` - Temporary Files

**Purpose**: Temporary files that may be deleted between reboots.

**Best practices**:

- Files may be automatically cleaned by the system
- Use `/var/tmp` for temporary files that should persist across reboots
- Implement proper cleanup mechanisms in applications
- Monitor disk usage to prevent system issues
- Use `tmpfs` for high-performance temporary storage

#### `/srv` - Service Data

**Purpose**: Data for services provided by the system.

**Structure**:

- `/srv/www` - Web server data
- `/srv/ftp` - FTP server data
- `/srv/[service]` - Service-specific data

**Best practices**:

- Use for data served by system services
- Alternative to `/var/www` for web content
- Organize by service type and purpose

### Linux Directory Organization for Healthcare SaaS

Based on your healthcare SaaS architecture, here's the recommended directory structure:

#### System Configuration (`/etc`)

```
/etc/
├── nginx/
│   ├── sites-available/
│   ├── sites-enabled/
│   └── conf.d/
├── keycloak/
│   ├── keycloak.conf
│   └── themes/
├── ssl/
│   ├── certs/
│   └── private/
├── mysql/
│   └── conf.d/
└── opt/
    └── healthcare-saas/
        ├── api.conf
        └── frontend.conf
```

#### Variable Data (`/var`)

```
/var/
├── lib/
│   ├── mysql/
│   │   ├── primary/
│   │   └── replica/
│   ├── keycloak/
│   ├── redis/
│   └── healthcare-saas/
│       ├── uploads/
│       ├── cache/
│       └── sessions/
├── log/
│   ├── nginx/
│   ├── mysql/
│   ├── keycloak/
│   └── healthcare-saas/
│       ├── api/
│       ├── auth/
│       └── audit/
└── backups/
    ├── databases/
    ├── configurations/
    └── volumes/
```

#### Optional Software (`/opt`)

```
/opt/
├── keycloak/
│   ├── bin/
│   ├── conf/
│   └── themes/
├── healthcare-saas/
│   ├── api/
│   ├── frontend/
│   └── scripts/
└── monitoring/
    ├── prometheus/
    └── grafana/
```

#### Web Content (`/var/www` or `/srv/www`)

```
/var/www/
├── html/
│   └── maintenance/
├── healthcare-saas/
│   ├── api/
│   ├── frontend/
│   │   ├── appointment/
│   │   ├── portal/
│   │   ├── directory/
│   │   └── admin/
│   └── shared/
│       ├── assets/
│       └── themes/
```

## Security and Permissions Best Practices

### File System Permissions

- Use principle of least privilege for all directories
- Implement proper SELinux/AppArmor policies if enabled
- Regular security audits of file permissions
- Encrypt sensitive data directories

### Standard Permission Patterns

```bash
# Configuration files
chmod 644 /etc/application/config.conf
chmod 600 /etc/application/secrets.conf

# Directories
chmod 755 /var/lib/application/
chmod 750 /var/log/application/
chmod 700 /home/user/

# SSL certificates
chmod 644 /etc/ssl/certs/*
chmod 600 /etc/ssl/private/*

# Application data
chmod 755 /opt/application/
chmod 644 /opt/application/config/*
```

### Ownership Patterns

```bash
# System services
chown root:root /etc/nginx/
chown mysql:mysql /var/lib/mysql/
chown redis:redis /var/lib/redis/

# Application-specific
chown www-data:www-data /var/www/
chown application:application /opt/application/
```

## Monitoring and Maintenance

### Disk Usage Monitoring

```bash
# Monitor critical directories
df -h /var/lib/
df -h /var/log/
df -h /opt/
du -sh /var/lib/*/
du -sh /var/log/*/
```

### Log Rotation

```bash
# /etc/logrotate.d/healthcare-saas
/var/log/healthcare-saas/* {
    daily
    rotate 30
    compress
    delaycompress
    missingok
    notifempty
    create 644 healthcare-saas healthcare-saas
}
```

### Cleanup Scripts

```bash
#!/bin/bash
# Cleanup temporary files
find /tmp -type f -atime +7 -delete
find /var/tmp -type f -atime +30 -delete

# Clean application caches
find /var/lib/healthcare-saas/cache -type f -mtime +1 -delete
```

## Conclusion

Proper Linux filesystem organization is fundamental to maintaining a secure, scalable, and maintainable healthcare SaaS platform. Following the Filesystem Hierarchy Standard while adapting it to your specific application needs ensures consistency, security, and operational efficiency. This foundation supports both traditional deployments and containerized environments while maintaining clear separation of concerns across configuration, data, logs, and application components.