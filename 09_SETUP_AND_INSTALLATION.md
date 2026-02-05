# Setup & Installation Guide

## Prerequisites

### **System Requirements**

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| **CPU** | 2 cores | 4+ cores |
| **RAM** | 4 GB | 16 GB |
| **Disk** | 50 GB | 200 GB SSD |
| **OS** | Linux/Windows/macOS | Ubuntu 20.04 LTS or CentOS 8 |
| **Network** | 100 Mbps | 1 Gbps |
| **Docker** | 19.03+ | 20.10+ |
| **Docker Compose** | 1.25+ | 2.0+ |

### **Software Dependencies**

```
Python:           3.7+ (tested on 3.8, 3.9, 3.10)
MySQL:            5.7 or 8.0
ClickHouse:       20.x or later
Git:              2.25+
Docker:           20.10+
Docker Compose:   2.0+
```

---

## Development Environment Setup

### **1. Clone Repository**

```bash
# Clone from GitLab
git clone https://gitlab.com/vikalpsomani/iperform.git
cd iperform

# Verify directory structure
ls -la
# Expected output:
# ├── main/
# ├── iperform_flow/
# ├── static/
# ├── templates/
# ├── config.py
# ├── database.py
# ├── requirements.txt
# ├── Dockerfile
# ├── docker-compose.yml
# └── ...
```

### **2. Environment Configuration**

Create `.env` file in project root:

```bash
# Database Configuration
DB_USER=admin
DB_PASSWORD=your_secure_password
DB_HOST=localhost
DB_PORT=3306
DB_NAME=iperform
DB_ENV=dev                          # dev, test, pro

# ClickHouse Configuration
CLICKHOUSE_DB_USER=default
CLICKHOUSE_DB_PASSWORD=password
CLICKHOUSE_DB_HOST=localhost
CLICKHOUSE_DB_PORT=8123
CLICKHOUSE_TYPE=clickhouse

# Flask Configuration
FLASK_ENV=development
FLASK_APP=wsgi.py
FLASK_DEBUG=1
SECRET_KEY=your-secret-key-here

# AWS Configuration
AWS_ACCESS_KEY_ID=your-aws-key
AWS_SECRET_ACCESS_KEY=your-aws-secret
AWS_S3_BUCKET=extractor-magentabi
AWS_REGION=ap-south-1

# Email Configuration
SES_REGION_NAME=ap-south-1
SES_KEY=your-ses-key
SES_SECRET=your-ses-secret
SES_SENDER_EMAIL=no-reply@magentabi.com

# Magenta Database
CLIENT_DB_USER=admin
CLIENT_DB_PASSWORD=your_password
CLIENT_DB_HOST=localhost
CLIENT_DB_PORT=3306

# iPerform Environment
IPERFORM_ENV=dev
VERIFY_TOKEN_EXP=false

# Security
WEBHOOK_TOKEN_SECRET=your-webhook-secret
JWT_SECRET_KEY=your-jwt-secret
```

### **3. Local Installation (Without Docker)**

#### **Step 1: Install Python Dependencies**

```bash
# Create virtual environment
python3 -m venv venv

# Activate virtual environment
# On Linux/macOS:
source venv/bin/activate
# On Windows:
venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

#### **Step 2: Setup MySQL Database**

```bash
# Install MySQL (if not already installed)
# Ubuntu/Debian:
sudo apt-get install mysql-server

# macOS:
brew install mysql

# Start MySQL service
sudo systemctl start mysql          # Linux
brew services start mysql           # macOS

# Login to MySQL
mysql -u root -p

# Create iPerform database
CREATE DATABASE iperform CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE DATABASE magenta CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

# Create admin user
CREATE USER 'admin'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON iperform.* TO 'admin'@'localhost';
GRANT ALL PRIVILEGES ON magenta.* TO 'admin'@'localhost';
FLUSH PRIVILEGES;

