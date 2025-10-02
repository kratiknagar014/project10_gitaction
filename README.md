# ORS Project 10 - Full Stack Deployment Guide

## 🚀 Project Overview

यह एक complete full-stack web application है जो Angular frontend और Spring Boot backend के साथ बनाई गई है। इस project को Docker, GitHub Actions, और DigitalOcean का use करके deploy किया जा सकता है।

### 🏗️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Angular UI    │    │  Spring Boot    │    │     MySQL       │
│   (Port 80)     │◄──►│   (Port 8084)   │◄──►│   (Port 3306)   │
│                 │    │                 │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### 📦 Technologies Used

**Frontend:**
- Angular 7.2.0
- Bootstrap 4.6.0
- Firebase (Notifications)
- TypeScript

**Backend:**
- Spring Boot 2.1.2
- Java 11
- Spring Security + JWT
- MySQL 8.0
- Swagger API Documentation

**DevOps:**
- Docker & Docker Compose
- GitHub Actions (CI/CD)
- DockerHub (Image Registry)
- DigitalOcean Droplet (Hosting)

## 🛠️ Local Development Setup

### Prerequisites
- Docker & Docker Compose installed
- Node.js 14+ (for frontend development)
- Java 11+ (for backend development)
- Git

### 1. Clone Repository
```bash
git clone <your-repo-url>
cd gitAction
```

### 2. Setup Environment Variables
```bash
cp .env.example .env
# Edit .env file with your actual values
```

### 3. Run with Docker Compose
```bash
# Start all services
docker-compose up -d

# Check status
docker-compose ps

# View logs
docker-compose logs -f
```

### 4. Access Application
- **Frontend**: http://localhost
- **Backend API**: http://localhost:8084
- **API Documentation**: http://localhost:8084/swagger-ui.html
- **Database**: localhost:3306

## 🌐 Production Deployment

### Step 1: Setup DockerHub Account
1. Create account at https://hub.docker.com
2. Create repositories:
   - `your-username/ors-backend`
   - `your-username/ors-frontend`

### Step 2: Setup DigitalOcean Droplet
1. Create Ubuntu 20.04 droplet (minimum 2GB RAM)
2. Add your SSH key
3. Run setup script:
```bash
# Copy setup script to droplet
scp deployment/setup-droplet.sh root@your-droplet-ip:/tmp/

# SSH into droplet and run setup
ssh root@your-droplet-ip
chmod +x /tmp/setup-droplet.sh
/tmp/setup-droplet.sh
```

### Step 3: Configure GitHub Secrets
Add these secrets in your GitHub repository settings:

```
DOCKER_USERNAME=your_dockerhub_username
DOCKER_PASSWORD=your_dockerhub_password
DO_DROPLET_IP=your_droplet_ip_address
DO_SSH_PRIVATE_KEY=your_ssh_private_key
DB_PASSWORD=your_secure_database_password
JWT_SECRET=your_jwt_secret_key
MAIL_USERNAME=your_email@gmail.com
MAIL_PASSWORD=your_gmail_app_password
```

### Step 4: Deploy
1. Push code to main/master branch
2. GitHub Actions will automatically:
   - Build and test the application
   - Create Docker images
   - Push images to DockerHub
   - Deploy to DigitalOcean droplet

## 📊 Monitoring & Management

### Health Checks
```bash
# Check application health
curl http://your-droplet-ip:8084/actuator/health

# Check frontend
curl http://your-droplet-ip/

# Monitor containers
docker-compose ps
```

### Logs
```bash
# View all logs
docker-compose logs -f

# View specific service logs
docker-compose logs -f backend
docker-compose logs -f frontend
```

### Backup
```bash
# Manual backup
/opt/ors-app/backup.sh

# Automated backups run daily at 2 AM
```

## 🔧 Troubleshooting

### Common Issues

**1. Database Connection Failed**
```bash
# Check database status
docker-compose ps database

# Check database logs
docker-compose logs database

# Restart database
docker-compose restart database
```

**2. Backend Not Starting**
```bash
# Check backend logs
docker-compose logs backend

# Check environment variables
docker-compose exec backend env | grep -E "DB_|JWT_|MAIL_"

# Restart backend
docker-compose restart backend
```

**3. Frontend Not Loading**
```bash
# Check frontend logs
docker-compose logs frontend

# Check nginx configuration
docker-compose exec frontend nginx -t

# Restart frontend
docker-compose restart frontend
```

### Performance Optimization

**1. Database Optimization**
```sql
-- Add indexes for better performance
ALTER TABLE your_table ADD INDEX idx_column_name (column_name);

-- Optimize tables
OPTIMIZE TABLE your_table;
```

**2. Application Optimization**
```bash
# Increase JVM memory (if needed)
# Edit docker-compose.yml and add:
environment:
  JAVA_OPTS: "-Xmx1024m -Xms512m"
```

## 🔐 Security Best Practices

1. **Environment Variables**: Never commit sensitive data
2. **Database**: Use strong passwords and limit connections
3. **SSL/TLS**: Setup HTTPS with Let's Encrypt (recommended)
4. **Firewall**: Only open necessary ports
5. **Updates**: Keep Docker images and system updated

## 📈 Scaling Options

### Horizontal Scaling
```yaml
# Add multiple backend instances
backend:
  scale: 3
  
# Add load balancer
nginx:
  image: nginx:alpine
  # Configure load balancing
```

### Vertical Scaling
```bash
# Increase droplet size in DigitalOcean dashboard
# Update docker-compose.yml with more resources
```

## 🤝 Contributing

1. Fork the repository
2. Create feature branch: `git checkout -b feature/new-feature`
3. Commit changes: `git commit -am 'Add new feature'`
4. Push to branch: `git push origin feature/new-feature`
5. Submit pull request

## 📞 Support

For issues and questions:
- Create GitHub issue
- Check logs: `docker-compose logs`
- Monitor health: `/opt/ors-app/monitor.sh`

## 📝 License

This project is licensed under the MIT License.

---

**Happy Coding! 🚀**
