# Production Deployment Guide

This guide provides the essential steps to deploy the iPerform application for live, production use.

## Pre-Deployment Checklist

Before you begin, ensure the following are ready:

-   **Server**: A cloud instance (like AWS, Azure, Google Cloud) or a physical on-premise server.
-   **Database**: A production-ready database (e.g., PostgreSQL, MySQL). A managed cloud database is recommended.
-   **Domain Name**: A registered domain (e.g., `crm.yourcompany.com`) pointed to your server's IP address.
-   **SSL Certificate**: To enable secure `https://` access. Let's Encrypt provides free certificates.
-   **Email Service**: An SMTP service for sending transactional emails (e.g., SendGrid, Amazon SES).

---

## Option 1: Cloud Deployment (Recommended)

Hosting on a cloud provider is flexible, scalable, and reliable.

### Cloud Setup Steps:

1.  **Provision Server & Database**:
    *   Launch a cloud server (e.g., AWS EC2, Azure VM) with a Linux OS (like Ubuntu 20.04). A server with at least 2 CPU cores and 4 GB RAM is a good starting point.
    *   Set up a managed database service (e.g., AWS RDS, Azure Database).

2.  **Configure DNS & SSL**:
    *   Point your domain name to the server's public IP address.
    *   Log in to your server via SSH and install an SSL certificate using a tool like Certbot.

3.  **Deploy the Application (Using Docker)**:
    *   Ensure Docker and Docker Compose are installed on your server.
    *   Copy the `docker-compose.prod.yml` and production environment files to your server.
    *   Update the environment file with your database credentials, domain name, and email service settings.
    *   Run the command `docker-compose -f docker-compose.prod.yml up -d` to start the application in the background.

4.  **Initial Configuration**:
    *   Access your domain (`https://crm.yourcompany.com`) in a web browser.
    *   Log in with the default administrator credentials.
    *   Change the admin password immediately.
    *   Create user accounts, assign roles, and configure essential system settings.

---

## Option 2: On-Premise Deployment

This option gives you full control but requires more maintenance.

### On-Premise Setup Steps:

1.  **Prepare Infrastructure**:
    *   Set up a physical or virtual server with a Linux OS.
    *   Install and configure a production database.
    *   Ensure the server has a static IP address and is connected to a reliable network.
    *   Configure your firewall to allow web traffic (ports 80 and 443).

2.  **Deploy the Application**:
    *   Follow the same deployment steps as **Cloud Deployment (Step 3)**, using Docker. This is the most reliable method.
    *   Alternatively, you can manually install all dependencies (Python, Gunicorn, Nginx, etc.) and configure them to run the application.

3.  **Configure Backups**:
    *   This is **your responsibility**.
    *   Set up a script or use a tool to perform regular (at least daily) backups of your database and uploaded files.
    *   Store backups in a separate, secure location (preferably off-site).

4.  **Initial Configuration**:
    *   Follow the same steps as **Cloud Deployment (Step 4)**.

---

## Post-Deployment Essentials

-   **Create Admin & User Accounts**: Set up accounts for your team and assign the correct permissions.
-   **Configure Integrations**: Connect to essential services like your email provider, Facebook Lead Ads, and WhatsApp Business API in the admin settings.
-   **Set Up Backups**: **CRITICAL**. Ensure automated daily backups of your database and files are running and tested. For cloud deployments, configure this in your provider's dashboard. For on-premise, this is a manual setup.
-   **Monitor the System**: Regularly check server logs and application health to ensure everything is running smoothly.

---

**Previous**: [← External Integrations](08_INTEGRATIONS.md)  
**Next**: [Daily Operations →](10_OPERATIONS.md)
