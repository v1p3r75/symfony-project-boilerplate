# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

[Describe your project here]

## Common Commands

```bash
# Start the development environment
docker compose up --wait

# Build fresh images
docker compose build --pull --no-cache

# Stop containers
docker compose down --remove-orphans

# Run Symfony console commands
docker compose exec php bin/console <command>

# Run Composer commands
docker compose exec php composer <command>

# Database migrations
docker compose exec php bin/console doctrine:migrations:migrate
docker compose exec php bin/console doctrine:migrations:diff

# Clear cache
docker compose exec php bin/console cache:clear
```

## Architecture

### Modules

```
src/
├── {Module}/             # Your domain modules
│   ├── Controller/
│   ├── DTO/
│   ├── Entity/
│   ├── Repository/
│   ├── Service/
│   └── UseCase/
└── Shared/
    ├── Contract/         # Interfaces for external services
    └── Gateway/          # External service implementations
```

### Structure d'un module

```
src/{Module}/
├── Controller/
├── DTO/
│   ├── Request/
│   └── Response/
├── Entity/
├── Enum/
├── Event/
├── EventListener/
├── Exception/
├── Repository/
├── Service/
└── UseCase/
    └── {Feature}/
        ├── {Feature}Request.php
        └── {Feature}UseCase.php
```

### Doctrine Configuration

Each domain module has its own entity mapping configured in `config/packages/doctrine.yaml`. When adding a new domain with entities, register it under `doctrine.orm.mappings`.

## Key Technical Details

- **PHP 8.4+** required
- **FrankenPHP** with worker mode for high performance
- **Automatic HTTPS** in development (accept self-signed cert at https://localhost)
- **XDebug** available but disabled by default (set `XDEBUG_MODE` env var to enable)
- **UUID** used as entity primary keys (Symfony UID component)
- **PostgreSQL 16** as the database
- **Gedmo Extensions** for Timestampable and SoftDeleteable

## Development Workflow

After finishing a feature:

```bash
# 1. Format code
docker compose exec php composer cs-fix

# 2. Run all tests
docker compose exec php composer test

# 3. If everything passes, commit
git add .
git commit -m "feat: add my feature"
```

## Docker Services

- `php` - FrankenPHP application server (ports 80, 443)
- `database` - PostgreSQL database
- `redis` - Cache and queues
- `pgadmin` - Database management (port 5050)
- `mailpit` - Email testing (dev only, port 8025)
- `minio` - S3 storage (dev only, ports 9000, 9001)

## Conventions

| Layer | Convention | Example |
|-------|------------|---------|
| PHP (Entity/DTO) | camelCase | `$firstName`, `$createdAt` |
| API JSON | camelCase | `firstName`, `createdAt` |
| Database | snake_case (auto) | `first_name`, `created_at` |

### Commits

Format: `<type>: <description>`

| Type | Usage |
|------|-------|
| `feat` | New feature |
| `fix` | Bug fix |
| `refactor` | Refactoring (no functional change) |
| `docs` | Documentation |
| `chore` | Maintenance (deps, config, etc.) |
| `test` | Add/modify tests |
