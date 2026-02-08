# Symfony Project Boilerplate

Symfony 8.0 project template with modular architecture, Docker, and complete development tooling.

## Features

- **Symfony 8.0** with PHP 8.4+
- **FrankenPHP** with worker mode for high performance
- **Docker Compose** multi-environment setup (dev/prod)
- **PostgreSQL 16** database
- **Redis 7** for caching and queues
- **Modular architecture** with domain-driven structure
- **Gedmo Doctrine Extensions** (Timestampable, SoftDeleteable)
- **PHPStan** for static analysis
- **PHP-CS-Fixer** for code style
- **PHPUnit** for testing (Integration + Functional)
- **GitHub Actions CI** pipeline

## Quick Start

```bash
# Clone this repository
git clone <repository-url> my-project
cd my-project

# Start the development environment
docker compose up --wait

# Install dependencies
docker compose exec php composer install

# Create database and run migrations
docker compose exec php bin/console doctrine:database:create --if-not-exists
docker compose exec php bin/console doctrine:migrations:migrate --no-interaction

# Access the application
# HTTP:  http://localhost
# HTTPS: https://localhost (accept self-signed cert)
```

## Docker Services

| Service | Description | Ports |
|---------|-------------|-------|
| php | FrankenPHP application server | 80, 443 |
| database | PostgreSQL 16 | 5432 |
| redis | Redis 7 for cache/queues | 6379 |
| pgadmin | Database management | 5050 |
| mailpit | Email testing (dev) | 8025, 1025 |
| minio | S3-compatible storage (dev) | 9000, 9001 |

## Project Structure

```
src/
├── Example/              # Example module (rename for your domain)
│   ├── Controller/
│   ├── DTO/
│   │   ├── Request/
│   │   └── Response/
│   ├── Entity/
│   ├── Enum/
│   ├── Event/
│   ├── EventListener/
│   ├── Exception/
│   ├── Repository/
│   ├── Service/
│   └── UseCase/
│       └── {Feature}/
│           ├── {Feature}Request.php
│           └── {Feature}UseCase.php
└── Shared/
    ├── Contract/         # Interfaces for external services
    ├── DTO/
    └── Gateway/          # External service implementations
```

## Adding a New Module

1. Create the folder `src/{ModuleName}/` with the structure above
2. Register the Doctrine mapping in `config/packages/doctrine.yaml`:
   ```yaml
   doctrine:
       orm:
           mappings:
               ModuleName:
                   type: attribute
                   is_bundle: false
                   dir: '%kernel.project_dir%/src/ModuleName/Entity'
                   prefix: 'App\ModuleName\Entity'
                   alias: ModuleName
   ```

## Development Workflow

```bash
# Format code
docker compose exec php composer cs-fix

# Run all tests
docker compose exec php composer test

# If everything passes, commit
git add .
git commit -m "feat: add my feature"
```

## Available Scripts

| Command | Description |
|---------|-------------|
| `composer cs-fix` | Fix code style |
| `composer cs-check` | Check code style (dry-run) |
| `composer phpstan` | Static analysis |
| `composer test:integration` | Integration tests |
| `composer test:functional` | Functional tests |
| `composer test` | Run all tests |

## Useful Commands

```bash
# Symfony console
docker compose exec php bin/console <command>

# Generate migration
docker compose exec php bin/console doctrine:migrations:diff

# Run migrations
docker compose exec php bin/console doctrine:migrations:migrate

# Clear cache
docker compose exec php bin/console cache:clear

# Access database
docker compose exec database psql -U app -d app
```

## Conventions

| Layer | Convention | Example |
|-------|------------|---------|
| PHP (Entity/DTO) | camelCase | `$firstName`, `$createdAt` |
| API JSON | camelCase | `firstName`, `createdAt` |
| Database | snake_case (auto) | `first_name`, `created_at` |

## License

MIT
