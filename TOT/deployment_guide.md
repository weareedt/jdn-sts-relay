# Deployment Guide - DigitalOcean Ubuntu VPS

This guide details the process of deploying the JDN STS Relay server on a DigitalOcean Ubuntu VPS using PM2 for process management.

## 1. DigitalOcean Droplet Setup

### Create Droplet
1. Log in to DigitalOcean
2. Click "Create Droplet"
3. Choose configuration:
   - Ubuntu 22.04 LTS
   - Basic plan (minimum 1GB RAM)
   - Choose datacenter region
   - Add SSH key for secure access
   - Choose hostname (e.g., jdn-sts-relay)

### Initial Server Configuration
```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Set timezone
sudo timedatectl set-timezone Asia/Kuala_Lumpur

# Create non-root user (optional but recommended)
sudo adduser deployer
sudo usermod -aG sudo deployer
```

## 2. Environment Setup

### Install Node.js
```bash
# Add NodeSource repository
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -

# Install Node.js and npm
sudo apt install -y nodejs

# Verify installation
node --version
npm --version
```

### Install PM2
```bash
# Install PM2 globally
sudo npm install -y pm2@latest -g

# Verify installation
pm2 --version
```

## 3. Project Deployment

### Setup Project Directory
```bash
# Create application directory
sudo mkdir -p /var/www/jdn-sts-relay
sudo chown -R $USER:$USER /var/www/jdn-sts-relay

# Navigate to directory
cd /var/www/jdn-sts-relay

# Clone repository
git clone https://github.com/your-repo/jdn-sts-relay.git .

# Install dependencies
npm install --production
```

### Configure Environment
```bash
# Create .env file
nano .env

# Add required environment variables
OPENAI_API_KEY=your_openai_api_key
WS_PORT=8081
```

### PM2 Configuration
Create ecosystem.config.js:
```javascript
module.exports = {
  apps: [{
    name: 'jdn-sts-relay',
    script: 'index.js',
    instances: 1,
    autorestart: true,
    watch: false,
    max_memory_restart: '1G',
    env: {
      NODE_ENV: 'production',
      OPENAI_API_KEY: 'your_openai_api_key',
      WS_PORT: 8081
    }
  }]
};
```

## 4. Application Launch

### Start Application
```bash
# Start with PM2
pm2 start ecosystem.config.js

# Save PM2 process list
pm2 save

# Setup PM2 to start on system boot
pm2 startup systemd
```

### Verify Deployment
```bash
# Check application status
pm2 status

# Monitor application
pm2 monit

# Check logs
pm2 logs jdn-sts-relay
```

## 5. Security Setup

### Configure Firewall (UFW)
```bash
# Install UFW if not installed
sudo apt install ufw

# Allow SSH (important!)
sudo ufw allow 22/tcp

# Allow application ports
sudo ufw allow 8081/tcp  # WebSocket
sudo ufw allow 3001/tcp  # HTTP proxy

# Enable firewall
sudo ufw enable

# Check status
sudo ufw status
```

### SSL Setup (Optional)
If you need HTTPS:
```bash
# Install Certbot
sudo apt install certbot

# Obtain certificate
sudo certbot certonly --standalone -d your-domain.com

# Configure SSL in your application
```

## 6. Monitoring and Maintenance

### PM2 Commands
```bash
# View application status
pm2 status

# Monitor CPU/Memory
pm2 monit

# View logs
pm2 logs jdn-sts-relay

# Restart application
pm2 restart jdn-sts-relay

# Stop application
pm2 stop jdn-sts-relay

# Remove from PM2
pm2 delete jdn-sts-relay
```

### System Monitoring
```bash
# Monitor system resources
htop

# Check disk usage
df -h

# Monitor network connections
netstat -tulpn
```

## 7. Troubleshooting

### Common Issues

1. **Connection Refused**
   - Check if application is running: `pm2 status`
   - Verify ports are open: `sudo ufw status`
   - Check logs: `pm2 logs jdn-sts-relay`

2. **Application Crash**
   - Check error logs: `pm2 logs jdn-sts-relay --err`
   - Verify environment variables
   - Check system resources: `htop`

3. **High Memory Usage**
   - Monitor resources: `pm2 monit`
   - Check max memory setting in ecosystem.config.js
   - Consider upgrading droplet if needed

### Log Management
```bash
# View real-time logs
pm2 logs jdn-sts-relay

# View error logs only
pm2 logs jdn-sts-relay --err

# Clear logs
pm2 flush

# Rotate logs
pm2 install pm2-logrotate
```

## 8. Backup and Recovery

### Backup Strategy
1. Environment Configuration
   ```bash
   # Backup .env and ecosystem config
   cp .env .env.backup
   cp ecosystem.config.js ecosystem.config.js.backup
   ```

2. Application Data
   ```bash
   # Create backup directory
   mkdir -p /var/backups/jdn-sts-relay

   # Backup application files
   tar -czf /var/backups/jdn-sts-relay/backup-$(date +%Y%m%d).tar.gz /var/www/jdn-sts-relay
   ```

### Recovery Steps
1. Restore from backup
2. Verify environment configuration
3. Reinstall dependencies
4. Restart PM2 process
