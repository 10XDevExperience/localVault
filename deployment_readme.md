# LocalVault Deployment Guide

Complete production deployment guide for LocalVault API v2.0 including environment setup, Docker deployment, CI/CD pipeline, and monitoring configuration.

## 📋 **Table of Contents**

1. [Prerequisites](#prerequisites)
2. [Environment Configuration](#environment-configuration)
3. [Local Development Setup](#local-development-setup)
4. [Docker Deployment](#docker-deployment)
5. [Production Deployment](#production-deployment)
6. [CI/CD Pipeline](#cicd-pipeline)
7. [Monitoring & Logging](#monitoring--logging)
8. [Security Configuration](#security-configuration)
9. [Troubleshooting](#troubleshooting)

## 🚀 **Prerequisites**

### Required Software
- **Docker**: 20.10+ and Docker Compose 2.0+
- **Git**: For version control
- **Node.js**: 18+ (for mobile development)
- **Python**: 3.8+ (for local backend development)
- **OpenSSL**: For generating secure secrets

### Required Accounts & Services
- **Docker Hub**: For container registry
- **GitHub**: With repository access
- **Domain Name**: For production deployment (optional)
- **Cloud Provider**: AWS, DigitalOcean, or similar (for production)

## 🔧 **Environment Configuration**

### Core Environment Variables

Create a `.env` file in the project root with the following variables:

```bash
# =============================================================================
# CORE CONFIGURATION
# =============================================================================
# Environment: dev, staging, or production
ENV=production

# Domain name where the application will be hosted
DOMAIN_NAME=https://your-domain.com

# Unique deployment code for additional security
DEPLOYMENT_CODE=your-unique-deployment-code

# =============================================================================
# DATABASE CONFIGURATION
# =============================================================================
# Database connection URL
# SQLite for development: "sqlite:///./localvault.db"
# PostgreSQL for production: "postgresql://user:password@host:5432/database"
DATABASE_URL="postgresql://localvault_user:secure_password@db:5432/localvault_db"

# Database pool settings (PostgreSQL only)
DB_POOL_SIZE=20
DB_MAX_OVERFLOW=30

# =============================================================================
# AUTHENTICATION & SECURITY
# =============================================================================
# JWT secret key (generate with: openssl rand -hex 32)
JWT_SECRET="your-super-secure-jwt-secret-key-here-32-chars"

# Hash secret for additional security (generate with: openssl rand -hex 32)
HASH_SECRET="your-super-secure-hash-secret-key-here-32-chars"

# Token expiration times
ACCESS_TOKEN_EXPIRE_MINUTES=43200    # 12 hours
REFRESH_TOKEN_EXPIRE_DAYS=60               # 60 days

# =============================================================================
# MINIO OBJECT STORAGE
# =============================================================================
# MinIO endpoint (internal Docker network)
MINIO_ENDPOINT=minio:9000

# MinIO access credentials
MINIO_ACCESS_KEY="your-minio-access-key"
MINIO_SECRET_KEY="your-minio-secret-key"

# MinIO security settings
MINIO_SECURE=false                           # Set to true for HTTPS
MINIO_BUCKET_NAME="localvault-storage"

# =============================================================================
# REDIS CONFIGURATION
# =============================================================================
# Redis connection URL
REDIS_URL="redis://redis:6379/0"

# Redis password (optional but recommended)
REDIS_PASSWORD="your-redis-password"

# =============================================================================
# EMAIL CONFIGURATION (for OTP)
# =============================================================================
# SMTP server configuration
SMTP_HOST="smtp.gmail.com"
SMTP_PORT=587
SMTP_USERNAME="your-email@gmail.com"
SMTP_PASSWORD="your-app-password"
SMTP_USE_TLS=true

# Email settings
EMAIL_FROM="noreply@your-domain.com"
EMAIL_FROM_NAME="LocalVault"

# =============================================================================
# APPLICATION SETTINGS
# =============================================================================
# API host and port
API_HOST=0.0.0.0
API_PORT=8000

# CORS settings (comma-separated origins)
CORS_ORIGINS="https://your-domain.com,https://www.your-domain.com"

# File upload limits (in bytes)
MAX_FILE_SIZE=20971520      # 20MB
MAX_TEXT_SIZE=1048576       # 1MB

# =============================================================================
# LOGGING CONFIGURATION
# =============================================================================
# Log level: DEBUG, INFO, WARNING, ERROR, CRITICAL
LOG_LEVEL=INFO

# Log format
LOG_FORMAT="%(asctime)s - %(name)s - %(levelname)s - %(message)s"

# =============================================================================
# MONITORING & HEALTH CHECKS
# =============================================================================
# Enable health check endpoints
ENABLE_HEALTH_CHECKS=true

# Monitoring settings
METRICS_ENABLED=true
METRICS_PORT=9090

# =============================================================================
# DEVELOPMENT ONLY
# =============================================================================
# Debug mode (never enable in production)
DEBUG=false

# Auto-reload for development
AUTO_RELOAD=false
```

### Environment-Specific Configuration Files

#### Development (`.env.dev`)
```bash
ENV=dev
DOMAIN_NAME=http://localhost:8000
DATABASE_URL="sqlite:///./localvault.db"
MINIO_ENDPOINT=localhost:9000
MINIO_ACCESS_KEY=minioadmin
MINIO_SECRET_KEY=minioadmin
JWT_SECRET="dev-jwt-secret-change-in-production"
HASH_SECRET="dev-hash-secret-change-in-production"
DEPLOYMENT_CODE=637984
DEBUG=true
AUTO_RELOAD=true
```

#### Staging (`.env.staging`)
```bash
ENV=staging
DOMAIN_NAME=https://staging.your-domain.com
DATABASE_URL="postgresql://staging_user:staging_pass@staging-db:5432/localvault_staging"
MINIO_ENDPOINT=staging-minio:9000
# Use secure secrets from GitHub secrets
JWT_SECRET=${JWT_SECRET}
HASH_SECRET=${HASH_SECRET}
DEPLOYMENT_CODE=${DEPLOYMENT_CODE}
LOG_LEVEL=DEBUG
```

#### Production (`.env.prod`)
```bash
ENV=production
DOMAIN_NAME=https://your-domain.com
DATABASE_URL="postgresql://prod_user:secure_pass@prod-db:5432/localvault_prod"
MINIO_ENDPOINT=prod-minio:9000
# Use secure secrets from GitHub secrets
JWT_SECRET=${JWT_SECRET}
HASH_SECRET=${HASH_SECRET}
DEPLOYMENT_CODE=${DEPLOYMENT_CODE}
LOG_LEVEL=INFO
DEBUG=false
```

## 🏠 **Local Development Setup**

### 1. Backend Development

```bash
# Navigate to backend directory
cd backend

# Create and activate virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Copy environment file
cp .env.sample .env

# Edit environment file
nano .env

# Run database migrations
alembic upgrade head

# Start development server
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### 2. Mobile App Development

```bash
# Navigate to mobile directory
cd mobile/LocalVault

# Install dependencies
npm install

# Copy and configure environment
cp .env.example .env
# Edit .env with your API endpoint

# Start development server
npx expo start

# Run on specific platforms
npx expo start --android
npx expo start --ios
npx expo start --web
```

### 3. Browser Extension Development

```bash
# Navigate to extension directory
cd extension

# Load in Chrome
# 1. Open Chrome and go to chrome://extensions/
# 2. Enable "Developer mode"
# 3. Click "Load unpacked"
# 4. Select the extension directory

# For development with hot reload
npm install
npm run dev
```

## 🐳 **Docker Deployment**

### Development Docker Setup

```bash
# Start development environment
docker-compose up -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down

# Rebuild and restart
docker-compose up -d --build
```

### Development Docker Compose (`docker-compose.yml`)

```yaml
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    env_file:
      - .env
    environment:
      - DATABASE_URL=sqlite:///./localvault.db
      - MINIO_ENDPOINT=minio:9000
      - REDIS_URL=redis://redis:6379/0
    volumes:
      - ./data:/app/data
      - ./backend:/app
    depends_on:
      - minio
      - redis
    restart: unless-stopped

  minio:
    image: minio/minio:latest
    ports:
      - "9000:9000"
      - "9001:9001"
    environment:
      - MINIO_ROOT_USER=minioadmin
      - MINIO_ROOT_PASSWORD=minioadmin
    volumes:
      - ./data/minio:/data
    command: server /data --console-address ":9001"
    restart: unless-stopped

  minio-setup:
    image: minio/mc:latest
    depends_on:
      - minio
    entrypoint: >
      /bin/sh -c "
      sleep 10;
      /usr/bin/mc config host add localminio http://minio:9000 minioadmin minioadmin;
      /usr/bin/mc mb localminio/shared || true;
      echo 'MinIO buckets created successfully';
      "
    restart: "no"

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - ./data/redis:/data
    restart: unless-stopped

volumes:
  data:
    driver: local
```

## 🚀 **Production Deployment**

### 1. Server Preparation

```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Install Docker and Docker Compose
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Create application directory
sudo mkdir -p /opt/localvault
sudo chown $USER:$USER /opt/localvault
cd /opt/localvault
```

### 2. Clone and Configure

```bash
# Clone repository
git clone https://github.com/your-username/local_vault.git .

# Create production environment file
cp .env.prod .env

# Generate secure secrets
JWT_SECRET=$(openssl rand -hex 32)
HASH_SECRET=$(openssl rand -hex 32)
DEPLOYMENT_CODE=$(openssl rand -hex 6)

# Update .env file with secure secrets
sed -i "s/your-super-secure-jwt-secret-key/${JWT_SECRET}/" .env
sed -i "s/your-super-secure-hash-secret/${HASH_SECRET}/" .env
sed -i "s/your-unique-deployment-code/${DEPLOYMENT_CODE}/" .env
```

### 3. Production Docker Compose (`docker-compose.prod.yml`)

```yaml
version: '3.8'

services:
  backend:
    build: 
      context: ./backend
      dockerfile: Dockerfile.prod
    restart: unless-stopped
    env_file:
      - .env
    environment:
      - DATABASE_URL=${DATABASE_URL}
      - MINIO_ENDPOINT=minio:9000
      - REDIS_URL=redis://redis:6379/0
      - JWT_SECRET=${JWT_SECRET}
      - HASH_SECRET=${HASH_SECRET}
      - DEPLOYMENT_CODE=${DEPLOYMENT_CODE}
    volumes:
      - ./data/app:/app/data
      - ./logs:/app/logs
    depends_on:
      - db
      - minio
      - redis
    networks:
      - localvault-network

  db:
    image: postgres:15-alpine
    restart: unless-stopped
    environment:
      - POSTGRES_DB=localvault_prod
      - POSTGRES_USER=localvault_user
      - POSTGRES_PASSWORD=${DB_PASSWORD}
    volumes:
      - ./data/postgres:/var/lib/postgresql/data
      - ./backups:/backups
    networks:
      - localvault-network

  minio:
    image: minio/minio:latest
    restart: unless-stopped
    command: server /data --console-address ":9001"
    environment:
      - MINIO_ROOT_USER=${MINIO_ACCESS_KEY}
      - MINIO_ROOT_PASSWORD=${MINIO_SECRET_KEY}
    volumes:
      - ./data/minio:/data
    networks:
      - localvault-network

  redis:
    image: redis:7-alpine
    restart: unless-stopped
    command: redis-server --requirepass ${REDIS_PASSWORD}
    volumes:
      - ./data/redis:/data
    networks:
      - localvault-network

  nginx:
    image: nginx:alpine
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/ssl:/etc/nginx/ssl
      - ./logs/nginx:/var/log/nginx
    depends_on:
      - backend
      - minio
    networks:
      - localvault-network

networks:
  localvault-network:
    driver: bridge

volumes:
  data:
    driver: local
  logs:
    driver: local
  backups:
    driver: local
```

### 4. Nginx Configuration (`nginx/nginx.conf`)

```nginx
events {
    worker_connections 1024;
}

http {
    upstream backend {
        server backend:8000;
    }

    upstream minio {
        server minio:9000;
    }

    # Rate limiting
    limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;
    limit_req_zone $binary_remote_addr zone=upload:10m rate=2r/s;

    server {
        listen 80;
        server_name your-domain.com www.your-domain.com;
        return 301 https://$server_name$request_uri;
    }

    server {
        listen 443 ssl http2;
        server_name your-domain.com www.your-domain.com;

        ssl_certificate /etc/nginx/ssl/cert.pem;
        ssl_certificate_key /etc/nginx/ssl/key.pem;
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;

        # Security headers
        add_header X-Frame-Options DENY;
        add_header X-Content-Type-Options nosniff;
        add_header X-XSS-Protection "1; mode=block";
        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";

        # API routes
        location /api/ {
            limit_req zone=api burst=20 nodelay;
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # File upload routes with stricter rate limiting
        location /api/v1/content/upload {
            limit_req zone=upload burst=5 nodelay;
            client_max_body_size 20M;
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # MinIO console
        location /minio/ {
            proxy_pass http://minio;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # Health checks
        location /health {
            proxy_pass http://backend;
            access_log off;
        }
    }
}
```

### 5. Deploy Production Services

```bash
# Build and start production containers
docker-compose -f docker-compose.prod.yml up -d --build

# Run database migrations
docker-compose -f docker-compose.prod.yml exec backend alembic upgrade head

# Check service status
docker-compose -f docker-compose.prod.yml ps

# View logs
docker-compose -f docker-compose.prod.yml logs -f backend
```

## 🔄 **CI/CD Pipeline**

### GitHub Actions Workflow (`.github/workflows/deploy.yml`)

```yaml
name: Deploy LocalVault

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          cd backend
          pip install -r requirements.txt
          pip install pytest pytest-cov

      - name: Run tests
        run: |
          cd backend
          pytest tests/ -v --cov=.

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./backend/coverage.xml

  security-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'sarif'
          output: 'trivy-results.sarif'

      - name: Upload Trivy scan results
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'

  build-and-push:
    needs: [test, security-scan]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    outputs:
      image-tag: ${{ steps.meta.outputs.tags }}
      image-digest: ${{ steps.build.outputs.digest }}
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Log in to Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}

      - name: Build and push Docker image
        id: build
        uses: docker/build-push-action@v5
        with:
          context: ./backend
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

  deploy-staging:
    needs: build-and-push
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/develop'
    environment: staging
    steps:
      - name: Deploy to staging
        uses: appleboy/ssh-action@v1.0.0
        with:
          host: ${{ secrets.STAGING_HOST }}
          username: ${{ secrets.STAGING_USER }}
          key: ${{ secrets.STAGING_SSH_KEY }}
          script: |
            cd /opt/localvault-staging
            git pull origin develop
            docker-compose -f docker-compose.staging.yml down
            docker-compose -f docker-compose.staging.yml up -d --build
            docker-compose -f docker-compose.staging.yml exec backend alembic upgrade head

  deploy-production:
    needs: build-and-push
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - name: Deploy to production
        uses: appleboy/ssh-action@v1.0.0
        with:
          host: ${{ secrets.PRODUCTION_HOST }}
          username: ${{ secrets.PRODUCTION_USER }}
          key: ${{ secrets.PRODUCTION_SSH_KEY }}
          script: |
            cd /opt/localvault
            git pull origin main
            docker-compose -f docker-compose.prod.yml down
            docker-compose -f docker-compose.prod.yml up -d --build
            docker-compose -f docker-compose.prod.yml exec backend alembic upgrade head

      - name: Health check
        run: |
          sleep 30
          curl -f https://your-domain.com/health || exit 1
```

### Required GitHub Secrets

Configure these secrets in your GitHub repository settings:

```yaml
# Database Credentials
DB_PASSWORD: "your-secure-database-password"
PRODUCTION_DB_URL: "postgresql://user:pass@host:5432/db"

# MinIO Credentials
MINIO_ACCESS_KEY: "your-minio-access-key"
MINIO_SECRET_KEY: "your-minio-secret-key"

# Security Secrets
JWT_SECRET: "your-jwt-secret-32-characters"
HASH_SECRET: "your-hash-secret-32-characters"
DEPLOYMENT_CODE: "your-unique-deployment-code"

# Email Configuration
SMTP_HOST: "smtp.gmail.com"
SMTP_PORT: "587"
SMTP_USERNAME: "your-email@gmail.com"
SMTP_PASSWORD: "your-app-password"

# Server Access
STAGING_HOST: "staging.your-domain.com"
STAGING_USER: "deploy"
STAGING_SSH_KEY: "-----BEGIN RSA PRIVATE KEY-----\n..."

PRODUCTION_HOST: "your-domain.com"
PRODUCTION_USER: "deploy"
PRODUCTION_SSH_KEY: "-----BEGIN RSA PRIVATE KEY-----\n..."

# SSL Certificates (optional, for auto-renewal)
SSL_CERT: "-----BEGIN CERTIFICATE-----\n..."
SSL_KEY: "-----BEGIN PRIVATE KEY-----\n..."

# Monitoring
SENTRY_DSN: "https://your-sentry-dsn"
```

## 📊 **Monitoring & Logging**

### 1. Application Health Checks

```bash
# Overall health
curl https://your-domain.com/health

# Database health
curl https://your-domain.com/health/db

# MinIO health
curl https://your-domain.com/health/minio

# Redis health
curl https://your-domain.com/health/redis
```

### 2. Log Management

```bash
# View application logs
docker-compose -f docker-compose.prod.yml logs -f backend

# View Nginx logs
docker-compose -f docker-compose.prod.yml logs -f nginx

# View database logs
docker-compose -f docker-compose.prod.yml logs -f db

# Rotate logs (add to cron)
0 2 * * * /usr/bin/docker system prune -f
```

### 3. Monitoring Setup

#### Prometheus Configuration (`monitoring/prometheus.yml`)

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'localvault'
    static_configs:
      - targets: ['backend:9090']
    metrics_path: '/metrics'
    scrape_interval: 5s

  - job_name: 'nginx'
    static_configs:
      - targets: ['nginx:9113']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node-exporter:9100']
```

#### Grafana Dashboard

Create a dashboard in Grafana with these panels:
- API Response Time
- Request Rate
- Error Rate
- Database Connections
- File Storage Usage
- Active Users
- Memory Usage
- CPU Usage

## 🔒 **Security Configuration**

### 1. SSL/TLS Setup

```bash
# Generate self-signed certificate (for development)
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout nginx/ssl/key.pem \
  -out nginx/ssl/cert.pem

# Let's Encrypt (for production)
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

### 2. Firewall Configuration

```bash
# Configure UFW firewall
sudo ufw enable
sudo ufw allow ssh
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw deny 9000/tcp  # Block direct MinIO access
sudo ufw deny 5432/tcp  # Block direct DB access
sudo ufw deny 6379/tcp  # Block direct Redis access
```

### 3. Security Hardening

```bash
# Update system regularly
sudo apt update && sudo apt upgrade -y

# Install security tools
sudo apt install fail2ban unattended-upgrades

# Configure fail2ban
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# Set up automatic security updates
sudo dpkg-reconfigure -plow unattended-upgrades
```

## 🔧 **Troubleshooting**

### Common Issues and Solutions

#### 1. Database Connection Issues

```bash
# Check database container status
docker-compose -f docker-compose.prod.yml ps db

# View database logs
docker-compose -f docker-compose.prod.yml logs db

# Connect to database
docker-compose -f docker-compose.prod.yml exec db psql -U localvault_user -d localvault_prod

# Reset database (DANGEROUS - data loss)
docker-compose -f docker-compose.prod.yml down
docker volume rm localvault_data_postgres
docker-compose -f docker-compose.prod.yml up -d db
docker-compose -f docker-compose.prod.yml exec backend alembic upgrade head
```

#### 2. MinIO Connection Issues

```bash
# Check MinIO container
docker-compose -f docker-compose.prod.yml ps minio

# View MinIO logs
docker-compose -f docker-compose.prod.yml logs minio

# Test MinIO connection
docker-compose -f docker-compose.prod.yml exec backend python -c "
from utils.minio_conn import minio_client
print('MinIO connection:', minio_client.list_buckets())
"

# Reset MinIO (DANGEROUS - data loss)
docker-compose -f docker-compose.prod.yml down
docker volume rm localvault_data_minio
docker-compose -f docker-compose.prod.yml up -d minio
```

#### 3. Redis Connection Issues

```bash
# Check Redis container
docker-compose -f docker-compose.prod.yml ps redis

# Test Redis connection
docker-compose -f docker-compose.prod.yml exec redis redis-cli ping

# Clear Redis cache
docker-compose -f docker-compose.prod.yml exec redis redis-cli FLUSHALL
```

#### 4. Performance Issues

```bash
# Check system resources
docker stats
free -h
df -h

# Check application logs for errors
docker-compose -f docker-compose.prod.yml logs backend | grep ERROR

# Monitor database queries
docker-compose -f docker-compose.prod.yml exec db psql -U localvault_user -d localvault_prod -c "
SELECT query, mean_time, calls 
FROM pg_stat_statements 
ORDER BY mean_time DESC 
LIMIT 10;
"
```

#### 5. SSL/TLS Issues

```bash
# Check certificate expiration
openssl x509 -in nginx/ssl/cert.pem -noout -dates

# Test SSL configuration
sudo nginx -t

# Reload Nginx
docker-compose -f docker-compose.prod.yml exec nginx nginx -s reload

# Check SSL with curl
curl -I https://your-domain.com
```

### Backup and Recovery

```bash
# Database backup
docker-compose -f docker-compose.prod.yml exec db pg_dump -U localvault_user localvault_prod > backup_$(date +%Y%m%d_%H%M%S).sql

# MinIO backup
docker run --rm -v localvault_data_minio:/data -v $(pwd):/backup minio/mc mirror /data /backup/minio_$(date +%Y%m%d_%H%M%S)

# Restore database
docker-compose -f docker-compose.prod.yml exec -T db psql -U localvault_user localvault_prod < backup_file.sql

# Restore MinIO
docker run --rm -v localvault_data_minio:/data -v $(pwd):/backup minio/mc mirror /backup/minio_backup /data
```

### Performance Optimization

```bash
# Optimize PostgreSQL
docker-compose -f docker-compose.prod.yml exec db psql -U localvault_user -d localvault_prod -c "
VACUUM ANALYZE;
REINDEX DATABASE localvault_prod;
"

# Clean up Docker
docker system prune -f
docker volume prune -f

# Monitor performance
htop
iotop
nethogs
```

---

## 📞 **Support**

For deployment issues:

1. **Check logs**: Always check container logs first
2. **Review configuration**: Verify all environment variables
3. **Check network**: Ensure containers can communicate
4. **Validate certificates**: Ensure SSL/TLS is properly configured
5. **Monitor resources**: Check CPU, memory, and disk usage

**Emergency Recovery**:
```bash
# Quick restart all services
docker-compose -f docker-compose.prod.yml restart

# Full rebuild (last resort)
docker-compose -f docker-compose.prod.yml down
docker-compose -f docker-compose.prod.yml up -d --build
```

---

*This deployment guide covers all aspects of deploying LocalVault in production, from basic setup to advanced monitoring and security hardening.*