# Exit MySQL
EXIT;
```

#### **Step 3: Initialize Database Schema**

```bash
# Run Flask shell to create tables
python3
>>> from __init__ import app, db
>>> from database import db
>>> with app.app_context():
...     db.create_all()
>>> exit()
```

Or use SQL files if available:

```bash
# Import database schema
mysql -u admin -p iperform < sql/iperform_schema.sql
mysql -u admin -p magenta < sql/magenta_schema.sql
```

#### **Step 4: Setup ClickHouse (Optional but Recommended)**

```bash
# Install ClickHouse
# Ubuntu/Debian:
curl https://repo.clickhouse.com/deb/key.gpg | sudo apt-key add -
echo "deb https://repo.clickhouse.com/deb/stable main" | sudo tee /etc/apt/sources.list.d/clickhouse.list
sudo apt-get update
sudo apt-get install clickhouse-server clickhouse-client

# Start ClickHouse
sudo systemctl start clickhouse-server

# Connect and create databases
clickhouse-client

# Create iPerform analytics schemas
CREATE DATABASE iperform_helper;
CREATE DATABASE extractor;
CREATE TABLE iperform_helper.leads ...;
```

#### **Step 5: Run Application**

```bash
# From project root with venv activated
python3 wsgi.py

# Should see output:
# * Running on http://127.0.0.1:5000
# * Press CTRL+C to quit
```

Access at `http://localhost:5000`

---

## Docker Setup (Recommended)

### **1. Docker Installation**

```bash
# Install Docker (Ubuntu/Debian)
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Verify installation
docker --version
docker-compose --version
```

### **2. Build and Run with Docker Compose**

```bash
# Navigate to project root
cd iperform

# Create .env file (as shown above)
nano .env

# Build images
docker-compose build

# Start services
docker-compose up -d

# Verify containers running
docker-compose ps

# Expected output:
# NAME                  COMMAND                  STATUS
# iperform-web          gunicorn -w 4...         Up
# iperform-mysql        docker-entrypoint.sh...  Up
# iperform-clickhouse   /entrypoint.sh           Up

# Check logs
docker-compose logs -f web
docker-compose logs -f mysql
docker-compose logs -f clickhouse
```

### **3. Docker Compose Services**

**docker-compose.yml** defines:

```yaml
version: '3.8'

services:
  web:
    build: .
    container_name: iperform-web
    ports:
      - "5000:5000"
    environment:
      - FLASK_ENV=development
      - DB_HOST=mysql
      - CLICKHOUSE_DB_HOST=clickhouse
    depends_on:
      - mysql
      - clickhouse
    volumes:
      - .:/home/iperform
    networks:
      - iperform-network

  mysql:
    image: mysql:5.7
    container_name: iperform-mysql
    environment:
      - MYSQL_ROOT_PASSWORD=root_password
      - MYSQL_DATABASE=iperform
      - MYSQL_USER=admin
      - MYSQL_PASSWORD=admin_password
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - iperform-network

  clickhouse:
    image: clickhouse/clickhouse-server:20.11
    container_name: iperform-clickhouse
    environment:
      - CLICKHOUSE_DEFAULT_ACCESS_MANAGEMENT=1
    ports:
      - "8123:8123"
      - "9000:9000"
    volumes:
      - clickhouse_data:/var/lib/clickhouse
    networks:
      - iperform-network

volumes:
  mysql_data:
  clickhouse_data:

networks:
  iperform-network:
    driver: bridge
```

### **4. Post-Deployment Setup**

```bash
# Execute database initialization inside container
docker-compose exec web python3
>>> from __init__ import app, db
>>> with app.app_context():
...     db.create_all()
>>> exit()

# Or run setup script
docker-compose exec web bash /home/iperform/setup.sh

# Create admin user
docker-compose exec web python3 -c "
from main.model.user import User
from database import db
# Create admin user
"
```

---

## Production Deployment

### **1. Server Setup (Ubuntu 20.04 LTS)**

