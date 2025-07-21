# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Cimetry is a comprehensive analytics platform for TeamCity builds and tests. It provides detailed statistics, error analysis, and AI-powered insights to improve test quality and performance.

- **Production**: https://cimetry.creatio.com
- **Testing Stage**: http://cimetry.creatio.com:97

## Important Notes

- Use Context7 MCP for documentation lookup and best practices solutions
- Check `dev_tools/credentials.md` for TeamCity database credentials
- Keep documentation in `docs/` up to date
- Use named Docker volumes for persistent data

## Architecture

### Backend Services

- **Main API** (cimetry-api): FastAPI with Repository pattern, JWT auth, LDAP integration
- **LLM Service** (llm_integration): AI/ML microservice for error analysis and semantic search
- **Databases**: PostgreSQL (Cimetry data, pgvector), TeamCity DB (read-only access for TeamCity data analysis)

## Cimetry API Service:

- **README**: `cimetry-api/README.md`
- **ALWAYS use UV** for package management (`uv run`, `uv add`, `uv sync`)
- **Development guidelines**: `docs/development-guidelines.md`
- **Repository Pattern**: Database access through repositories (`cimetry-api/app/repositories/`)
- **Data Validation**: Pydantic schemas for request/response validation (`cimetry-api/app/schemas/`)
- **Service Layer**: Business logic in services (`cimetry-api/app/services/`)
- **API Routes**: Endpoint definitions (`cimetry-api/app/api/`)
- **Caching**: File-based caching in `cimetry-api/cache/` directory
- **Models**: SQLAlchemy models for TeamCity database (`cimetry-api/app/models/`)
- **Schemas**: Pydantic models for API validation (`cimetry-api/app/schemas/`)
- **Authentication**: LDAP/Active Directory integration with JWT tokens
- **Scheduler**: APScheduler for background tasks (LDAP sync)

### Security Practices

- **Authentication**: JWT-based, most endpoints require auth when AUTH_ENABLED=true
- **Secrets**: Never commit secrets, use environment variables
- **Input Validation**: Use Pydantic schemas for all API inputs

### Database migrations (PostgreSQL)

```bash
# Use the migration script (recommended)
./scripts/run-migrations.sh -a current        # Check current status
./scripts/run-migrations.sh                   # Apply all pending migrations
./scripts/run-migrations.sh -a history        # View migration history

# Or run directly with uv
cd cimetry-api && uv run alembic upgrade head

# Create new migration
cd cimetry-api && uv run alembic revision -m "description" --autogenerate
```

3. Review and test migration before applying

### Cimetry API Service Testing approach:

- Write tests for all new functionality
- Use pytest fixtures for test data
- Mock external dependencies
- Aim for high test coverage
- See `cimetry-api/tests/README.md` for detailed testing guide

## Frontend (Next.js):

- **README**: `frontend/README.md`
- **Next.js App Router**: Pages in `frontend/src/app/`
- **Component Structure**: Shadcn-inspired components in `frontend/src/components/`
- **State Management**: React Context API in `frontend/src/contexts/`
- **API Integration**: Services in `frontend/src/services/`
- **Type Safety**: TypeScript types in `frontend/src/types/`

### Key Development Guidelines

#### Frontend (TypeScript)

- Use TailwindCSS for all styling (no CSS files)
- Implement early returns in functions
- Prefix event handlers with "handle" (e.g., `handleClick`)
- Use functional components with hooks
- Include accessibility attributes
- Avoid hydration errors by using useEffect for browser APIs

## LLM Integration Service:

- **README**: `llm-integration/README.md`
- **LLM API**: Ollama or OpenRouter for AI processing
- **Vector Storage**: PostgreSQL with pgvector extension
- **Embeddings**: sentence-transformers for semantic search
- **LLM Providers**: Ollama (local) or OpenRouter (cloud)
- **Error Analysis**: Semantic error search and pattern clustering

### Key Development Guidelines

#### LLM Integration Service (Python)

- Use async/await for I/O operations
- Follow Repository/Service/Controller pattern
- Add type hints to all functions
- Use Pydantic for data validation
- Handle errors with proper logging

## Essential Development Commands

### 1. Running Development Environment

```bash
# Start all services (recommended)
./scripts/compose-deploy.sh -e development

# Or run services individually:
cd cimetry-api && uv run uvicorn app.main:app --reload --port 5000
cd frontend && npm run dev
```

### 2. Testing

```bash
# Run all tests with coverage
./scripts/run-tests.sh

# Run specific test categories
./scripts/run-tests.sh --category unit
./scripts/run-tests.sh --category integration

# Quick test commands
make test-all           # Test all services
cd cimetry-api && uv run pytest tests/test_specific.py::test_name  # Run single test
cd llm_integration && uv run pytest tests/test_specific.py::test_name  # Run single test in LLM service
cd frontend && npm run test  # Run frontend tests
```

### 3. Code Quality

```bash
# Format and lint all code
make format-all
make lint-all

# Quick check (format then lint)
make quick-check-all

# Frontend specific
cd frontend && npm run lint:fix
```

## Database Guidelines

- **Query Optimization**: Use indexed columns, avoid N+1 queries
- **Migrations PostgreSQL**: Always review auto-generated migrations before applying
- **TeamCity DB**: This DB is from external service. Read only. Always use `WITH (NOLOCK)` hint for queries

## Project Structure

```
cimetry/
├── cimetry-api/          # Main API service
│   ├── app/
│   │   ├── api/endpoints/    # API routes
│   │   ├── repositories/    # Data access layer
│   │   ├── services/        # Business logic
│   │   └── schemas/         # Pydantic models
│   └── tests/              # pytest tests
├── frontend/             # Next.js application
│   └── src/
│       ├── app/          # App router pages
│       └── components/   # React components
├── llm_integration/      # AI/ML service
└── scripts/             # Deployment scripts
```
