# Installation & Setup Guide

## Docker setup (recommended)

1. Install Docker and Docker Compose.
2. From the project root, create a `.env` file with required settings.
3. Create the MySQL database.
4. Build the images:
	```bash
	docker compose build --no-cache
	```
5. Start the services:
	```bash
	docker compose up
	```
6. Stop the services when needed:
	```bash
	docker compose down
	```
7. Verify the app is running at `http://localhost:4444`.

## .env demo (copy/paste)

```env
# COMPOSE_PROJECT_NAME=iperform
# IPERFORM_ENV=test
# # dev | test | pro
# DB_ENV=pro
# # dev | test | pro | beta
# CLIENT_DB_ENV=local

COMPOSE_PROJECT_NAME=iperform
IPERFORM_ENV=test
DB_ENV=test
CLIENT_DB_ENV=local

DB_USER=your_db_user
DB_PASSWORD=your_db_password
DB_HOST=your_db_host
DB_PORT=3306
DB_NAME=iperform

CLICKHOUSE_DB_USER=your_clickhouse_user
CLICKHOUSE_DB_PASSWORD=your_clickhouse_password
CLICKHOUSE_DB_HOST=your_clickhouse_host
CLICKHOUSE_DB_PORT=8123
CLICKHOUSE_TYPE=clickhouse

ENV_DEC=your_secret_key
MAGENTA_EMAIL_PASSWORD=your_email_password
MAILMODO_API_KEY=your_mailmodo_api_key
TOKEN_ENC_STRING=your_token_enc_string

PRINT_ENABLED=true
```

## Manual setup

1. Install Python 3.9 and MySQL 8.
2. From the project root, create and activate a virtual environment, then install dependencies:
	```bash
	python -m venv venv
	venv\Scripts\activate
	pip install -r requirements.txt
	```
3. Create a `.env` file with required settings.
4. Create the MySQL database.
5. Start the app:
	```bash
	python wsgi.py
	```

---

**Previous**: [← Project Overview](01_PROJECT_OVERVIEW.md)
**Next**: [How It Works →](03_ARCHITECTURE.md)
