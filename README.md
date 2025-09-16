# akaunting Installation Guide

akaunting is a free and open-source accounting software. Akaunting provides free accounting software for small businesses

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
  - RAM: 1GB minimum
  - Storage: 2GB for data
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default akaunting port)
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

# Install akaunting
sudo dnf install -y akaunting

# Enable and start service
sudo systemctl enable --now akaunting

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
akaunting --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install akaunting
sudo apt install -y akaunting

# Enable and start service
sudo systemctl enable --now akaunting

# Configure firewall
sudo ufw allow 80

# Verify installation
akaunting --version
```

### Arch Linux

```bash
# Install akaunting
sudo pacman -S akaunting

# Enable and start service
sudo systemctl enable --now akaunting

# Verify installation
akaunting --version
```

### Alpine Linux

```bash
# Install akaunting
apk add --no-cache akaunting

# Enable and start service
rc-update add akaunting default
rc-service akaunting start

# Verify installation
akaunting --version
```

### openSUSE/SLES

```bash
# Install akaunting
sudo zypper install -y akaunting

# Enable and start service
sudo systemctl enable --now akaunting

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
akaunting --version
```

### macOS

```bash
# Using Homebrew
brew install akaunting

# Start service
brew services start akaunting

# Verify installation
akaunting --version
```

### FreeBSD

```bash
# Using pkg
pkg install akaunting

# Enable in rc.conf
echo 'akaunting_enable="YES"' >> /etc/rc.conf

# Start service
service akaunting start

# Verify installation
akaunting --version
```

### Windows

```bash
# Using Chocolatey
choco install akaunting

# Or using Scoop
scoop install akaunting

# Verify installation
akaunting --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/akaunting

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
akaunting --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable akaunting

# Start service
sudo systemctl start akaunting

# Stop service
sudo systemctl stop akaunting

# Restart service
sudo systemctl restart akaunting

# Check status
sudo systemctl status akaunting

# View logs
sudo journalctl -u akaunting -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add akaunting default

# Start service
rc-service akaunting start

# Stop service
rc-service akaunting stop

# Restart service
rc-service akaunting restart

# Check status
rc-service akaunting status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'akaunting_enable="YES"' >> /etc/rc.conf

# Start service
service akaunting start

# Stop service
service akaunting stop

# Restart service
service akaunting restart

# Check status
service akaunting status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start akaunting
brew services stop akaunting
brew services restart akaunting

# Check status
brew services list | grep akaunting
```

### Windows Service Manager

```powershell
# Start service
net start akaunting

# Stop service
net stop akaunting

# Using PowerShell
Start-Service akaunting
Stop-Service akaunting
Restart-Service akaunting

# Check status
Get-Service akaunting
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream akaunting_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name akaunting.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name akaunting.example.com;

    ssl_certificate /etc/ssl/certs/akaunting.example.com.crt;
    ssl_certificate_key /etc/ssl/private/akaunting.example.com.key;

    location / {
        proxy_pass http://akaunting_backend;
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
    ServerName akaunting.example.com
    Redirect permanent / https://akaunting.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName akaunting.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/akaunting.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/akaunting.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend akaunting_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/akaunting.pem
    redirect scheme https if !{ ssl_fc }
    default_backend akaunting_backend

backend akaunting_backend
    balance roundrobin
    server akaunting1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R akaunting:akaunting /etc/akaunting
sudo chmod 750 /etc/akaunting

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
sudo systemctl status akaunting

# View logs
sudo journalctl -u akaunting -f

# Monitor resource usage
top -p $(pgrep akaunting)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/akaunting"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/akaunting-backup-$DATE.tar.gz" /etc/akaunting /var/lib/akaunting

echo "Backup completed: $BACKUP_DIR/akaunting-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop akaunting

# Restore from backup
tar -xzf /backup/akaunting/akaunting-backup-*.tar.gz -C /

# Start service
sudo systemctl start akaunting
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u akaunting -n 100
sudo tail -f /var/log/akaunting/akaunting.log

# Check configuration
akaunting --version

# Check permissions
ls -la /etc/akaunting
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
top -p $(pgrep akaunting)

# Check disk I/O
iotop -p $(pgrep akaunting)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  akaunting:
    image: akaunting:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/akaunting
      - ./data:/var/lib/akaunting
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update akaunting

# Debian/Ubuntu
sudo apt update && sudo apt upgrade akaunting

# Arch Linux
sudo pacman -Syu akaunting

# Alpine Linux
apk update && apk upgrade akaunting

# openSUSE
sudo zypper update akaunting

# FreeBSD
pkg update && pkg upgrade akaunting

# Always backup before updates
tar -czf /backup/akaunting-pre-update-$(date +%Y%m%d).tar.gz /etc/akaunting

# Restart after updates
sudo systemctl restart akaunting
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/akaunting

# Clean old logs
find /var/log/akaunting -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/akaunting
```

## Additional Resources

- Official Documentation: https://docs.akaunting.org/
- GitHub Repository: https://github.com/akaunting/akaunting
- Community Forum: https://forum.akaunting.org/
- Best Practices Guide: https://docs.akaunting.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
