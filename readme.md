# LocalVault API v2.0 - Complete Full-Stack Content Management System

A comprehensive, production-ready local file sharing and content management system with support for both file uploads and text content storage using a polymorphic model architecture. This project includes a FastAPI backend, React Native mobile app, and browser extension.

## 🚀 Features & Architecture

### 🏗️ **Polymorphic Content Model**
- **Unified Architecture**: Single `Content` model handles both file uploads and text content
- **File Content**: Stores file metadata and references to MinIO storage
- **Text Content**: Stores text directly in the database with full-text search
- **Common Fields**: Title, tags, timestamps, ownership, and content type detection

### 🔐 **Dual Authentication System**
- **User Authentication**: OTP-based login with email verification
- **Device Authentication**: Device-based authentication for seamless access
- **JWT Tokens**: Secure access and refresh token system
- **Session Management**: Redis-based OTP storage for scalability

### 📁 **File Management**
- **Multi-format Support**: Images, documents, archives, text files, and more
- **Size Limits**: 20MB for files, 1MB for text content
- **Automatic Type Detection**: Smart content type identification
- **MinIO Integration**: Scalable object storage with bucket management

### 📱 **Multi-Platform Support**
- **FastAPI Backend**: High-performance async API with automatic documentation
- **React Native Mobile**: Cross-platform mobile app (iOS/Android)
- **Browser Extension**: Chrome extension for web integration
- **Docker Support**: Complete containerized deployment

## 📋 **Project Structure**

```
local_vault/
├── backend/                    # FastAPI backend application
│   ├── apis/                  # API route handlers
│   │   ├── auth_api.py       # Authentication endpoints
│   │   ├── content_api.py     # Content management endpoints
│   │   ├── download_api.py    # File download endpoints
│   │   └── routers.py        # API router configuration
│   ├── db/                    # Database layer
│   │   ├── models.py          # SQLAlchemy models
│   │   ├── schema.py          # Pydantic schemas
│   │   └── db_conn.py        # Database connection
│   ├── services/               # Business logic
│   │   └── user_service.py   # User management services
│   ├── utils/                  # Utilities and helpers
│   │   ├── minio_conn.py     # MinIO integration
│   │   ├── redis_helper.py    # Redis operations
│   │   └── app_logger.py     # Logging configuration
│   ├── alembic/               # Database migrations
│   ├── requirements.txt        # Python dependencies
│   ├── Dockerfile            # Backend Docker configuration
│   └── main.py              # FastAPI application entry
├── extension/                # Browser extension
│   ├── manifest.json         # Extension configuration
│   ├── popup.html/js       # Extension popup interface
│   ├── content.js           # Content script
│   └── background.js        # Background service worker
├── mobile/                  # React Native mobile app
│   └── LocalVault/
│       ├── src/            # Mobile app source code
│       ├── package.json     # Mobile dependencies
│       └── app.json        # Expo configuration
├── docker-compose.yaml       # Complete stack deployment
├── deployment_readme.md     # Detailed deployment guide
└── readme.md              # This file
```

## 🛠️ **Technology Stack**

### Backend
- **FastAPI**: Modern, fast web framework for building APIs
- **SQLAlchemy**: Powerful ORM for database operations
- **Alembic**: Database migration tool
- **Pydantic**: Data validation and settings management
- **MinIO**: High-performance object storage
- **Redis**: In-memory data structure store
- **PostgreSQL**: Reliable relational database (production)
- **SQLite**: Lightweight database (development)

### Mobile
- **React Native**: Cross-platform mobile development
- **Expo**: Development platform for React Native
- **React Navigation**: Navigation library
- **Axios**: HTTP client for API calls

### Infrastructure
- **Docker**: Containerization platform
- **Docker Compose**: Multi-container orchestration
- **Nginx**: Reverse proxy and load balancer
- **GitHub Actions**: CI/CD pipeline

## 🚀 **Quick Start for YouTube Tutorial**

### Prerequisites
- **Docker & Docker Compose**: For containerized deployment
- **Git**: For cloning the repository
- **Node.js 18+**: For mobile app development (optional)
- **Python 3.8+**: For local backend development (optional)

### 1. Clone and Setup
```bash
# Clone the repository
git clone https://github.com/your-username/local_vault.git
cd local_vault

# Copy environment configuration
cp backend/.env.sample .env
```

### 2. Configure Environment Variables
Edit the `.env` file with your configuration:

```bash
# Environment
ENV=dev
DOMAIN_NAME=http://localhost:8000

# Database Configuration
DATABASE_URL="sqlite:///./localvault.db"  # Use PostgreSQL for production

# MinIO Object Storage
MINIO_ENDPOINT=localhost:9000
MINIO_ACCESS_KEY=minioadmin
MINIO_SECRET_KEY=minioadmin

# JWT Configuration
JWT_SECRET="your-super-secret-jwt-key-change-in-production"
DEPLOYMENT_CODE=637984

# Token Expiration
ACCESS_TOKEN_EXPIRE_MINUTES=43200  # 12 hours
REFRESH_TOKEN_EXPIRE_DAYS=60

# Security
HASH_SECRET="your-hash-secret-change-in-production"
```

