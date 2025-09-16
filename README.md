# filebrowser Installation Guide

filebrowser is a free and open-source web file manager. File Browser provides a web interface for managing files with sharing capabilities

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 128MB minimum
  - Storage: 50MB for app
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default filebrowser port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install filebrowser
sudo dnf install -y filebrowser

# Enable and start service
sudo systemctl enable --now filebrowser

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
filebrowser --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install filebrowser
sudo apt install -y filebrowser

# Enable and start service
sudo systemctl enable --now filebrowser

# Configure firewall
sudo ufw allow 80

# Verify installation
filebrowser --version
```

### Arch Linux

```bash
# Install filebrowser
sudo pacman -S filebrowser

# Enable and start service
sudo systemctl enable --now filebrowser

# Verify installation
filebrowser --version
```

### Alpine Linux

```bash
# Install filebrowser
apk add --no-cache filebrowser

# Enable and start service
rc-update add filebrowser default
rc-service filebrowser start

# Verify installation
filebrowser --version
```

### openSUSE/SLES

```bash
# Install filebrowser
sudo zypper install -y filebrowser

# Enable and start service
sudo systemctl enable --now filebrowser

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
filebrowser --version
```

### macOS

```bash
# Using Homebrew
brew install filebrowser

# Start service
brew services start filebrowser

# Verify installation
filebrowser --version
```

### FreeBSD

```bash
# Using pkg
pkg install filebrowser

# Enable in rc.conf
echo 'filebrowser_enable="YES"' >> /etc/rc.conf

# Start service
service filebrowser start

# Verify installation
filebrowser --version
```

### Windows

```bash
# Using Chocolatey
choco install filebrowser

# Or using Scoop
scoop install filebrowser

# Verify installation
filebrowser --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/filebrowser

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
filebrowser --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable filebrowser

# Start service
sudo systemctl start filebrowser

# Stop service
sudo systemctl stop filebrowser

# Restart service
sudo systemctl restart filebrowser

# Check status
sudo systemctl status filebrowser

# View logs
sudo journalctl -u filebrowser -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add filebrowser default

# Start service
rc-service filebrowser start

# Stop service
rc-service filebrowser stop

# Restart service
rc-service filebrowser restart

# Check status
rc-service filebrowser status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'filebrowser_enable="YES"' >> /etc/rc.conf

# Start service
service filebrowser start

# Stop service
service filebrowser stop

# Restart service
service filebrowser restart

# Check status
service filebrowser status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start filebrowser
brew services stop filebrowser
brew services restart filebrowser

# Check status
brew services list | grep filebrowser
```

### Windows Service Manager

```powershell
# Start service
net start filebrowser

# Stop service
net stop filebrowser

# Using PowerShell
Start-Service filebrowser
Stop-Service filebrowser
Restart-Service filebrowser

# Check status
Get-Service filebrowser
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream filebrowser_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name filebrowser.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name filebrowser.example.com;

    ssl_certificate /etc/ssl/certs/filebrowser.example.com.crt;
    ssl_certificate_key /etc/ssl/private/filebrowser.example.com.key;

    location / {
        proxy_pass http://filebrowser_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName filebrowser.example.com
    Redirect permanent / https://filebrowser.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName filebrowser.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/filebrowser.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/filebrowser.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend filebrowser_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/filebrowser.pem
    redirect scheme https if !{ ssl_fc }
    default_backend filebrowser_backend

backend filebrowser_backend
    balance roundrobin
    server filebrowser1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R filebrowser:filebrowser /etc/filebrowser
sudo chmod 750 /etc/filebrowser

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status filebrowser

# View logs
sudo journalctl -u filebrowser -f

# Monitor resource usage
top -p $(pgrep filebrowser)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/filebrowser"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/filebrowser-backup-$DATE.tar.gz" /etc/filebrowser /var/lib/filebrowser

echo "Backup completed: $BACKUP_DIR/filebrowser-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop filebrowser

# Restore from backup
tar -xzf /backup/filebrowser/filebrowser-backup-*.tar.gz -C /

# Start service
sudo systemctl start filebrowser
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u filebrowser -n 100
sudo tail -f /var/log/filebrowser/filebrowser.log

# Check configuration
filebrowser --version

# Check permissions
ls -la /etc/filebrowser
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep filebrowser)

# Check disk I/O
iotop -p $(pgrep filebrowser)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  filebrowser:
    image: filebrowser:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/filebrowser
      - ./data:/var/lib/filebrowser
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update filebrowser

# Debian/Ubuntu
sudo apt update && sudo apt upgrade filebrowser

# Arch Linux
sudo pacman -Syu filebrowser

# Alpine Linux
apk update && apk upgrade filebrowser

# openSUSE
sudo zypper update filebrowser

# FreeBSD
pkg update && pkg upgrade filebrowser

# Always backup before updates
tar -czf /backup/filebrowser-pre-update-$(date +%Y%m%d).tar.gz /etc/filebrowser

# Restart after updates
sudo systemctl restart filebrowser
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/filebrowser

# Clean old logs
find /var/log/filebrowser -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/filebrowser
```

## Additional Resources

- Official Documentation: https://docs.filebrowser.org/
- GitHub Repository: https://github.com/filebrowser/filebrowser
- Community Forum: https://forum.filebrowser.org/
- Best Practices Guide: https://docs.filebrowser.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
