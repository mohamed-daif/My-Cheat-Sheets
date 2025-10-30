# Docker Compose v3 Complete Cheat Sheet

## Table of Contents

- [Overview & v3 Changes](#overview--v3-changes)
- [Installation & v2 CLI](#installation--v2-cli)
- [Compose File v3 Specification](#compose-file-v3-specification)
- [Basic Commands (v2 CLI)](#basic-commands-v2-cli)
- [Service Configuration v3](#service-configuration-v3)
- [Networking v3](#networking-v3)
- [Volumes & Storage v3](#volumes--storage-v3)
- [Secrets & Configs](#secrets--configs)
- [Deploy & Swarm Mode](#deploy--swarm-mode)
- [Advanced v3 Features](#advanced-v3-features)
- [Best Practices v3](#best-practices-v3)
- [Migration Guide](#migration-guide)
- [Troubleshooting](#troubleshooting)

## Overview & v3 Changes

### What's New in Compose v3

- **Native Docker CLI integration** (`docker compose` instead of `docker-compose`)
- **Better Swarm mode integration**
- **Removed legacy options**: `volume_driver`, `cpu_shares`, `cpu_quota`, `cpuset`
- **New `deploy` section** for Swarm-specific configurations
- **Improved resource management**
- **Enhanced networking capabilities**

### v3 Version Matrix

```yaml
# Supported versions
version: '3.8'    # Current recommended
version: '3.9'    # Added start_period in healthcheck
version: '3.10'   # Added rollback_config in deploy
```

## Installation & v2 CLI

### Docker Compose v2 (Current)

```bash
# Included with Docker Desktop (recommended)
# For Linux systems:
sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Verify installation
docker compose version
# Output: Docker Compose version v2.24.0
```

### Legacy v1 vs v2 Commands

```bash
# Legacy v1 (python, deprecated)
docker-compose up
docker-compose down

# Current v2 (Go, integrated with Docker CLI)
docker compose up
docker compose down

# v2 supports both syntaxes for compatibility
docker-compose up  # Still works in v2
docker compose up  # Preferred syntax
```

## Compose File v3 Specification

### Basic v3 Structure

```yaml
# docker-compose.yml
version: "3.8" # Optional in newer versions

services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    deploy:
      resources:
        limits:
          memory: 512M
          cpus: "1.0"

  database:
    image: postgres:15
    environment:
      POSTGRES_DB: myapp
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:

networks:
  default:
    driver: bridge
```

### Minimal v3.8+ Format

```yaml
# version field is now optional in Docker Compose v2
services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"

  app:
    build: .
    environment:
      NODE_ENV: production
```

## Basic Commands (v2 CLI)

### Project Management

```bash
# Start all services
docker compose up
docker compose up -d  # Detached mode

# Start specific services
docker compose up web database

# Stop services
docker compose down
docker compose down -v  # Remove volumes
docker compose down --rmi all  # Remove images

# View project status
docker compose ps
docker compose ls  # List running compose projects

# Project-specific operations
docker compose -p myproject up  # Specify project name
export COMPOSE_PROJECT_NAME=myapp  # Environment variable
```

### Service Operations

```bash
# Service lifecycle
docker compose start
docker compose stop
docker compose restart
docker compose pause
docker compose unpause

# Scale services
docker compose up --scale web=3 --scale worker=2

# View logs
docker compose logs
docker compose logs -f web  # Follow specific service
docker compose logs --tail=50 web

# Execute commands
docker compose exec web bash
docker compose exec database psql -U user myapp

# Run one-off commands
docker compose run --rm web npm test
docker compose run --rm -p 3000:3000 web bash
```

### Build & Image Management

```bash
# Build images
docker compose build
docker compose build --no-cache
docker compose build --pull  # Always pull base images

# Pull images
docker compose pull
docker compose pull --ignore-pull-failures

# Push images
docker compose push

# List images
docker compose images
```

## Service Configuration v3

### Complete Service Definition

```yaml
services:
  web:
    # Image or build (mutually exclusive)
    image: nginx:alpine
    # OR
    build:
      context: .
      dockerfile: Dockerfile.prod
      args:
        BUILD_VERSION: 1.0.0
      target: runtime
      cache_from:
        - myapp:latest
      labels:
        - "build-date=2024-01-01"

    # Container configuration
    container_name: my-web-app
    hostname: web-container
    domainname: example.local

    # Restart policy
    restart: unless-stopped # no, always, on-failure, unless-stopped

    # Runtime and capabilities
    runtime: runc
    init: true # Use init process as PID 1
    read_only: true # Read-only root filesystem
    tmpfs:
      - /tmp:size=100m,mode=1777
    security_opt:
      - no-new-privileges:true
    cap_add:
      - NET_ADMIN
    cap_drop:
      - ALL

    # Health check (v3.4+)
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s # v3.4+
      start_interval: 5s # v3.11+

    # Configuration management
    configs:
      - source: app_config
        target: /app/config.yaml
        uid: "1000"
        gid: "1000"
        mode: 0440

    # Secrets management
    secrets:
      - db_password
      - source: api_key
        target: /run/secrets/api_key
        mode: 0400
```

### Resource Management

```yaml
services:
  app:
    # Resource limits (standalone)
    mem_limit: 512m
    mem_reservation: 256m
    cpus: 1.5

    # OR use deploy section (Swarm and standalone)
    deploy:
      resources:
        limits:
          cpus: "1.0"
          memory: 512M
          pids: 100 # Process limit
        reservations:
          cpus: "0.25"
          memory: 128M

      # Update configuration
      update_config:
        parallelism: 2
        delay: 10s
        failure_action: rollback
        order: start-first

      # Rollback configuration
      rollback_config:
        parallelism: 0
        delay: 0s
        failure_action: pause
        order: stop-first

      # Restart policy
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s

      # Placement constraints
      placement:
        constraints:
          - node.role == manager
          - node.labels.zone == east
        preferences:
          - spread: node.labels.datacenter

      # Replicas
      replicas: 3
```

## Networking v3

### Network Configuration

```yaml
services:
  web:
    networks:
      - frontend
      - backend
    ports:
      - "80:80"
      - "443:443"
      - "8080-8090:8080-8090" # Port ranges

  api:
    networks:
      backend:
        aliases:
          - api-service
          - backend-api
    expose:
      - "3000" # Expose without publishing

  database:
    networks:
      - backend
    ports:
      - target: 5432
        published: 5432
        protocol: tcp
        mode: host # host, ingress

networks:
  frontend:
    driver: bridge
    driver_opts:
      com.docker.network.driver.mtu: 1500
    ipam:
      driver: default
      config:
        - subnet: 172.20.0.0/16
          gateway: 172.20.0.1
    internal: false
    attachable: true
    labels:
      - "environment=production"

  backend:
    external: true
    name: my-existing-network

  default:
    driver: bridge
```

### Advanced Networking

```yaml
services:
  app:
    network_mode: "bridge"  # bridge, host, none, service:name, container:name
    # OR
    network_mode: "host"  # Use host network directly
    # OR
    network_mode: "service:database"  # Share network namespace

    ports:
      - target: 80
        published: 8080
        protocol: tcp
        mode: host  # host, ingress (Swarm)

    # Extra hosts
    extra_hosts:
      - "api.example.com:10.0.1.100"
      - "cache.example.com:10.0.1.101"
```

## Volumes & Storage v3

### Volume Configuration

```yaml
services:
  database:
    volumes:
      # Named volume
      - db_data:/var/lib/postgresql/data

      # Bind mount
      - ./data:/app/data:rw

      # Volume with options
      - logs:/var/log:rw,noexec,nosuid

      # Anonymous volume
      - /tmp

      # Read-only volume
      - config:/etc/config:ro

      # Volume from another service
      - shared_data:/app/shared

      # Tmpfs volume
      - type: tmpfs
        target: /cache
        tmpfs:
          size: 1000000000  # 1GB

      # Bind mount with propagation
      - type: bind
        source: ./src
        target: /app/src
        bind:
          propagation: rshared

    # Volume driver for service
    volumes:
      - data_volume:/data

volumes:
  db_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/data
      # For NFS
      # type: nfs
      # o: addr=192.168.1.1,rw
      # device: ":/path/to/nfs/share"

  logs:
    driver: local
    labels:
      - "env=prod"

  shared_data:
    external: true
    name: shared_storage

  data_volume:
    driver: local-persist
    driver_opts:
      mountpoint: /mnt/volumes/data
```

### Storage Optimizations

```yaml
services:
  app:
    volumes:
      # Performance optimizations for different OS
      - ./src:/app/src:cached # macOS
      - ./src:/app/src:delegated # Linux
      - ./src:/app/src:consistent # Windows

      # Volume with size limit
      - type: volume
        source: large_data
        target: /data
        volume:
          nocopy: true # Disable copying from container

    # Tmpfs configuration
    tmpfs:
      - /tmp:size=100m,mode=1777
      - /run:size=50m,mode=1777,uid=1000

volumes:
  large_data:
    driver: local
    driver_opts:
      type: xfs
      o: "pquota=1"
```

## Secrets & Configs

### Secrets Management

```yaml
services:
  database:
    image: postgres:15
    secrets:
      - db_password
      - source: ssl_cert
        target: /run/secrets/ssl_cert.pem
        mode: 0444
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password

  app:
    image: myapp:latest
    secrets:
      - api_key
      - db_password

secrets:
  db_password:
    file: ./secrets/db_password.txt
    # OR external secret
    # external: true
    # name: production_db_password

  api_key:
    environment: API_KEY # From environment variable

  ssl_cert:
    file: ./ssl/certificate.pem

  # For Swarm mode
  swarm_secret:
    external: true
    name: my_swarm_secret
```

### Configs Management

```yaml
services:
  web:
    image: nginx:alpine
    configs:
      - source: nginx_conf
        target: /etc/nginx/nginx.conf
        mode: 0444
      - source: site_config
        target: /etc/nginx/conf.d/default.conf

  app:
    image: myapp:latest
    configs:
      - app_config

configs:
  nginx_conf:
    file: ./nginx/nginx.conf
    labels:
      - "service=web"

  site_config:
    content: |
      server {
        listen 80;
        server_name localhost;
        location / {
          proxy_pass http://app:3000;
        }
      }

  app_config:
    external: true
    name: production_app_config
```

## Deploy & Swarm Mode

### Swarm Deploy Configuration

```yaml
services:
  web:
    image: nginx:alpine
    deploy:
      # Replica mode
      replicas: 3
      # OR global mode
      # mode: global

      # Resource management
      resources:
        limits:
          cpus: "0.50"
          memory: 512M
        reservations:
          cpus: "0.25"
          memory: 128M

      # Update strategy
      update_config:
        parallelism: 2
        delay: 10s
        failure_action: rollback
        monitor: 60s
        max_failure_ratio: 0.3
        order: start-first

      # Rollback strategy
      rollback_config:
        parallelism: 1
        delay: 5s
        failure_action: pause
        monitor: 30s
        max_failure_ratio: 0.1
        order: stop-first

      # Restart policy
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s

      # Placement constraints
      placement:
        constraints:
          - node.role == worker
          - node.labels.zone == east
          - engine.labels.operatingsystem == ubuntu
        preferences:
          - spread: node.labels.datacenter

      # Service labels
      labels:
        - "com.example.description=Web server"
        - "traefik.enable=true"

      # Endpoint mode
      endpoint_mode: vip # vip, dnsrr

    # Health check for Swarm
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 60s
```

### Stack Deployments

```bash
# Deploy to Swarm
docker stack deploy -c docker-compose.yml myapp

# View stack services
docker stack services myapp

# Remove stack
docker stack rm myapp

# With multiple compose files
docker stack deploy -c docker-compose.yml -c docker-compose.prod.yml myapp
```

## Advanced v3 Features

### Profiles

```yaml
services:
  web:
    image: nginx:alpine
    profiles:
      - frontend
      - production
    ports:
      - "80:80"

  api:
    image: node:18
    profiles:
      - backend
    environment:
      NODE_ENV: production

  database:
    image: postgres:15
    profiles:
      - backend
      - database
    environment:
      POSTGRES_DB: myapp

  dev-tools:
    image: node:18
    profiles:
      - development
    volumes:
      - ./:/app
    command: npm run dev

  tests:
    image: node:18
    profiles:
      - testing
    command: npm test
# Usage:
# docker compose --profile frontend up
# docker compose --profile development --profile backend up
```

### Extensions & Customization

```yaml
# Define reusable configurations
x-common-environment: &common-env
  NODE_ENV: production
  LOG_LEVEL: info

x-database-config: &db-config
  image: postgres:15
  environment:
    POSTGRES_DB: myapp
    POSTGRES_USER: user
  volumes:
    - db_data:/var/lib/postgresql/data

services:
  web:
    image: nginx:alpine
    environment:
      <<: *common-env
      WEB_PORT: 80

  api:
    image: node:18
    environment:
      <<: *common-env
      DATABASE_URL: postgresql://user:pass@db:5432/myapp
    depends_on:
      - database

  database:
    <<: *db-config
    environment:
      POSTGRES_PASSWORD: secret

volumes:
  db_data:
```

### Dependency Management

```yaml
services:
  web:
    image: nginx:alpine
    depends_on:
      api:
        condition: service_healthy
      database:
        condition: service_started
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s

  api:
    image: node:18
    depends_on:
      database:
        condition: service_healthy
      redis:
        condition: service_started
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s

  database:
    image: postgres:15
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
```

## Best Practices v3

### Security Hardening

```yaml
services:
  app:
    # Security best practices
    user: "1000:1000" # Non-root user
    read_only: true # Read-only root filesystem
    security_opt:
      - no-new-privileges:true
    cap_drop:
      - ALL
    cap_add: # Only add necessary capabilities
      - NET_BIND_SERVICE
    tmpfs:
      - /tmp:rw,noexec,nosuid,size=100m
      - /run:rw,noexec,nosuid,size=10m
    privileged: false
    init: true # Use init process

    # Resource limits
    deploy:
      resources:
        limits:
          memory: 512M
          cpus: "1.0"
          pids: 100
```

### Production Configuration

```yaml
services:
  web:
    image: nginx:alpine
    restart: unless-stopped
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
    deploy:
      resources:
        limits:
          memory: 256M
          cpus: "0.5"
        reservations:
          memory: 128M
          cpus: "0.25"
      update_config:
        parallelism: 2
        delay: 10s
        failure_action: rollback
      rollback_config:
        parallelism: 1
        delay: 5s

  app:
    build:
      context: .
      target: runtime
      cache_from:
        - myapp:latest
    environment:
      - NODE_ENV=production
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    configs:
      - source: app_config
        target: /app/config/production.yaml
```

## Migration Guide

### v2 to v3 Changes

```yaml
# v2 (legacy)
version: '2.4'
services:
  web:
    build: .
    cpu_shares: 73
    cpu_quota: 50000
    cpuset: 0,1
    mem_limit: 1000000000
    memswap_limit: 2000000000
    volume_driver: mydriver

# v3 (current)
version: '3.8'
services:
  web:
    build: .
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 1G
    volumes:
      - data:/app

volumes:
  data:
    driver: local
```

### Removed Options in v3

- `volume_driver` → Use volume configuration
- `cpu_shares`, `cpu_quota`, `cpuset` → Use `deploy.resources.limits.cpus`
- `mem_limit`, `memswap_limit` → Use `deploy.resources.limits.memory`
- `extends` → Use YAML anchors or multiple compose files

## Troubleshooting

### Common v3 Issues

```bash
# Check compose file syntax
docker compose config

# Dry run to see what would be created
docker compose up --dry-run

# View detailed service configuration
docker compose convert

# Check service dependencies
docker compose ps --all

# Debug networking
docker compose exec web nslookup database

# View resource usage
docker stats $(docker compose ps -q)

# Check health status
docker compose ps --format "table {{.Name}}\t{{.Service}}\t{{.Status}}"

# Environment troubleshooting
docker compose config | grep -A5 -B5 ENV_VAR
```

### Debug Commands

```bash
# Inspect service configuration
docker compose config services.web

# View logs with timestamps
docker compose logs --timestamps

# Execute debug shell
docker compose exec -it web sh

# Copy files for debugging
docker compose cp web:/app/logs ./debug-logs

# Check volume contents
docker compose run --rm -v db_data:/data busybox ls -la /data

# Network connectivity test
docker compose run --rm curlimages/curl http://web:80

# Resource limits check
docker compose exec web cat /sys/fs/cgroup/memory/memory.limit_in_bytes
```

### Performance Optimization

```bash
# Build cache optimization
docker compose build --parallel --memory=2g

# Resource monitoring
docker compose top
docker system df

# Cleanup
docker compose down --remove-orphans
docker system prune -f

# Volume optimization
docker volume ls -f dangling=true
```

---

## Quick Reference Card

### Essential v3 Commands

```bash
# Project lifecycle
docker compose up -d
docker compose down
docker compose ps

# Service management
docker compose logs -f service
docker compose exec service command
docker compose restart service

# Build and images
docker compose build --no-cache
docker compose pull
```

### v3 File Structure

```yaml
services:
  service-name:
    image: name:tag
    build:
      context: .
      target: stage
    deploy:
      resources:
        limits:
          memory: 512M
          cpus: "1.0"
    networks:
      - network-name
    volumes:
      - volume-name:/path

volumes:
  volume-name:

networks:
  network-name:

configs:
  config-name:

secrets:
  secret-name:
```

### Deployment Patterns

```bash
# Development
docker compose up

# Production with profiles
docker compose --profile production up

# Swarm deployment
docker stack deploy -c docker-compose.yml app

# Multiple environments
docker compose -f docker-compose.yml -f docker-compose.prod.yml up
```

---

_Remember: Docker Compose v3 is designed for modern Docker deployments with better Swarm integration and improved resource management. Always test with `docker compose config` before deployment!_
