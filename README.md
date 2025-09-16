# ubooquity Installation Guide

ubooquity is a free and open-source comics and ebooks server. Ubooquity serves comics and ebooks with OPDS support for various readers

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
  - RAM: 512MB minimum
  - Storage: 1GB for app
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 2202 (default ubooquity port)
  - Admin on 2203
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

# Install ubooquity
sudo dnf install -y ubooquity

# Enable and start service
sudo systemctl enable --now ubooquity

# Configure firewall
sudo firewall-cmd --permanent --add-port=2202/tcp
sudo firewall-cmd --reload

# Verify installation
ubooquity --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install ubooquity
sudo apt install -y ubooquity

# Enable and start service
sudo systemctl enable --now ubooquity

# Configure firewall
sudo ufw allow 2202

# Verify installation
ubooquity --version
```

### Arch Linux

```bash
# Install ubooquity
sudo pacman -S ubooquity

# Enable and start service
sudo systemctl enable --now ubooquity

# Verify installation
ubooquity --version
```

### Alpine Linux

```bash
# Install ubooquity
apk add --no-cache ubooquity

# Enable and start service
rc-update add ubooquity default
rc-service ubooquity start

# Verify installation
ubooquity --version
```

### openSUSE/SLES

```bash
# Install ubooquity
sudo zypper install -y ubooquity

# Enable and start service
sudo systemctl enable --now ubooquity

# Configure firewall
sudo firewall-cmd --permanent --add-port=2202/tcp
sudo firewall-cmd --reload

# Verify installation
ubooquity --version
```

### macOS

```bash
# Using Homebrew
brew install ubooquity

# Start service
brew services start ubooquity

# Verify installation
ubooquity --version
```

### FreeBSD

```bash
# Using pkg
pkg install ubooquity

# Enable in rc.conf
echo 'ubooquity_enable="YES"' >> /etc/rc.conf

# Start service
service ubooquity start

# Verify installation
ubooquity --version
```

### Windows

```bash
# Using Chocolatey
choco install ubooquity

# Or using Scoop
scoop install ubooquity

# Verify installation
ubooquity --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/ubooquity

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
ubooquity --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable ubooquity

# Start service
sudo systemctl start ubooquity

# Stop service
sudo systemctl stop ubooquity

# Restart service
sudo systemctl restart ubooquity

# Check status
sudo systemctl status ubooquity

# View logs
sudo journalctl -u ubooquity -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add ubooquity default

# Start service
rc-service ubooquity start

# Stop service
rc-service ubooquity stop

# Restart service
rc-service ubooquity restart

# Check status
rc-service ubooquity status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'ubooquity_enable="YES"' >> /etc/rc.conf

# Start service
service ubooquity start

# Stop service
service ubooquity stop

# Restart service
service ubooquity restart

# Check status
service ubooquity status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start ubooquity
brew services stop ubooquity
brew services restart ubooquity

# Check status
brew services list | grep ubooquity
```

### Windows Service Manager

```powershell
# Start service
net start ubooquity

# Stop service
net stop ubooquity

# Using PowerShell
Start-Service ubooquity
Stop-Service ubooquity
Restart-Service ubooquity

# Check status
Get-Service ubooquity
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream ubooquity_backend {
    server 127.0.0.1:2202;
}

server {
    listen 80;
    server_name ubooquity.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name ubooquity.example.com;

    ssl_certificate /etc/ssl/certs/ubooquity.example.com.crt;
    ssl_certificate_key /etc/ssl/private/ubooquity.example.com.key;

    location / {
        proxy_pass http://ubooquity_backend;
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
    ServerName ubooquity.example.com
    Redirect permanent / https://ubooquity.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName ubooquity.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/ubooquity.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/ubooquity.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:2202/
    ProxyPassReverse / http://127.0.0.1:2202/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend ubooquity_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/ubooquity.pem
    redirect scheme https if !{ ssl_fc }
    default_backend ubooquity_backend

backend ubooquity_backend
    balance roundrobin
    server ubooquity1 127.0.0.1:2202 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R ubooquity:ubooquity /etc/ubooquity
sudo chmod 750 /etc/ubooquity

# Configure firewall
sudo firewall-cmd --permanent --add-port=2202/tcp
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
sudo systemctl status ubooquity

# View logs
sudo journalctl -u ubooquity -f

# Monitor resource usage
top -p $(pgrep ubooquity)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/ubooquity"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/ubooquity-backup-$DATE.tar.gz" /etc/ubooquity /var/lib/ubooquity

echo "Backup completed: $BACKUP_DIR/ubooquity-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop ubooquity

# Restore from backup
tar -xzf /backup/ubooquity/ubooquity-backup-*.tar.gz -C /

# Start service
sudo systemctl start ubooquity
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u ubooquity -n 100
sudo tail -f /var/log/ubooquity/ubooquity.log

# Check configuration
ubooquity --version

# Check permissions
ls -la /etc/ubooquity
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 2202

# Test connectivity
telnet localhost 2202

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep ubooquity)

# Check disk I/O
iotop -p $(pgrep ubooquity)

# Check connections
ss -an | grep 2202
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  ubooquity:
    image: ubooquity:latest
    ports:
      - "2202:2202"
    volumes:
      - ./config:/etc/ubooquity
      - ./data:/var/lib/ubooquity
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update ubooquity

# Debian/Ubuntu
sudo apt update && sudo apt upgrade ubooquity

# Arch Linux
sudo pacman -Syu ubooquity

# Alpine Linux
apk update && apk upgrade ubooquity

# openSUSE
sudo zypper update ubooquity

# FreeBSD
pkg update && pkg upgrade ubooquity

# Always backup before updates
tar -czf /backup/ubooquity-pre-update-$(date +%Y%m%d).tar.gz /etc/ubooquity

# Restart after updates
sudo systemctl restart ubooquity
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/ubooquity

# Clean old logs
find /var/log/ubooquity -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/ubooquity
```

## Additional Resources

- Official Documentation: https://docs.ubooquity.org/
- GitHub Repository: https://github.com/ubooquity/ubooquity
- Community Forum: https://forum.ubooquity.org/
- Best Practices Guide: https://docs.ubooquity.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
