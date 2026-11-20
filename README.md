Loyalty SaaS Platform
A multi-tenant, white-label loyalty platform designed to help businesses launch and manage branded customer loyalty programmes. The platform is being developed as a modular monolith, with a Laravel backend and React frontend, supported by MySQL, Redis, and Docker-based infrastructure.
> **Project status:** Active development. This repository contains application source code and database migrations. Features and integrations remain subject to implementation and verification.
Project Overview
The platform is intended to provide a shared SaaS environment in which multiple businesses can operate independently while using common application and database infrastructure.
Each business has tenant-scoped data and business-level access controls. Platform administration is separated from business-user access.
Main objectives
Provide a configurable, white-label loyalty platform for multiple businesses.
Support business-specific loyalty programmes, cards, memberships, stamps, rewards, and redemptions.
Maintain tenant data separation and enforce business ownership relationships.
Support separate platform-level and business-level access models.
Enable digital loyalty passes through Apple Wallet and Google Wallet integrations.
Maintain reliable financial, consent, and audit records.
Key Capabilities
Area	Scope
Multi-tenancy	Shared database with business-level tenant ownership and isolation controls
Platform administration	Separate platform users, roles, and permissions
Business access	Business users and role-based access relationships
Loyalty programmes	Programmes, cards, memberships, stamps, and rewards
Redemptions	Redemption records and related approval/ownership checks
Digital wallets	Wallet accounts, passes, and onboarding relationships
Billing and subscriptions	Subscription, payment, invoice, and refund records
Auditability	Platform and business audit records
Background processing	Redis-backed queue infrastructure
The table describes the platform's domain scope; it does not imply that every workflow or external integration is production-ready.
Technology Stack
Layer	Technology
Backend	PHP, Laravel
Frontend	React
Database	MySQL 8
Cache / Queue	Redis
Web server	Nginx
Containers	Docker and Docker Compose
Digital wallet integrations	Apple Wallet and Google Wallet
Architecture
The application follows a modular monolith approach. Business capabilities are organized into application domains while sharing common deployment and core infrastructure.
Multi-tenant data model
A shared MySQL database is used by the application.
Business-owned records are associated with a `business_id` tenant identifier where applicable.
Application-level ownership checks complement database relationships.
ULIDs are used for primary identifiers in relevant domain tables.
Timestamps are stored in UTC.
Monetary values use fixed-precision decimal fields, with ISO currency codes.
Access-control separation
The platform distinguishes between:
Platform users: users responsible for platform-level administration and operations.
Business users: users who access and manage their own business environment.
Role and permission relationships are represented separately for platform access. Authorization must also be enforced at route, service, and resource level.
Repository Structure
```text
loyalty-saas/
├── backend/                 # Laravel application
│   ├── app/                 # Application code and domain modules
│   ├── database/            # Migrations, factories, and seeders
│   ├── routes/              # Application routes
│   ├── tests/               # Automated tests
│   └── ...
├── frontend/                # React application
├── infrastructure/
│   ├── docker/              # Docker build configuration
│   └── nginx/               # Nginx configuration
├── docker-compose.yml       # Local service orchestration
└── README.md
```
Some directories or files may evolve as development continues.
Local Development Setup
Prerequisites
Git
Docker Desktop with Docker Compose
A code editor such as Visual Studio Code
1. Clone the repository
```bash
git clone https://github.com/Misbah-code/loyalty-saas.git
cd loyalty-saas
```
If the repository is private, the GitHub account used to clone it must have access.
2. Configure environment files
Create the root and backend environment files from the example templates, if provided:
```bash
cp .env.example .env
cp backend/.env.example backend/.env
```
On Windows PowerShell:
```powershell
Copy-Item .env.example .env
Copy-Item backend/.env.example backend/.env
```
Review the environment variables and configure the required database credentials and application settings. The root `.env` must contain the database variables referenced by `docker-compose.yml`, including:
`DB_PASSWORD`
`DB_ROOT_PASSWORD`
Configure the backend database connection to match the Docker Compose MySQL service. Never commit real passwords, API keys, tokens, or other secrets.
> If an example file is not present in your checkout, obtain the correct template from the project maintainer rather than guessing secret values.
3. Build and start the services
```bash
docker compose up -d --build
```
Check service status:
```bash
docker compose ps
```
The current Compose configuration maps the local web service to `http://localhost:8080`. MySQL and Redis ports are bound to localhost for local development.
4. Install backend dependencies
```bash
docker compose exec app sh
cd /var/www/backend
composer install
```
5. Generate the Laravel application key
If the backend environment does not already contain a valid application key:
```bash
docker compose exec app php artisan key:generate
```
6. Run database migrations
After confirming that the backend environment points to the intended development database:
```bash
docker compose exec app php artisan migrate
```
Database safety: Never run destructive reset commands against an existing project database. In particular, do not use `migrate:fresh`, `db:wipe`, table truncation, or Docker volume deletion as routine setup or troubleshooting steps. Review migrations and database targets before applying schema changes.
7. Run automated tests
```bash
docker compose exec app sh -lc "cd /var/www/backend && php artisan test"
```
Tests that require a database should use an isolated test database. Do not grant the runtime application database user schema-creation privileges merely to make tests pass.
Database Readiness and Engineering Principles
Database integrity and tenant isolation are key engineering priorities. The readiness work includes:
Tenant ownership and cross-business relationship validation.
Payment, subscription, invoice, and refund record protection.
Loyalty transaction history and reversal/adjustment handling.
Consent history preservation.
Audit-log integrity.
Wallet credential and customer-data protection.
Platform/business authentication and authorization separation.
Data retention, backups, and reporting performance.
The current project tracking records the first nine items of the 26-item master database-readiness checklist as verified based on project-owner confirmation. Item 10, covering financial-record protections, remains in progress. This is a development tracking status, not a claim of independent security certification or production readiness.
Security Notes
Keep `.env` files and credentials out of Git.
Use least-privilege database accounts.
Do not expose database or Redis services publicly.
Enforce tenant ownership checks in application services and endpoints.
Apply authorization checks to sensitive administrative and financial operations.
Preserve financial and audit history; use controlled reversals or adjustments rather than rewriting historical transactions.
Verify payment-provider webhooks and enforce idempotency before relying on webhook events.
Protect wallet credentials and customer tokens appropriately.
Review configuration and security controls before production deployment.
Development Workflow
A recommended contribution workflow:
Create a feature branch from `main`.
Implement a focused change.
Add or update automated tests.
Run relevant tests and inspect the changes.
Commit with a descriptive message.
Push the branch and open a pull request for review.
Example:
```bash
git switch main
git pull origin main
git switch -c feature/short-description

# Make and test your changes

git add .
git commit -m "Add short description of change"
git push -u origin feature/short-description
```
Avoid committing environment files, database dumps, generated dependencies, or private customer/business data.
Current Development Status
The project is under active development. The repository includes the Laravel backend, React frontend, infrastructure configuration, database migrations, and automated tests.
The database-readiness checklist is used to track tenant isolation, financial integrity, security, and operational improvements. Refer to the latest project delivery/readiness plan for detailed item-by-item status.
External payment gateways and Apple/Google Wallet integrations require configuration, provider credentials, end-to-end testing, and production approval.
Author
Misbah Msaad Hamid  
IT Project Coordinator | Software Project Delivery | Business Analysis
GitHub: Misbah-code
---
Disclaimer
This repository is a work in progress and is shared for technical review and portfolio purposes. Do not use it as a production deployment guide without completing environment-specific configuration, security review, integration testing, operational readiness checks, and deployment approval.
