# CLAUDE.md - Databricks ETL Sales Pipeline Demo

## Project Overview
This is a **demo Databricks ETL pipeline** for sales and marketing data processing. It implements a medallion architecture (bronze → silver → gold layers) using Databricks SQL and Python.

**Target Workspace**: zoltan-verebes-catalog-m / dg-day schema

## Development Workflow

### Local Development
- Use `uv sync --dev` to install dependencies
- Run `uv run pytest` for unit tests
- Edit code in `src/sales_project/` and `resources/`

### Databricks Deployment
- **Dev**: `databricks bundle deploy --target dev`
- **Prod**: `databricks bundle deploy --target prod`
- **Run**: `databricks bundle run` to execute jobs/pipelines

## Project Structure
- `src/sales_project/` - Shared Python code for jobs and pipelines
- `resources/` - Job and pipeline configuration (YAML)
- `tests/` - Unit tests
- `docs/` - Project documentation (PRD, TDD)

## Key Files
- `project.md` - Project tracking and phase status
- `docs/PRD.md` - Product Requirements Document
- `docs/TDD.md` - Technical Design Document
- `pyproject.toml` - Project dependencies and metadata
- `resources/sample_pipeline.yml` - Main ETL pipeline definition

## Demo Considerations
- This is for demonstration purposes - focus on clarity over optimization
- Keep changes backward compatible with existing documentation
- Test locally before deploying to Databricks
- Use meaningful commit messages that reflect demo intent

## Development Phases
1. ✅ Requirements (Phase 1)
2. ✅ Technical Design (Phase 2)
3. ⏳ Implementation (Phase 3)
4. ⏳ Documentation (Phase 4)

Current phase: **Phase 3 - Implementation**
