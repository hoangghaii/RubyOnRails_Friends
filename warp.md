# warp.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is a Rails 8.0+ application named "Friends" with a modern Rails stack including:

- **Ruby Version**: 3.4.5
- **Rails Version**: 8.0.2+
- **Database**: SQLite3 with Solid adapters (Solid Cache, Solid Queue, Solid Cable)
- **Frontend**: Hotwire (Turbo + Stimulus), Import Maps, Propshaft asset pipeline
- **Deployment**: Kamal for Docker-based deployments
- **Code Quality**: RuboCop Rails Omakase, Brakeman security scanning

## Common Commands

### Development Server

```bash
# Start the development server
bin/rails server
# or
bin/dev

# Start Rails console  
bin/rails console
```

### Database Operations

```bash
# Setup database
bin/rails db:setup

# Run migrations
bin/rails db:migrate

# Reset database (drop, create, migrate, seed)
bin/rails db:reset

# Access database console
bin/rails dbconsole
```

### Testing

```bash
# Run all tests
bin/rails test

# Run system tests
bin/rails test:system

# Run specific test file
bin/rails test test/models/some_model_test.rb

# Run tests with specific pattern
bin/rails test -n "/pattern/"

# Prepare test database
bin/rails db:test:prepare
```

### Code Quality & Security

```bash
# Run RuboCop linting
bin/rubocop

# Auto-fix RuboCop issues
bin/rubocop -a

# Security scanning with Brakeman
bin/brakeman

# JavaScript dependency audit
bin/importmap audit
```

### Asset Management

```bash
# Precompile assets
bin/rails assets:precompile

# Update importmap
bin/importmap update
```

### Job Queue (Solid Queue)

```bash
# Process jobs (if not running in Puma)
bin/jobs
```

### Deployment (Kamal)

```bash
# Deploy application
bin/kamal deploy

# Check deployment status
bin/kamal app details

# View logs
bin/kamal logs -f

# Access deployed console
bin/kamal console

# Access shell on deployed container
bin/kamal shell
```

## Architecture Overview

### Rails 8 Modern Stack Features

- **Solid Queue**: Database-backed Active Job adapter for background processing
- **Solid Cache**: Database-backed Rails.cache implementation
- **Solid Cable**: Database-backed Action Cable adapter for WebSockets
- **Propshaft**: Modern asset pipeline replacement for Sprockets
- **Import Maps**: JavaScript module loading without bundling

### Application Structure

```
app/
├── controllers/        # Request handling (inherits from ApplicationController)
├── models/            # Data models (inherits from ApplicationRecord) 
├── views/             # HTML templates
├── helpers/           # View helpers
├── jobs/              # Background jobs (inherits from ApplicationJob)
└── mailers/           # Email handling (inherits from ApplicationMailer)

config/
├── routes.rb          # URL routing configuration
├── application.rb     # Main app configuration (autoload_lib enabled)
├── database.yml       # Database configuration
├── deploy.yml         # Kamal deployment configuration
└── environments/      # Environment-specific settings

test/
├── fixtures/          # Test data
├── models/            # Model tests
├── controllers/       # Controller tests
├── system/            # System/integration tests
└── test_helper.rb     # Test configuration (parallel testing enabled)
```

### Key Configuration Notes

- **Autoloading**: `config.autoload_lib(ignore: %w[assets tasks])` is enabled
- **Modern Browser Support**: `allow_browser versions: :modern` enforced in ApplicationController
- **Parallel Testing**: Configured to use `number_of_processors` workers
- **Container Deployment**: Configured for Docker deployment via Kamal
- **SQLite**: Uses modern SQLite 2.1+ with WAL mode for better concurrency

### Development Patterns

- Follows **Rails Omakase** styling conventions via RuboCop
- Uses **Hotwire** for SPA-like interactions without heavy JavaScript
- **Stimulus** controllers for progressive enhancement
- **Turbo** for fast page updates and form submissions

## CI/CD Pipeline

The GitHub Actions workflow includes:

1. **Security Scanning**: Brakeman for Ruby, importmap audit for JS dependencies  
2. **Code Linting**: RuboCop with Rails Omakase configuration
3. **Testing**: Full test suite including system tests with Chrome
4. **Parallel Execution**: Separate jobs for scanning, linting, and testing

## Environment-Specific Notes

### Development

- Uses SQLite database files
- Hot reloading enabled
- Web console available on exception pages

### Production  

- Deployed via Kamal with Docker containers
- Solid Queue runs within Puma process (`SOLID_QUEUE_IN_PUMA=true`)
- SSL termination via proxy configuration
- Persistent storage volume for SQLite and Active Storage files

### Testing

- Separate test database
- System tests run with headless Chrome
- Screenshots captured on system test failures
- Parallel test execution enabled
