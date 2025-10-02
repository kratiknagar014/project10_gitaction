# 🚀 Complete Deployment Guide - Step by Step

## चरण 1: Prerequisites Setup (पहले ये सब setup करें)

### 1.1 DockerHub Account
```bash
# 1. https://hub.docker.com पर account बनाएं
# 2. दो repositories बनाएं:
#    - your-username/ors-backend
#    - your-username/ors-frontend
```

### 1.2 DigitalOcean Account
```bash
# 1. https://digitalocean.com पर account बनाएं
# 2. SSH key generate करें:
ssh-keygen -t rsa -b 4096 -c "your-email@example.com"
# 3. Public key को DigitalOcean में add करें
```

### 1.3 Gmail App Password
```bash
# 1. Gmail में 2-factor authentication enable करें
# 2. App password generate करें:
#    - Google Account Settings > Security > App passwords
#    - Select "Mail" और device name enter करें
```

---

## चरण 2: Local Testing (पहले local में test करें)

### 2.1 Environment Setup
```bash
# Repository clone करें
git clone <your-repo-url>
cd gitAction

# Environment file बनाएं
cp .env.example .env

# .env file edit करें:
nano .env
```

### 2.2 Local Build और Test
```bash
# सभी services start करें
docker-compose up -d

# Status check करें
docker-compose ps

# Logs देखें
docker-compose logs -f

# Test करें:
# Frontend: http://localhost
# Backend: http://localhost:8084/actuator/health
```

### 2.3 Local Cleanup
```bash
# Services stop करें
docker-compose down

# Images clean करें (optional)
docker system prune -f
```

---

## चरण 3: DigitalOcean Droplet Setup

### 3.1 Droplet Create करें
```bash
# DigitalOcean dashboard में:
# 1. Create Droplet
# 2. Ubuntu 20.04 LTS select करें
# 3. Minimum 2GB RAM (4GB recommended)
# 4. Add SSH key
# 5. Create droplet
```

### 3.2 Droplet Setup
```bash
# SSH से connect करें
ssh root@your-droplet-ip

# Setup script copy करें
# Local machine से:
scp deployment/setup-droplet.sh root@your-droplet-ip:/tmp/

# Droplet में script run करें:
chmod +x /tmp/setup-droplet.sh
/tmp/setup-droplet.sh

# Reboot करें
reboot
```

### 3.3 Verify Setup
```bash
# SSH reconnect करें
ssh root@your-droplet-ip

# Docker check करें
docker --version
docker-compose --version

# Directory check करें
ls -la /opt/ors-app/
```

---

## चरण 4: GitHub Repository Setup

### 4.1 Repository Secrets Add करें
```bash
# GitHub repository में जाएं
# Settings > Secrets and variables > Actions

# ये secrets add करें:
DOCKER_USERNAME=your_dockerhub_username
DOCKER_PASSWORD=your_dockerhub_password
DO_DROPLET_IP=your_droplet_ip
DO_SSH_PRIVATE_KEY=your_private_ssh_key_content
DB_PASSWORD=your_secure_db_password
JWT_SECRET=your_jwt_secret_key_minimum_32_chars
MAIL_USERNAME=your_email@gmail.com
MAIL_PASSWORD=your_gmail_app_password
```

### 4.2 SSH Private Key Setup
```bash
# Local machine में:
cat ~/.ssh/id_rsa

# Output को copy करें और DO_SSH_PRIVATE_KEY में paste करें
# ध्यान दें: पूरी key copy करें including:
# -----BEGIN OPENSSH PRIVATE KEY-----
# ... key content ...
# -----END OPENSSH PRIVATE KEY-----
```

---

## चरण 5: Production Environment Setup

### 5.1 Production Environment File
```bash
# Droplet में environment file बनाएं
ssh root@your-droplet-ip
cd /opt/ors-app

# Environment file बनाएं
cat > .env << EOF
DB_PASSWORD=your_secure_db_password
JWT_SECRET=your_jwt_secret_key_minimum_32_chars
MAIL_USERNAME=your_email@gmail.com
MAIL_PASSWORD=your_gmail_app_password
DOCKER_USERNAME=your_dockerhub_username
EOF

# Permissions set करें
chmod 600 .env
```

