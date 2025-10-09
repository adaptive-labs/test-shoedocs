# Getting started

How do one start? its the opposite of what you think...I know shocking..its easy to begin.

## Prerequisites

Before you dive into Shoehorn, make sure you have the necessary tools installed on your development machine. You'll need Node.js version 18 or higher for the frontend development, and Go 1.21 or later for backend work. Docker and Docker Compose are essential for running the local development environment with all required services.

Additionally, ensure you have access to a PostgreSQL database and a Typesense instance. The Docker Compose setup handles these automatically, but for production deployments you'll want dedicated instances. Git is obviously required for cloning the repository and managing version control.

## Installation Steps

Start by cloning the Shoehorn repository from GitHub to your local machine. Navigate to the project directory and you'll find two main folders: the backend Go application and the frontend SvelteKit application. Each has its own set of dependencies that need to be installed separately.

For the backend, run go mod download to fetch all Go dependencies. The project uses several key libraries including Chi for routing, sqlc for type-safe database queries, and the Typesense Go client for search functionality. These dependencies are pinned to specific versions to ensure consistency across environments.

The frontend requires bun or npm to install JavaScript dependencies. Navigate to the web directory and run bun install. This will download Svelte 5, SvelteKit, Tailwind CSS, and all the component libraries used throughout the application. The installation process typically takes a few minutes depending on your internet connection.

## Configuration

Shoehorn uses environment variables for all configuration, following twelve-factor app principles. Copy the example environment file to create your local configuration. The file includes sensible defaults for development, but you'll need to customize certain values for your setup.

Database connection strings need to point to your PostgreSQL instance. For local development, the Docker Compose setup provides a database at localhost:5432. Set the username, password, and database name according to your preferences. Connection pooling is handled automatically by the application.

Typesense configuration requires an API key and the host address. Generate a strong random key for local development, or use the one provided in the Docker Compose file. The search collection names are prefixed by tenant ID to support multi-tenancy in the future.

Authentication settings depend on your identity provider. For local development, you can use oauth2-proxy with a mock provider or connect to a real Keycloak instance. Set the client ID, client secret, and redirect URLs appropriately. Remember that redirect URLs must match exactly, including protocol and port number.

## Running Locally

With configuration complete, start the supporting services using Docker Compose. This brings up PostgreSQL, Typesense, and oauth2-proxy in isolated containers. The services initialize with the correct schemas and settings, ready for the application to connect.

Launch the backend API server from the project root directory. The server listens on port 8080 by default and provides RESTful endpoints for the frontend. Database migrations run automatically on startup, creating or updating tables as needed. Watch the logs to confirm successful startup and connection to dependencies.

In a separate terminal, start the frontend development server from the web directory. Vite powers the development experience with instant hot module replacement. The frontend proxies API requests to the backend, so both servers work together seamlessly. Navigate to localhost:5173 in your browser to see the application.

## Initial Setup

The first time you access Shoehorn, you'll need to log in through your configured identity provider. After authentication, you'll land on the dashboard showing an empty catalog. This is normal for a fresh installation with no data yet imported.

Navigate to the admin section to configure the GitHub crawler. Enter your GitHub token and select which organizations and repositories to index. The crawler discovers catalog-info.yaml files and imports entity definitions automatically. This initial crawl may take several minutes depending on repository count.

Once the crawler completes, the entity catalog populates with services, libraries, and other components from your repositories. The search indexer runs in the background, building the Typesense collections for fast full-text search. Check the admin dashboard to monitor indexing progress and troubleshoot any errors.

## Development Workflow

With the local environment running, you're ready to start developing. Make changes to Svelte components in the web/src directory and see updates instantly in your browser. The development server watches for file changes and recompiles automatically, preserving application state when possible.

Backend changes require restarting the Go server, or you can use Air for automatic reloading. Create new API endpoints by adding handlers in the internal/handlers directory. Use sqlc to generate type-safe database access code from SQL queries. This approach catches errors at compile time rather than runtime.