### 3. Launch with Docker Compose
```bash
# Start all services
docker-compose up -d

# Check service status
docker-compose ps

# View logs (if needed)
docker-compose logs -f
```

### 4. Access the Application
- **API Documentation**: http://localhost:8000/docs
- **ReDoc Documentation**: http://localhost:8000/redoc
- **MinIO Console**: http://localhost:9001
- **API Base URL**: http://localhost:8000/api/v1

## 📚 **API Documentation**

### Authentication Endpoints
```bash
# User Login (sends OTP)
POST /api/v1/auth/login
{
    "email": "user@example.com"
}

# Verify OTP
POST /api/v1/auth/verify-otp
{
    "email": "user@example.com",
    "otp": "123456"
}

# Device Login
POST /api/v1/auth/device-login
{
    "device_id": "unique-device-identifier"
}

# Refresh Token
POST /api/v1/auth/refresh
{
    "refresh_token": "refresh-token-here"
}
```

### Content Management Endpoints
```bash
# Upload Content (unified for files and text)
POST /api/v1/content/upload
Content-Type: multipart/form-data

# List User Content
GET /api/v1/content/list
Headers: Authorization: Bearer <token>

# Download File
GET /api/v1/content/download/{content_id}
Headers: Authorization: Bearer <token>

# Get Text Content
GET /api/v1/content/{content_id}
Headers: Authorization: Bearer <token>

# Delete Content
DELETE /api/v1/content/{content_id}
Headers: Authorization: Bearer <token>

# Search Content
GET /api/v1/content/search?query=search-term
Headers: Authorization: Bearer <token>
```

## 📁 **Supported File Types**

### Images
- JPEG, PNG, GIF, WebP, SVG
- Maximum size: 20MB

### Documents
- PDF, Word (.doc, .docx)
- Excel (.xls, .xlsx)
- PowerPoint (.ppt, .pptx)
- Maximum size: 20MB

### Text Files
- TXT, CSV, HTML, CSS, JavaScript, JSON, XML
- Maximum size: 20MB

### Archives
- ZIP, RAR, 7Z
- Maximum size: 20MB

### Text Content
- Plain text, notes, code snippets
- Maximum size: 1MB
- Stored directly in database with full-text search

## 🔧 **Development Setup**

### Backend Development
```bash
# Navigate to backend directory
cd backend

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run database migrations
alembic upgrade head

# Start development server
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### Mobile App Development
```bash
# Navigate to mobile directory
cd mobile/LocalVault

# Install dependencies
npm install

# Start development server
npx expo start

# Run on specific platforms
npx expo start --android
npx expo start --ios
npx expo start --web
```

### Browser Extension Development
```bash
# Navigate to extension directory
cd extension

# Load extension in Chrome
# 1. Open Chrome and go to chrome://extensions/
# 2. Enable "Developer mode"
# 3. Click "Load unpacked"
# 4. Select the extension directory
```

## 🐳 **Docker Configuration**

### Production Deployment
```bash
# Build and start production containers
docker-compose -f docker-compose.prod.yml up -d

# Scale backend services
docker-compose -f docker-compose.prod.yml up -d --scale backend=3
```

### Environment-Specific Configurations
- **Development**: SQLite database, local MinIO
- **Staging**: PostgreSQL, remote MinIO, basic monitoring
- **Production**: PostgreSQL cluster, MinIO cluster, full monitoring

## 🔒 **Security Considerations**

### Production Security Checklist
- [ ] Change all default passwords and secrets
- [ ] Use HTTPS/TLS certificates
- [ ] Configure proper CORS origins
- [ ] Set up database connection pooling
- [ ] Enable Redis authentication
- [ ] Configure MinIO with SSL/TLS
- [ ] Set up proper logging and monitoring
- [ ] Implement rate limiting
- [ ] Add input validation and sanitization
- [ ] Regular security updates and patches

### Environment Variables Security
```bash
# Generate secure secrets
JWT_SECRET=$(openssl rand -hex 32)
HASH_SECRET=$(openssl rand -hex 32)
DEPLOYMENT_CODE=$(openssl rand -hex 6)

# Use in production
echo "JWT_SECRET=${JWT_SECRET}" >> .env
echo "HASH_SECRET=${HASH_SECRET}" >> .env
echo "DEPLOYMENT_CODE=${DEPLOYMENT_CODE}" >> .env
```

## 📊 **Monitoring and Logging**

### Application Metrics
- **API Response Times**: Track endpoint performance
- **Database Query Performance**: Monitor slow queries
- **File Storage Usage**: Track MinIO storage metrics
- **Authentication Events**: Log login attempts and failures

### Health Checks
```bash
# Application health
GET /health