### 5.2 Production Docker Compose
```bash
# Production compose file copy करें
# Local machine से:
scp docker-compose.prod.yml root@your-droplet-ip:/opt/ors-app/docker-compose.yml

# Database init script copy करें
scp deployment/init.sql root@your-droplet-ip:/opt/ors-app/deployment/
```

---

## चरण 6: First Deployment

### 6.1 Manual Deployment Test
```bash
# Code को GitHub में push करें
git add .
git commit -m "Initial deployment setup"
git push origin main

# GitHub Actions check करें:
# Repository > Actions tab में जाकर workflow status देखें
```

### 6.2 Deployment Verification
```bash
# Droplet में check करें
ssh root@your-droplet-ip
cd /opt/ors-app

# Containers status
docker-compose ps

# Application health
curl http://localhost:8084/actuator/health
curl http://localhost/

# Logs check करें
docker-compose logs -f
```

---

## चरण 7: Domain Setup (Optional)

### 7.1 Domain Configuration
```bash
# अगर आपका domain है तो:
# 1. Domain के DNS में A record add करें:
#    Type: A
#    Name: @ (या www)
#    Value: your-droplet-ip

# 2. SSL certificate setup करें:
ssh root@your-droplet-ip

# Certbot install करें
apt install certbot python3-certbot-nginx -y

# SSL certificate generate करें
certbot --nginx -d your-domain.com
```

### 7.2 Nginx Configuration Update
```bash
# Production nginx config के लिए:
# Frontend Dockerfile में nginx.conf को update करें
# SSL और domain के लिए proper configuration add करें
```

---

## चरण 8: Monitoring Setup

### 8.1 Basic Monitoring
```bash
# Droplet में monitoring script run करें
ssh root@your-droplet-ip
/opt/ors-app/monitor.sh

# Automated monitoring setup करें
# Crontab में add करें:
crontab -e

# Add this line:
*/5 * * * * /opt/ors-app/monitor.sh >> /opt/ors-app/logs/monitor.log 2>&1
```

### 8.2 Log Management
```bash
# Log rotation check करें
logrotate -d /etc/logrotate.d/ors-app

# Manual backup test करें
/opt/ors-app/backup.sh
```

---

## चरण 9: Testing Complete Flow

### 9.1 End-to-End Test
```bash
# 1. Code change करें (कोई छोटा change)
# 2. Git push करें
# 3. GitHub Actions workflow देखें
# 4. Deployment verify करें
# 5. Application test करें
```

### 9.2 Rollback Test
```bash
# अगर कोई issue हो तो rollback करें:
ssh root@your-droplet-ip
cd /opt/ors-app

# Previous image use करें
docker-compose down
docker-compose pull
docker-compose up -d
```

---

## 🚨 Troubleshooting Common Issues

### Issue 1: GitHub Actions Failing
```bash
# Check करें:
# 1. All secrets properly set हैं
# 2. SSH key format correct है
# 3. DockerHub credentials valid हैं
# 4. Droplet accessible है
```

### Issue 2: Database Connection Error
```bash
# Droplet में check करें:
ssh root@your-droplet-ip
cd /opt/ors-app

# Database logs
docker-compose logs database

# Database restart
docker-compose restart database

# Environment variables check
docker-compose exec backend env | grep DB_
```

### Issue 3: Frontend Not Loading
```bash
# Nginx configuration check
docker-compose exec frontend nginx -t

# Frontend logs
docker-compose logs frontend

# Restart frontend
docker-compose restart frontend
```

### Issue 4: Backend API Not Working
```bash
# Backend logs check
docker-compose logs backend

# Health check
curl http://localhost:8084/actuator/health

# Environment check
docker-compose exec backend env | grep -E "JWT_|MAIL_|DB_"
```

---

## 🎉 Success Checklist

- [ ] Local development working
- [ ] DockerHub repositories created
- [ ] DigitalOcean droplet setup
- [ ] GitHub secrets configured
- [ ] First deployment successful
- [ ] Frontend accessible
- [ ] Backend API working
- [ ] Database connected
- [ ] Email functionality working
- [ ] Monitoring setup
- [ ] Backup system working

---

## 📞 Next Steps

1. **Security**: Setup SSL certificate
2. **Monitoring**: Add advanced monitoring (Prometheus/Grafana)
3. **Scaling**: Setup load balancer if needed
4. **Backup**: Setup automated database backups
5. **CI/CD**: Add testing stages in pipeline

**Congratulations! 🎊 आपका application successfully deploy हो गया है!**
