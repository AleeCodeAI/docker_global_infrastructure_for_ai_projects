# Shared AI Infrastructure with Docker Compose

## Introduction

This repository provides a shared infrastructure setup using Docker Compose for AI engineering projects. It includes essential services commonly used in AI development, such as PostgreSQL for data storage, Redis for caching and session management, Langfuse for AI observability and tracing, and N8N for workflow automation. By centralizing these services in a single, reusable infrastructure, you can avoid creating redundant containers for each project, saving system resources and simplifying management.

## What's Included

The `docker-compose.yml` file defines the following components:

### Services

- **PostgreSQL (postgres)**: A robust relational database running PostgreSQL 16. It's configured with health checks, persistent data volumes, and exposed on port 5434 (mapped to internal 5432). This service serves as the primary database for applications like Langfuse.

- **Redis (redis)**: An in-memory data structure store using Redis 7 Alpine image. It includes append-only file persistence, health checks, and is exposed on port 6380 (mapped to internal 6379). Ideal for caching, session storage, and pub/sub messaging.

- **Langfuse (langfuse)**: An open-source LLM engineering platform for observability and analytics. It depends on PostgreSQL and is configured with environment variables for authentication and database connection. Exposed on port 3100 (mapped to internal 3000).

- **N8N (n8n)**: A workflow automation tool that allows you to connect APIs, services, and databases. It's configured with encryption and webhook settings, with data persisted in a volume. Exposed on port 5679 (mapped to internal 5678).

### Volumes

- `postgres_data`: Persistent storage for PostgreSQL data.
- `redis_data`: Persistent storage for Redis data.
- `n8n_data`: Persistent storage for N8N user data and workflows.

### Networks

- `infra-network`: A custom bridge network named `infra-network` that connects all services. This allows other projects to join the same network and communicate with these shared services without exposing ports externally.

## Why This Setup?

As an AI engineer, you'll often work on multiple projects that require similar backend services like databases, caching layers, observability tools, and automation platforms. Creating separate instances of these services for each project leads to:

- **Resource Waste**: Multiple containers consuming CPU, memory, and disk space unnecessarily.
- **Management Complexity**: Handling updates, backups, and configurations across numerous isolated setups.
- **Inconsistency**: Potential version mismatches or configuration drifts between projects.

This shared infrastructure addresses these issues by providing a centralized, reusable environment. All services run on a dedicated network, and your AI projects can connect to this network to access the services seamlessly. This approach promotes efficiency, consistency, and scalability in your development workflow.

## Environment Variables Setup

Before running the infrastructure, you need to configure the required environment variables. Use the provided `.env.example` file as a template:

1. Copy `.env.example` to `.env`:
   ```
   cp .env.example .env
   ```

2. Edit `.env` and fill in the values:

   - `POSTGRES_USER`: Username for PostgreSQL (default: postgres).
   - `POSTGRES_PASSWORD`: Password for the PostgreSQL user. Choose a strong, secure password.
   - `LANGFUSE_NEXTAUTH_SECRET`: A secret key for NextAuth.js authentication in Langfuse. Generate a random string (e.g., using `openssl rand -base64 32`).
   - `LANGFUSE_SALT`: A salt value for hashing in Langfuse. Generate a random string.
   - `N8N_ENCRYPTION_KEY`: Encryption key for N8N data. Generate a secure key (e.g., 32-character random string).

   **Security Note**: Never commit the `.env` file to version control. Ensure it's added to `.gitignore`.

## Running the Infrastructure

To set up and run your shared AI infrastructure:

1. **Prerequisites**:
   - Docker and Docker Compose installed on your system.
   - Sufficient resources (CPU, memory) to run the services.

2. **Clone or Navigate to the Repository**:
   ```
   cd /path/to/infrastructure
   ```

3. **Configure Environment Variables** (as described above).

4. **Start the Services**:
   ```
   docker-compose up -d
   ```
   This will start all services in detached mode. The `-d` flag runs containers in the background.

5. **Verify Services**:
   - PostgreSQL: Accessible at `localhost:5434`
   - Redis: Accessible at `localhost:6380`
   - Langfuse: Web UI at `http://localhost:3100`
   - N8N: Web UI at `http://localhost:5679`

6. **Check Logs** (if needed):
   ```
   docker-compose logs [service-name]
   ```
   Replace `[service-name]` with `postgres`, `redis`, `langfuse`, or `n8n`.

7. **Stop the Services**:
   ```
   docker-compose down
   ```
   This stops and removes containers but preserves volumes.

## Connecting Your AI Projects

To use these shared services in your AI projects:

1. In your project's `docker-compose.yml`, add the `infra-network` to your services:
   ```yaml
   networks:
     infra-network:
       external: true
   ```

2. Configure your application to connect to the services using their container names (e.g., `postgres:5432` for database, `redis:6379` for Redis).

This setup ensures your projects can leverage the shared infrastructure efficiently.

## Troubleshooting

- **Port Conflicts**: If ports 5434, 6380, 3100, or 5679 are in use, modify the port mappings in `docker-compose.yml`.
- **Health Checks Failing**: Ensure environment variables are set correctly and check service logs.
- **Data Persistence**: Volumes are automatically created; data persists across container restarts.

For more details, refer to the official documentation of each service: [PostgreSQL](https://www.postgresql.org/docs/), [Redis](https://redis.io/documentation), [Langfuse](https://langfuse.com/docs), [N8N](https://docs.n8n.io/).

## Contributing

Feel free to suggest improvements or additional services commonly used in AI projects.