```bash
# Update system
sudo apt-get update && sudo apt-get upgrade -y

# Install required packages
sudo apt-get install -y curl wget git mysql-server nginx certbot python3-certbot-nginx

# Install Docker
curl -fsSL https://get.docker.com | sudo sh

# Add user to docker group
sudo usermod -aG docker $USER
newgrp docker
```

### **2. Clone and Configure**

```bash
# Clone repository to /home
cd /home
sudo git clone https://gitlab.com/vikalpsomani/iperform.git
cd iperform

# Create production .env
sudo nano .env
# Configure with production values:
DB_ENV=pro
IPERFORM_ENV=pro
FLASK_ENV=production
# ... (use strong passwords and real AWS credentials)

# Set permissions
sudo chown -R $USER:$USER /home/iperform
chmod +x docker-entrypoint.sh
```

### **3. Production Docker Compose**

Use `docker-compose.prod.yml`:

```bash
# Build and start
docker-compose -f docker-compose.prod.yml build
docker-compose -f docker-compose.prod.yml up -d

# Verify
docker-compose -f docker-compose.prod.yml ps
```

### **4. Nginx Reverse Proxy**

```bash
# Create Nginx config
sudo nano /etc/nginx/sites-available/iperform

# Configuration:
upstream iperform_app {
    server web:5000;
}

server {
    listen 80;
    server_name iperform.magentabi.com;

    location / {
        proxy_pass http://iperform_app;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

# Enable site
sudo ln -s /etc/nginx/sites-available/iperform /etc/nginx/sites-enabled/

# Test and restart
sudo nginx -t
sudo systemctl restart nginx
```

### **5. SSL Certificate (Let's Encrypt)**

```bash
# Request certificate
sudo certbot certonly --nginx -d iperform.magentabi.com

# Nginx will automatically update to use HTTPS
sudo systemctl restart nginx

# Auto-renewal
sudo systemctl enable certbot.timer
```

### **6. Backup Strategy**

```bash
# Create backup script: /home/iperform/backup.sh
#!/bin/bash

BACKUP_DIR="/backups/iperform"
DATE=$(date +%Y%m%d_%H%M%S)

# MySQL backup
docker-compose exec -T mysql mysqldump -u admin -p$DB_PASSWORD iperform \
    > $BACKUP_DIR/iperform_$DATE.sql

# Upload to S3
aws s3 cp $BACKUP_DIR/iperform_$DATE.sql s3://iperform-backups/

# Cleanup old backups
find $BACKUP_DIR -name "*.sql" -mtime +30 -delete

# Setup cron job
0 2 * * * /home/iperform/backup.sh    # Run daily at 2 AM
```

---

## Monitoring & Health Checks

### **1. Health Check Endpoint**

```bash
# Test health
curl http://localhost:5000/health

# Expected response:
# {
#   "status": "healthy",
#   "database": "connected",
#   "clickhouse": "connected",
#   "timestamp": "2024-02-05T10:30:00Z"
# }
```

### **2. Application Monitoring**

```bash
# View logs in real-time
docker-compose logs -f web

# Watch resource usage
docker stats iperform-web iperform-mysql iperform-clickhouse

# Check container health
docker-compose exec web curl http://localhost:5000/health
```

### **3. Database Monitoring**

```bash
# MySQL
docker-compose exec mysql mysql -u admin -p -e "SHOW STATUS LIKE 'Threads%';"

# ClickHouse
docker-compose exec clickhouse clickhouse-client -q "SELECT version();"
```

---

## Testing Installation

### **1. API Testing**

```bash
# Get login token
curl -X POST http://localhost:5000/i_auth \
  -H "Content-Type: application/json" \
  -d '{
    "username": "admin@iperform.com",
    "password": "encrypted_password"
  }'

# List leads
curl -X GET http://localhost:5000/leads \
  -H "Authorization: Bearer <token>"
```

### **2. Database Testing**

```bash
# MySQL connectivity
docker-compose exec mysql mysql -u admin -p -e "SELECT 1;"

# ClickHouse connectivity
docker-compose exec clickhouse clickhouse-client -q "SELECT 1;"
```

