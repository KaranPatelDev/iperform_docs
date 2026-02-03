# Project Overview

## What is iPerform?

iPerform is a CRM and lead-management backend designed to capture, track, and act on customer leads across channels. It provides APIs for lead workflows, activity logging, communication, reporting, and support operations.

## What the system does

Core capabilities reflected in the codebase:

- **Lead management**: Create, update, search, and track leads, including bulk import and status changes.
- **Activity and follow-up tracking**: Log interactions and schedule follow-ups tied to leads and users.
- **Support ticketing**: Create and manage tickets, assignments, status changes, and communications.
- **Messaging**: WhatsApp messaging and template management, plus email-related utilities.
- **Facebook lead ingestion**: Import Facebook lead ads and map them into internal lead records.
- **Reporting**: Generate reports and custom report datasets for analytics and dashboards.

## Architecture overview

- **Backend framework**: Flask, organized around modular Blueprints in [main/api](../main/api).
- **Data access**: SQLAlchemy models in [main/model](../main/model) with MySQL as the primary store.
- **Utilities**: Shared helpers and decorators for authentication, validation, logging, and integrations in [main/utills](../main/utills).
- **Configuration**: Centralized settings in [config.py](../config.py).

## Integrations referenced in code

- **Facebook**: Lead capture via Facebook lead ads.
- **WhatsApp**: Messaging workflows and template management.
- **Email services**: Email sending utilities, including AWS SES configuration.
- **OpenAI**: Client configured for AI-assisted features.
- **ClickHouse**: Analytics/BI workflows in reporting utilities.

---
**Next**: [Installation Guide →](02_INSTALLATION_GUIDE.md)