Write tests alongside your code to maintain quality and prevent regressions. The frontend uses Vitest for unit testing and Playwright for end-to-end tests. The backend relies on Go's built-in testing framework with testify for assertions. Aim for high test coverage on critical paths.

## Database Migrations

Schema changes are managed through versioned migration files in the db/migrations directory. Each migration has an up and down script to apply and rollback changes respectively. Migrations run in order based on version numbers, ensuring consistent state across environments.

Create new migrations using the provided scripts or manually following the naming convention. Include both DDL statements for schema changes and any necessary data transformations. Test migrations thoroughly in development before applying to production, including the rollback path.

The application tracks which migrations have run in a special schema_migrations table. This prevents re-running migrations and enables automatic application on startup. For complex migrations, consider breaking them into multiple steps to reduce risk and enable easier rollback.

## Building for Production

Production builds optimize the application for performance and size. The frontend build process minifies JavaScript, optimizes images, and generates static assets with cache-friendly filenames. Build the frontend by running bun run build from the web directory, creating a .svelte-kit/output directory with the compiled application.

Compile the Go backend for your target platform using standard Go build tools. Enable optimizations and strip debug symbols to reduce binary size. The resulting executable is self-contained and can run without Go installed on the target system. Cross-compilation allows building for Linux servers from Mac or Windows development machines.

Create Docker images for containerized deployments. The provided Dockerfile uses multi-stage builds to keep images small. Only the compiled binaries and necessary runtime dependencies are included in the final image. Tag images with version numbers for traceability and rollback capability.

## Deployment Considerations

When deploying Shoehorn, ensure all environment variables are set correctly for your production environment. Never hardcode secrets or use development credentials in production. Use Kubernetes secrets, HashiCorp Vault, or your cloud provider's secret management service.

Run database migrations as an init container or job before starting application pods. This ensures schema updates complete before new code attempts to use changed tables. Set appropriate resource limits and requests based on your expected load and observed usage patterns.

Configure health check endpoints for liveness and readiness probes. The API exposes /health for basic health checks and /ready for readiness checks that verify database and search connectivity. Use these endpoints to enable Kubernetes to automatically restart unhealthy pods and route traffic only to ready instances.

## Monitoring and Observability

Shoehorn outputs structured JSON logs suitable for aggregation by tools like Elasticsearch or Datadog. Logs include request IDs for tracing requests across service boundaries. Set log levels appropriately for each environment, using debug for development and info or warn for production.

Metrics are exposed in Prometheus format at the /metrics endpoint. Key metrics include request duration histograms, error rates, and resource utilization. Configure Prometheus to scrape these metrics and set up alerting rules for critical conditions like high error rates or slow response times.

Distributed tracing can be added by integrating OpenTelemetry or a similar framework. Trace requests through the application stack to identify performance bottlenecks and understand service dependencies. This visibility becomes crucial as your catalog grows and query complexity increases.

## Troubleshooting Common Issues

If entities aren't appearing in search results, verify the indexer job has completed successfully. Check logs for indexing errors or Typesense connection issues. You can trigger a manual reindex from the admin interface if automatic indexing fails.

Database connection pool exhaustion causes timeouts and degraded performance. Monitor connection usage and adjust pool settings based on concurrent request volume. Ensure connections are properly released after use by using defer statements in Go code.

Authentication failures often stem from misconfigured redirect URIs or expired tokens. Double-check that redirect URIs match exactly between oauth2-proxy configuration and your identity provider settings. Expired or invalid client secrets will also prevent successful authentication.

## Next Steps

Now that you have Shoehorn running locally, explore the codebase to understand the architecture. Read through the API handlers to see how requests are processed. Examine the Svelte components to learn the UI patterns and state management approach.

Try adding a new entity type or extending existing entity metadata. This exercise will touch multiple layers of the application and give you hands-on experience with the development workflow. Start small with a new field, then progress to more complex features.

Join the community to ask questions, share experiences, and contribute back. Your feedback helps improve Shoehorn for everyone. Whether you're fixing bugs, adding features, or improving documentation, all contributions are welcome and appreciated.