### **3. Run Tests (if available)**

```bash
# Run unit tests
python -m pytest tests/

# Run integration tests
python -m pytest tests/integration/

# Generate coverage report
pytest --cov=main tests/
```

---

## Troubleshooting

### **Common Issues**

| Issue | Cause | Solution |
|-------|-------|----------|
| **Port 5000 already in use** | Another service on port | `sudo lsof -i :5000` then kill process |
| **MySQL connection refused** | MySQL not running | `docker-compose restart mysql` |
| **Permission denied** | File permissions | `chmod -R 755 /home/iperform` |
| **ClickHouse failed to start** | Disk space or memory | Increase Docker memory limit |
| **Slow queries** | Missing indexes | Run `sql/create_indexes.sql` |
| **Out of disk space** | Large log files | Clean old logs: `docker logs --tail 0 container_id` |

### **Debug Mode**

```bash
# Enable Flask debug mode
export FLASK_DEBUG=1
python wsgi.py

# Enable verbose logging
python -c "import logging; logging.basicConfig(level=logging.DEBUG)"

# Check environment variables
docker-compose config
```

---

## Scaling for Production

### **Horizontal Scaling**

```yaml
# Use Docker Swarm or Kubernetes
# Scale web service to 3 instances
docker-compose up -d --scale web=3

# Or with Kubernetes
kubectl scale deployment iperform-web --replicas=3
```

### **Load Balancing**

```nginx
upstream iperform_backend {
    least_conn;
    server web1:5000;
    server web2:5000;
    server web3:5000;
}
```

### **Database Optimization**

```bash
# Enable MySQL replication for read-heavy workloads
# Configure ClickHouse clustering for distributed analytics
# Implement Redis caching layer for frequently accessed data
```

---

## Post-Installation Configuration

### **1. Create First Admin User**

```python
# In Python shell
from main.model.user import User
from main.model.role import Role
from database import db

admin_role = Role.query.filter_by(role_name='ADMIN').first()
admin = User(
    username='admin@iperform.com',
    email='admin@iperform.com',
    password='hashed_password',
    role_id=admin_role.id,
    active=True
)
db.session.add(admin)
db.session.commit()
```

### **2. Configure Company**

```python
from main.model.company import Company

company = Company(
    company_name='Default Company',
    gst_number='18AABCU1234F1Z0',
    active=True
)
db.session.add(company)
db.session.commit()
```

### **3. Setup Email Templates**

```python
from main.model.email_template import EmailTemplate

templates = [
    EmailTemplate(name='demo_scheduled', ...),
    EmailTemplate(name='proposal_sent', ...),
    ...
]
```

### **4. Configure Integrations**

- Set up AWS SES/SMTP credentials
- Configure Extractor connection details
- Setup Facebook Lead Ads API
- Configure webhook endpoints

---

## Next Steps

1. **Access Application**: `http://localhost:5000` (dev) or `https://iperform.magentabi.com` (prod)
2. **Login**: Use admin credentials created in setup
3. **Configure Companies**: Add your companies/clients
4. **Import Leads**: Use CSV import or API
5. **Setup Integrations**: Connect ERP systems
6. **Create Users**: Add team members
7. **Test Workflows**: Verify core functionalities

---

## Support & Resources

- **Documentation**: `/iperform_flow/` directory
- **API Documentation**: `/api/v1/docs`
- **GitHub Issues**: Report bugs and feature requests
- **Email Support**: support@magentabi.com
- **Community Forum**: forum.magentabi.com

---

## Security Checklist

Before production deployment:

- ✅ Change all default passwords
- ✅ Generate strong JWT secret keys
- ✅ Enable HTTPS with valid SSL certificate
- ✅ Configure firewall rules
- ✅ Enable database encryption
- ✅ Set up automated backups
- ✅ Configure monitoring and alerts
- ✅ Review access control policies
- ✅ Enable audit logging
- ✅ Perform security testing