# Database connection
GET /health/db

# MinIO connection
GET /health/minio

# Redis connection
GET /health/redis
```

## 🚀 **Performance Optimization**

### Database Optimization
- **Indexing**: Proper indexes on frequently queried columns
- **Connection Pooling**: Efficient database connection management
- **Query Optimization**: Analyze and optimize slow queries
- **Caching**: Redis caching for frequently accessed data

### File Storage Optimization
- **Compression**: Automatic file compression for supported types
- **CDN Integration**: Content delivery network for static assets
- **Lifecycle Policies**: Automatic cleanup of old unused files
- **Backup Strategies**: Regular backup and disaster recovery

## 🔄 **CI/CD Pipeline**

### GitHub Actions Workflow
- **Automatic Testing**: Run tests on pull requests
- **Security Scanning**: Dependency vulnerability checks
- **Docker Builds**: Automated image building and pushing
- **Deployment**: Automated deployment to staging/production

### Required GitHub Secrets
```yaml
# GitHub Repository Secrets
DOCKER_USERNAME: "your-dockerhub-username"
DOCKER_PASSWORD: "your-dockerhub-password"
PRODUCTION_DB_URL: "postgresql://user:pass@host:5432/db"
MINIO_ACCESS_KEY: "your-minio-access-key"
MINIO_SECRET_KEY: "your-minio-secret-key"
JWT_SECRET: "your-production-jwt-secret"
HASH_SECRET: "your-production-hash-secret"
DEPLOYMENT_CODE: "your-production-deployment-code"
```

## 🌐 **Nginx Configuration**

### Basic Reverse Proxy Setup
```nginx
server {
    listen 80;
    server_name your-domain.com;
    
    location /api/ {
        proxy_pass http://backend:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    
    location /minio/ {
        proxy_pass http://minio:9000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## 🤝 **Contributing Guidelines**

### Development Workflow
1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Make your changes with proper testing
4. Commit your changes: `git commit -m 'Add amazing feature'`
5. Push to the branch: `git push origin feature/amazing-feature`
6. Open a Pull Request

### Code Standards
- **Python**: Follow PEP 8, use type hints
- **JavaScript**: Use ESLint configuration, modern ES6+ syntax
- **Documentation**: Update docs for new features
- **Testing**: Write tests for new functionality
- **Git**: Write clear, descriptive commit messages

### Testing
```bash
# Backend tests
cd backend
pytest tests/ -v

# Mobile app tests
cd mobile/LocalVault
npm test

# Extension tests
cd extension
npm test
```

## 📄 **License**

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🆘 **Support and Community**

### Getting Help
- **Documentation**: Check this README and `deployment_readme.md`
- **API Documentation**: Visit `/docs` endpoint
- **Issues**: Create GitHub issues for bugs and feature requests
- **Discussions**: Use GitHub Discussions for questions

### Common Issues
- **CORS Errors**: Check `allow_origins` in CORS middleware
- **Database Connection**: Verify `DATABASE_URL` format and credentials
- **MinIO Connection**: Ensure MinIO is running and credentials are correct
- **File Upload Issues**: Check file size limits and supported formats

## 🎯 **Roadmap**

### Upcoming Features
- [ ] **Web Dashboard**: React-based web interface
- [ ] **Real-time Sync**: WebSocket-based real-time updates
- [ ] **Advanced Search**: Full-text search with filters
- [ ] **File Versioning**: Track file changes and history
- [ ] **Sharing Features**: Share content with other users
- [ ] **Mobile Notifications**: Push notifications for important events
- [ ] **Offline Support**: PWA capabilities for mobile app
- [ ] **Analytics**: Usage analytics and insights

### Performance Improvements
- [ ] **Database Optimization**: Advanced query optimization
- [ ] **Caching Layer**: Redis caching for API responses
- [ ] **Image Processing**: Automatic image optimization
- [ ] **Compression**: Better file compression algorithms

## 📺 **YouTube Tutorial Series**

This project is designed as a comprehensive tutorial series covering:

### Series Outline
1. **Project Setup & Architecture Overview**
2. **Backend API Development with FastAPI**
3. **Database Design & Migrations**
4. **Authentication & Security Implementation**
5. **File Storage with MinIO**
6. **React Native Mobile App Development**
7. **Browser Extension Development**
8. **Docker & Production Deployment**
9. **CI/CD Pipeline with GitHub Actions**
10. **Performance Optimization & Monitoring**

### Tutorial Features
- **Step-by-Step Instructions**: Detailed explanations for each concept
- **Code Examples**: Complete, working code samples
- **Best Practices**: Industry-standard development practices
- **Troubleshooting**: Common issues and solutions
- **Production Deployment**: Real-world deployment strategies

---

**Built with ❤️ for the YouTube Developer Community**

*This project serves as a complete learning resource for modern full-stack development, covering everything from API design to mobile app development and production deployment.*
