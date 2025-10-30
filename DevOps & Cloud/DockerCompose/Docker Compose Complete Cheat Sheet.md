# Docker Compose Complete Cheat Sheet

## Table of Contents

- [Overview & Concepts](#overview--concepts)
- [Installation & Setup](#installation--setup)
- [Docker Compose File Structure](#docker-compose-file-structure)
- [Basic Commands](#basic-commands)
- [Service Configuration](#service-configuration)
- [Networking](#networking)
- [Volumes & Storage](#volumes--storage)
- [Environment Variables](#environment-variables)
- [Advanced Features](#advanced-features)
- [Docker Compose v2/v3](#docker-compose-v2v3)
- [Best Practices](#best-practices)
- [Common Use Cases](#common-use-cases)
- [Troubleshooting](#troubleshooting)

## Overview & Concepts

### What is Docker Compose?

- Tool for defining and running multi-container Docker applications
- Uses YAML files to configure application services
- Simplifies orchestration of multiple containers
- Manages dependencies between services

### Key Concepts

- **Service**: A containerized application (web server, database, etc.)
- **Project**: Collection of services defined in a `docker-compose.yml`
- **Compose File**: YAML file defining services, networks, volumes
- **Dependencies**: Relationships between services (depends_on, links)

## Installation & Setup

### Installing Docker Compose

```bash
# Docker Desktop (includes Compose)
# Download from docker.com/products/docker-desktop

# Linux standalone installation
sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Verify installation
docker-compose --version
docker compose version  # v2 syntax
```

### Project Structure

```
my-app/
├── docker-compose.yml
├── docker-compose.override.yml
├── docker-compose.prod.yml
├── backend/
│   ├── Dockerfile
│   └── src/
├── frontend/
│   ├── Dockerfile
│   └── src/
└── database/
    └── init.sql
```

## Docker Compose File Structure

### Basic Structure

```yaml
version: "3.8"

services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"

  database:
    image: postgres:13
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass

volumes:
  db_data:

networks:
  app-network:
    driver: bridge
```

### Version Matrix

```yaml
# Compose v1 (legacy)
version: '2.4'

# Compose v2 (current)
version: '3.8'

# Compose v3.8+ features
# - extends (removed in v3)
# - profiles
# - device requests
```

## Basic Commands

### Lifecycle Management

```bash
# Start services in foreground
docker-compose up
docker compose up  # v2 syntax

# Start in background
docker-compose up -d

# Start specific services
docker-compose up web database

# Stop services
docker-compose down

# Stop and remove volumes
docker-compose down -v

# Stop and remove images
docker-compose down --rmi all

# Restart services
docker-compose restart
docker-compose restart web

# Pause and unpause
docker-compose pause
docker-compose unpause
```

### Service Management

```bash
# List running services
docker-compose ps

# List all services (including stopped)
docker-compose ps -a

# View service logs
docker-compose logs
docker-compose logs web
docker-compose logs -f web  # Follow logs
docker-compose logs --tail=100 web

# Execute command in running container
docker-compose exec web bash
docker-compose exec database psql -U user myapp

# Run one-off commands
docker-compose run --rm web npm test
docker-compose run --rm database bash

# Scale services
docker-compose up --scale web=3 --scale worker=2
```

### Build Operations

```bash
# Build images
docker-compose build
docker-compose build --no-cache
docker-compose build web

# Pull images
docker-compose pull
docker-compose pull --ignore-pull-failures

# View images
docker-compose images

# Remove stopped containers
docker-compose rm

# Remove unused images
docker-compose images -q | xargs docker rmi
```

## Service Configuration

### Basic Service Definition

```yaml
services:
  web:
    # Image or build
    image: nginx:alpine
    # OR build from Dockerfile
    build:
      context: ./web
      dockerfile: Dockerfile
      args:
        BUILD_ENV: production

    # Container name
    container_name: my-web-app

    # Restart policy
    restart: unless-stopped

    # Port mapping
    ports:
      - "80:80"
      - "443:443"

    # Environment variables
    environment:
      NODE_ENV: production
      DATABASE_URL: postgresql://user:pass@db:5432/myapp

    # Environment file
    env_file:
      - .env
      - .env.db

    # Dependencies
    depends_on:
      - database
      - cache

    # Health check
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

### Build Configuration

```yaml
services:
  app:
    build:
      context: . # Build context path
      dockerfile: Dockerfile.dev
      args:
        - HTTP_PROXY=http://proxy.example.com
        - NODE_VERSION=18
      target: builder # Multi-stage build target
      cache_from:
        - myapp:latest
      labels:
        - "com.example.description=My application"

    # Build secrets
    secrets:
      - server-certificate

secrets:
  server-certificate:
    file: ./server.crt
```

### Resource Management

```yaml
services:
  web:
    # CPU and memory limits
    deploy:
      resources:
        limits:
          cpus: "1.0"
          memory: 512M
        reservations:
          cpus: "0.25"
          memory: 128M

    # Memory swap
    mem_limit: 1g
    memswap_limit: 2g
    mem_reservation: 512m

    # CPU shares
    cpus: 1.5
    cpu_shares: 1024
    cpu_quota: 75000 # 75% of CPU
    cpuset: 0,1 # Use specific CPUs

    # Device access
    devices:
      - "/dev/ttyUSB0:/dev/ttyUSB0"

    # Privileged mode
    privileged: false

    # User and group
    user: "1000:1000"
    group_add:
      - dialout
```

## Networking

### Network Configuration

```yaml
services:
  web:
    networks:
      - frontend
      - backend

  database:
    networks:
      - backend

networks:
  frontend:
    driver: bridge
    driver_opts:
      com.docker.network.driver.mtu: 1450
    ipam:
      config:
        - subnet: 172.16.1.0/24

  backend:
    external: true
    name: my-backend-network
```

### Network Aliases & Links

```yaml
services:
  web:
    networks:
      app_net:
        aliases:
          - webapp
          - frontend

  api:
    networks:
      app_net:
        aliases:
          - backend

    # Legacy links (deprecated)
    links:
      - database:db

  database:
    networks:
      app_net:
        aliases:
          - db
          - database
```

### Port Configuration

```yaml
services:
  web:
    ports:
      # Host:Container mapping
      - "80:80"
      - "8080:8080"

      # Specify IP
      - "127.0.0.1:3306:3306"

      # Random host port
      - "3000-4000:80"
      - "9090-9091:8080"

      # Protocol specification
      - "53:53/udp"
      - "80:80/tcp"

      # Host IP only
      - "0.0.0.0:443:443"

    # Expose ports without publishing
    expose:
      - "3000"
      - "8000"
```

## Volumes & Storage

### Volume Configuration

```yaml
services:
  database:
    volumes:
      # Named volume
      - db_data:/var/lib/postgresql/data

      # Host path
      - ./data:/app/data

      # Relative path
      - ../config:/etc/config

      # Read-only volume
      - ./static:/var/www/html:ro

      # Volume with options
      - logs:/var/log/nginx:rw

      # Temporary in-memory volume
      - tmpfs:/tmp:rw,size=100m

    # Volume driver options
    volume_driver: local

    # Tmpfs volumes
    tmpfs:
      - /tmp:size=100m,mode=1777

volumes:
  db_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /path/on/host

  logs:
    driver: local
    labels:
      - "com.example.description=Application logs"

  shared_data:
    external: true
    name: my_shared_volume
```

### Bind Mounts & Consistency

```yaml
services:
  app:
    volumes:
      # Cached mount (macOS/Windows)
      - ./src:/app/src:cached

      # Delegated mount (performance)
      - ./node_modules:/app/node_modules:delegated

      # Consistent (default)
      - ./data:/app/data:consistent

      # Volume with specific options
      - data_volume:/app/data:rw,noexec,nosuid
```

## Environment Variables

### Variable Configuration

```yaml
services:
  web:
    # Inline environment variables
    environment:
      NODE_ENV: production
      DATABASE_URL: postgresql://user:pass@db:5432/myapp
      REDIS_URL: redis://redis:6379
      - DEBUG=1  # Alternative syntax

    # Environment file
    env_file:
      - .env
      - .env.production
      - ./config/database.env

    # Expand shell variables
    environment:
      - HOME=/home/${USER:-default}
      - PATH=${PATH}:/usr/local/bin

    # Use variable substitution
    env_file:
      - ${ENV_FILE:-.env}
```

### Variable Substitution

```yaml
# .env file
COMPOSE_PROJECT_NAME=myapp
DB_PORT=5432
WEB_PORT=80

# docker-compose.yml
services:
  web:
    image: nginx:alpine
    container_name: ${COMPOSE_PROJECT_NAME:-default}_web
    ports:
      - "${WEB_PORT:-8080}:80"
    environment:
      DB_HOST: database
      DB_PORT: ${DB_PORT}

  database:
    image: postgres:13
    ports:
      - "${DB_PORT}:5432"
```

## Advanced Features

### Health Checks

```yaml
services:
  web:
    image: nginx:alpine
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
      start_interval: 5s

    # Disable default health check
    healthcheck:
      disable: true

  database:
    image: postgres:13
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
```

### Profiles

```yaml
services:
  web:
    image: nginx:alpine
    profiles:
      - frontend
      - production

  api:
    image: node:18
    profiles:
      - backend

  database:
    image: postgres:13
    profiles:
      - backend
      - database

  dev-tools:
    image: node:18
    profiles:
      - development
    volumes:
      - ./:/app

  tests:
    image: node:18
    profiles:
      - testing
    command: npm test
```

### Extends & Multiple Files

```yaml
# docker-compose.base.yml
services:
  app:
    image: node:18
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      NODE_ENV: development

# docker-compose.override.yml
services:
  app:
    command: npm run dev
    ports:
      - "3000:3000"
    depends_on:
      - database

  database:
    image: postgres:13
    environment:
      POSTGRES_DB: myapp

# Usage
# docker-compose -f docker-compose.base.yml -f docker-compose.override.yml up
```

### Deployment Configuration

```yaml
services:
  web:
    image: nginx:alpine
    deploy:
      replicas: 3
      update_config:
        parallelism: 2
        delay: 10s
        failure_action: rollback
      rollback_config:
        parallelism: 0
        delay: 0s
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
      placement:
        constraints:
          - node.role == manager
        preferences:
          - spread: node.labels.zone
      resources:
        limits:
          cpus: "0.50"
          memory: 512M
        reservations:
          cpus: "0.25"
          memory: 128M
    configs:
      - source: nginx_config
        target: /etc/nginx/nginx.conf

configs:
  nginx_config:
    file: ./nginx.conf
```

## Docker Compose v2/v3

### v2 Syntax (New)

```bash
# v2 uses 'docker compose' (no hyphen)
docker compose up
docker compose down
docker compose ps

# Project name can be set via:
export COMPOSE_PROJECT_NAME=myapp
# or
docker compose -p myapp up
```

### v1 vs v2 Differences

```bash
# v1 (legacy)
docker-compose up
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up

# v2 (current)
docker compose up
docker compose -f docker-compose.yml -f docker-compose.prod.yml up

# v2 supports profiles natively
docker compose --profile frontend up
```

## Best Practices

### File Organization

```yaml
# Recommended structure
# docker-compose.yml - Base configuration
# docker-compose.override.yml - Development overrides
# docker-compose.prod.yml - Production overrides
# docker-compose.test.yml - Testing configuration

# Use multiple files for different environments
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up
```

### Security Practices

```yaml
services:
  web:
    # Don't run as root
    user: "1000:1000"

    # Read-only root filesystem
    read_only: true

    # Temporary filesystems
    tmpfs:
      - /tmp
      - /var/run

    # Security options
    security_opt:
      - no-new-privileges:true
    cap_drop:
      - ALL
    cap_add:
      - NET_BIND_SERVICE

    # Don't use privileged mode
    privileged: false
```

### Performance Optimization

```yaml
services:
  web:
    # Use specific CPU/memory limits
    deploy:
      resources:
        limits:
          cpus: "1.0"
          memory: 512M

    # Optimize volume mounts
    volumes:
      - ./src:/app/src:cached # macOS
      - ./src:/app/src:delegated # Linux

    # Use health checks
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s

    # Proper restart policies
    restart: unless-stopped
```

## Common Use Cases

### Development Environment

```yaml
version: "3.8"

services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    volumes:
      - ./frontend:/app:delegated
      - /app/node_modules
    environment:
      - NODE_ENV=development
    command: npm start

  backend:
    build: ./backend
    ports:
      - "8000:8000"
    volumes:
      - ./backend:/app:delegated
      - /app/node_modules
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/myapp
      - REDIS_URL=redis://redis:6379
    depends_on:
      - database
      - redis

  database:
    image: postgres:13
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
    volumes:
      - db_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql

  redis:
    image: redis:alpine
    volumes:
      - redis_data:/data

volumes:
  db_data:
  redis_data:
```

### Production Stack

```yaml
version: "3.8"

services:
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - static_volume:/var/www/static
      - ./ssl:/etc/nginx/ssl:ro
    depends_on:
      - web
    restart: always

  web:
    image: myapp:latest
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://user:pass@db:5432/myapp
    volumes:
      - static_volume:/app/static
    restart: always
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s

  database:
    image: postgres:13
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
    volumes:
      - db_data:/var/lib/postgresql/data
      - ./backups:/backups
    restart: always

  redis:
    image: redis:alpine
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    restart: always

volumes:
  db_data:
  redis_data:
  static_volume:
```

### Microservices

```yaml
version: "3.8"

services:
  gateway:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./gateway.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - users-service
      - orders-service

  users-service:
    build: ./services/users
    environment:
      - DB_URL=postgresql://user:pass@users-db:5432/users
    deploy:
      replicas: 2

  orders-service:
    build: ./services/orders
    environment:
      - DB_URL=postgresql://user:pass@orders-db:5432/orders
      - REDIS_URL=redis://redis:6379
    deploy:
      replicas: 3

  users-db:
    image: postgres:13
    environment:
      POSTGRES_DB: users
    volumes:
      - users_data:/var/lib/postgresql/data

  orders-db:
    image: postgres:13
    environment:
      POSTGRES_DB: orders
    volumes:
      - orders_data:/var/lib/postgresql/data

  redis:
    image: redis:alpine

volumes:
  users_data:
  orders_data:

networks:
  default:
    driver: bridge
```

## Troubleshooting

### Common Issues & Solutions

```bash
# View detailed logs
docker-compose logs --tail=100 -f service_name

# Check service status
docker-compose ps
docker-compose top

# Inspect service configuration
docker-compose config
docker-compose config --services

# Force rebuild
docker-compose build --no-cache
docker-compose up --force-recreate

# Check network connectivity
docker-compose exec service_name ping other_service

# View network details
docker network ls
docker network inspect project_name_default

# Clean up
docker-compose down -v --remove-orphans
docker system prune -f

# Environment variable issues
docker-compose config | grep -A5 -B5 VARIABLE_NAME
```

### Debug Commands

```bash
# Validate compose file
docker-compose config

# See what would be created
docker-compose up --dry-run

# Check service dependencies
docker-compose ps --services

# Execute debug shell
docker-compose exec service_name sh
docker-compose exec service_name bash

# Copy files to/from container
docker-compose cp service_name:/path/to/file ./local/path
docker-compose cp ./local/file service_name:/path/

# Monitor resource usage
docker-compose top
docker stats $(docker-compose ps -q)
```

### Performance Monitoring

```bash
# Resource usage
docker stats $(docker-compose ps -q)

# Container processes
docker-compose top

# Log analysis
docker-compose logs --timestamps | grep ERROR

# Network inspection
docker network inspect $(docker-compose ps -q | head -1)

# Volume usage
docker system df
docker volume ls
```

---

## Quick Reference Card

### Essential Commands

```bash
# Start services
docker-compose up -d

# Stop services
docker-compose down

# View logs
docker-compose logs -f

# Restart services
docker-compose restart

# Build images
docker-compose build
```

### Common Patterns

```bash
# Development with rebuild
docker-compose up --build

# Production with multiple files
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up -d

# Run one-off commands
docker-compose run --rm web npm test

# Scale services
docker-compose up --scale web=3

# Environment specific
docker-compose --env-file .env.production up
```

### File Structure Template

```yaml
version: "3.8"

services:
  service-name:
    image: name:tag
    build: .
    ports:
      - "host:container"
    environment:
      KEY: value
    volumes:
      - host_path:container_path
    depends_on:
      - other-service

volumes:
  volume-name:

networks:
  network-name:
```

---

_Remember: Always test your Compose files with `docker-compose config` before running, and use version control for your `docker-compose.yml` files!_
