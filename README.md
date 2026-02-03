# iPerform - Production Documentation


## 📚 Documentation Structure

This documentation provides complete coverage of the iPerform application, including architecture, setup, configuration, API reference, and operational guidelines.

### Core Documentation

1. **[Project Overview](01_PROJECT_OVERVIEW.md)**
   - System description and purpose
   - Key features and capabilities
   - Technology stack
   - High-level architecture

2. **[Installation Guide](02_INSTALLATION_GUIDE.md)**
   - Prerequisites and system requirements
   - Docker-based installation
   - Local development setup
   - Production deployment
   - Verification steps

3. **[Architecture](03_ARCHITECTURE.md)**
   - Application flow and request lifecycle
   - Blueprint registration mechanism
   - Layer architecture
   - Integration patterns

4. **[Database Schema](04_DATABASE_SCHEMA.md)**
   - Database structure (iperform and magenta schemas)
   - Table relationships and foreign keys
   - Connection management
   - Dynamic database handling

5. **[Configuration](05_CONFIGURATION.md)**
   - Environment variables reference
   - Role-based access control (RBAC)
   - Feature flags and settings
   - Environment-specific configurations

6. **[API Reference](06_API_REFERENCE.md)**
   - Authentication and session management
   - Available endpoints by module
   - Request/response formats
   - Error handling

7. **[Business Logic](07_BUSINESS_LOGIC.md)**
   - Lead lifecycle management
   - Ticketing system workflow
   - User hierarchy and permissions
   - Notification system

8. **[External Integrations](08_INTEGRATIONS.md)**
   - Facebook Lead Ads
   - WhatsApp Business API
   - AWS SES (Email)
   - Azure Blob Storage
   - OpenAI API
   - Sentry monitoring

9. **[Deployment Guide](09_DEPLOYMENT.md)**
   - Production deployment steps
   - Docker configuration
   - Nginx reverse proxy setup
   - SSL certificate configuration
   - Monitoring and logging

10. **[Operations & Maintenance](10_OPERATIONS.md)**
    - Database maintenance
    - Backup procedures
    - Scaling guidelines
    - Performance tuning
    - Security best practices

11. **[Troubleshooting](11_TROUBLESHOOTING.md)**
    - Common issues and solutions
    - Debug procedures
    - Log analysis
    - FAQ

---

## Quick Links

- **Getting Started**: [Installation Guide →](02_INSTALLATION_GUIDE.md)
- **API Endpoints**: [API Reference →](06_API_REFERENCE.md)
- **Configuration**: [Configuration Guide →](05_CONFIGURATION.md)
- **Common Issues**: [Troubleshooting →](11_TROUBLESHOOTING.md)

---

## Documentation Standards

This documentation follows these principles:
- ✅ **Accuracy**: Based on actual codebase inspection
- ✅ **Completeness**: Covers all major system components
- ✅ **Clarity**: Clear explanations with examples
- ✅ **Maintainability**: Structured for easy updates

---

**Version**: 1.0  
**Last Updated**: February 3, 2026  
**Application**: iPerform CRM & Lead Management System
