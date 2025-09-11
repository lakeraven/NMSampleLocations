# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

NMSampleLocations is a FastAPI-based geospatial sample data management system for the New Mexico Bureau of Geology and Mineral Resources. It provides a RESTful API for managing sample location data with PostGIS/PostgreSQL backend and supports research, field operations, and public data delivery.

## Development Commands

### Environment Setup
```bash
# Set up virtual environment and install dependencies
uv venv
source .venv/bin/activate
uv pip install -r requirements.txt

# Install development dependencies
uv sync --locked --all-extras --dev
```

### Environment Configuration
```bash
# Copy template and configure for your environment
cp .env.example .env
# Edit .env as needed - POSTGRES_USER defaults to current system user
```

### Database Management
```bash
# Run database migrations
alembic upgrade head

# Initialize lexicon data (development mode)
python manage.py initialize-lexicon
```

### Running the Application
```bash
# Start development server
uvicorn main:app --reload

# Alternative entry point
python main.py
```

### Testing
```bash
# Run all tests with verbose output and timing
uv run pytest -vv --durations=20

# Run tests with JUnit XML output (CI format)
uv run pytest -vv --durations=20 --junitxml=junit.xml
```

## Architecture

### Core Application Structure
- **main.py**: FastAPI application entry point with router registration and middleware setup
- **core/**: Application configuration and startup logic
  - `app.py`: FastAPI app factory with lifespan management, database initialization
  - `settings.py`: Environment-based configuration management
- **api/**: Route handlers organized by domain (location, sensor, thing, geothermal, etc.)
- **db/**: Database models, engine, and session management using SQLAlchemy + PostGIS
- **schemas/**: Pydantic models for request/response validation
- **services/**: Business logic and helper functions
- **admin/**: FastAdmin integration for administrative interface

### Database Layer
- Uses SQLAlchemy with PostGIS for geospatial operations
- Alembic for database migrations
- Database models in `db/` directory with Base metadata management
- Engine configuration in `db/engine.py`

### API Design
- RESTful endpoints with FastAPI
- Route organization by domain entities (locations, sensors, things, etc.)
- FastAPI-pagination integration for paginated responses
- CORS middleware configured for cross-origin requests
- Admin interface mounted at `/admin` endpoint

### Key Features
- Geospatial data handling with native GeoJSON support
- Lexicon management system with JSON-based initialization
- Multi-domain data management (geothermal, geochronology, publications, etc.)
- Asset management capabilities
- Form-based data entry support

### Development Mode Behavior
When `MODE=development` environment variable is set:
- Database tables are dropped and recreated on startup
- Default admin user created (username: admin, password: admin)
- Lexicon populated from `core/lexicon.json`

### Testing Infrastructure
- Pytest-based test suite with comprehensive coverage
- PostGIS/TimescaleDB test database via Docker
- Tests organized by domain matching the API structure
- GitHub Actions CI/CD with automated testing

### Dependencies Management
- Uses `uv` package manager for fast dependency resolution
- Requirements defined in `pyproject.toml` with dev dependencies
- Lock file (`uv.lock`) for reproducible builds