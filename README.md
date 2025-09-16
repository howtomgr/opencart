# opencart Installation Guide

opencart is a free and open-source online store platform. OpenCart provides easy-to-use e-commerce platform

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
  - CPU: 2+ cores
  - RAM: 2GB minimum
  - Storage: 5GB for data
  - Network: HTTP/HTTPS
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default opencart port)
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

# Install opencart
sudo dnf install -y opencart

# Enable and start service
sudo systemctl enable --now opencart

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
opencart --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install opencart
sudo apt install -y opencart

# Enable and start service
sudo systemctl enable --now opencart

# Configure firewall
sudo ufw allow 80

# Verify installation
opencart --version
```

### Arch Linux

```bash
# Install opencart
sudo pacman -S opencart

# Enable and start service
sudo systemctl enable --now opencart

# Verify installation
opencart --version
```

### Alpine Linux

```bash
# Install opencart
apk add --no-cache opencart

# Enable and start service
rc-update add opencart default
rc-service opencart start

# Verify installation
opencart --version
```

### openSUSE/SLES

```bash
# Install opencart
sudo zypper install -y opencart

# Enable and start service
sudo systemctl enable --now opencart

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
opencart --version
```

### macOS

```bash
# Using Homebrew
brew install opencart

# Start service
brew services start opencart

# Verify installation
opencart --version
```

### FreeBSD

```bash
# Using pkg
pkg install opencart

# Enable in rc.conf
echo 'opencart_enable="YES"' >> /etc/rc.conf

# Start service
service opencart start

# Verify installation
opencart --version
```

### Windows

```bash
# Using Chocolatey
choco install opencart

# Or using Scoop
scoop install opencart

# Verify installation
opencart --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/opencart

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
opencart --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable opencart

# Start service
sudo systemctl start opencart

# Stop service
sudo systemctl stop opencart

# Restart service
sudo systemctl restart opencart

# Check status
sudo systemctl status opencart

# View logs
sudo journalctl -u opencart -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add opencart default

# Start service
rc-service opencart start

# Stop service
rc-service opencart stop

# Restart service
rc-service opencart restart

# Check status
rc-service opencart status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'opencart_enable="YES"' >> /etc/rc.conf

# Start service
service opencart start

# Stop service
service opencart stop

# Restart service
service opencart restart

# Check status
service opencart status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start opencart
brew services stop opencart
brew services restart opencart

# Check status
brew services list | grep opencart
```

### Windows Service Manager

```powershell
# Start service
net start opencart

# Stop service
net stop opencart

# Using PowerShell
Start-Service opencart
Stop-Service opencart
Restart-Service opencart

# Check status
Get-Service opencart
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream opencart_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name opencart.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name opencart.example.com;

    ssl_certificate /etc/ssl/certs/opencart.example.com.crt;
    ssl_certificate_key /etc/ssl/private/opencart.example.com.key;

    location / {
        proxy_pass http://opencart_backend;
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
    ServerName opencart.example.com
    Redirect permanent / https://opencart.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName opencart.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/opencart.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/opencart.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend opencart_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/opencart.pem
    redirect scheme https if !{ ssl_fc }
    default_backend opencart_backend

backend opencart_backend
    balance roundrobin
    server opencart1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R opencart:opencart /etc/opencart
sudo chmod 750 /etc/opencart

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
sudo systemctl status opencart

# View logs
sudo journalctl -u opencart -f

# Monitor resource usage
top -p $(pgrep opencart)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/opencart"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/opencart-backup-$DATE.tar.gz" /etc/opencart /var/lib/opencart

echo "Backup completed: $BACKUP_DIR/opencart-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop opencart

# Restore from backup
tar -xzf /backup/opencart/opencart-backup-*.tar.gz -C /

# Start service
sudo systemctl start opencart
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u opencart -n 100
sudo tail -f /var/log/opencart/opencart.log

# Check configuration
opencart --version

# Check permissions
ls -la /etc/opencart
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
top -p $(pgrep opencart)

# Check disk I/O
iotop -p $(pgrep opencart)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  opencart:
    image: opencart:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/opencart
      - ./data:/var/lib/opencart
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update opencart

# Debian/Ubuntu
sudo apt update && sudo apt upgrade opencart

# Arch Linux
sudo pacman -Syu opencart

# Alpine Linux
apk update && apk upgrade opencart

# openSUSE
sudo zypper update opencart

# FreeBSD
pkg update && pkg upgrade opencart

# Always backup before updates
tar -czf /backup/opencart-pre-update-$(date +%Y%m%d).tar.gz /etc/opencart

# Restart after updates
sudo systemctl restart opencart
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/opencart

# Clean old logs
find /var/log/opencart -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/opencart
```

## Additional Resources

- Official Documentation: https://docs.opencart.org/
- GitHub Repository: https://github.com/opencart/opencart
- Community Forum: https://forum.opencart.org/
- Best Practices Guide: https://docs.opencart.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
