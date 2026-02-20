# Project Status & Phase Tracking
## Databricks ETL Sales Pipeline

**Project Name**: Sales & Marketing Data Processing Pipeline
**Workspace**: zoltan-verebes-catalog-m / dg-day schema
**Last Updated**: 2026-02-20

---

## Development Phases

### Phase 1: Requirements ✅ COMPLETE
**Status**: Completed
**Deliverable**: Product Requirements Document (PRD)
**Output**: `/docs/PRD.md`
**Completion Date**: 2026-02-20
**Summary**:
- Captured high-level business goals and pain points
- Defined 3 data source entities (customers, sales, sales_orders)
- Documented 2 primary use cases (sales reporting, marketing campaigns)
- Specified 24 functional requirements
- Documented 16 non-functional requirements and constraints
- Identified 12 open questions for future phases

### Phase 2: Technical Design ✅ COMPLETE
**Status**: Completed
**Deliverable**: Technical Design Document (TDD)
**Output**: `/docs/TDD.md`
**Completion Date**: 2026-02-20
**Scope**: ✅ ALL COMPLETE
- ✅ Designed medallion architecture (bronze → silver → gold layers)
- ✅ Defined data models with real schema (sampled from Databricks Volume)
- ✅ Documented transformation logic and data lineage
- ✅ Specified data quality implementation strategy (validation, quarantine, metrics)
- ✅ Designed access control and security model (RBAC, PII classification)
- ✅ Planned monitoring and logging strategy

**Key Findings**:
- Actual data has JSON fields requiring parsing (product metadata, clicked_items, ordered_products)
- Customer table includes geo-coordinates, tax info, and loyalty segments
- Sales and orders data are denormalized with customer names
- Timezone enrichment needed from state codes

**Blockers/Dependencies**: Phase 1 Complete ✅

### Phase 3: Implementation ⏳ IN PROGRESS
**Status**: Ready to begin
**Deliverable**: Working ETL pipeline code
**Output**:
- Python modules in `src/sales_project/`
- Pipeline and job configurations in `resources/`
- Unit tests in `tests/`
**Expected Completion**: TBD
**Scope** (5 Sprints):
- **Sprint 1**: Bronze layer ingestion (CSV loading + metadata tracking)
- **Sprint 2**: Silver layer transformations (dedup, JSON parsing, validation)
- **Sprint 3**: Gold layer aggregations (sales metrics, marketing customer profiles)
- **Sprint 4**: Security & governance (RBAC, PII masking, access control)
- **Sprint 5**: Testing & deployment (unit tests, integration tests, DAB config)

**Implementation Challenges**:
- JSON parsing for product, clicked_items, ordered_products
- Timezone enrichment from state codes
- Handling denormalized customer data
- Deduplication of customer records

**Blockers/Dependencies**: Phase 2 Complete ✅

### Phase 4: Documentation & Testing ⏳ PENDING
**Status**: Not Started
**Deliverable**: Complete project documentation and test coverage
**Output**:
- Updated `/docs/PRD.md` with any refinements
- Updated `/docs/TDD.md` with implementation details
- Test results and coverage reports
- Deployment and operational runbooks
**Expected Completion**: TBD
**Scope**:
- Document implementation decisions and deviations from design
- Write operational runbooks for pipeline execution
- Document data lineage and transformation logic
- Create deployment guides for dev and prod
- Achieve target test coverage (to be defined)

**Blockers/Dependencies**: Phase 3 Complete

---

## Current Phase
**🔄 Phase 3** ⏳ Implementation (Ready to begin)
**→ Previous**: Phase 2 - Technical Design ✅ Complete

---

## Key Milestones

| Milestone | Target Date | Status |
|-----------|------------|--------|
| Phase 1: PRD Completion | 2026-02-20 | ✅ Complete |
| Phase 2: TDD Completion | TBD | ⏳ Pending |
| Phase 3: Pipeline Implementation | TBD | ⏳ Pending |
| Phase 4: Testing & Documentation | TBD | ⏳ Pending |
| Production Deployment | TBD | ⏳ Pending |

---

## Open Questions to Address
See `/docs/PRD.md` Section 7 for detailed open questions. Key items:
- OQ-1: Exact data volumes and growth trajectory
- OQ-5: Product category definitions
- OQ-6: Marketing REST API export format
- OQ-7: Timezone mapping strategy
- OQ-10: Audit and compliance requirements

---

## Team & Roles

| Role | Name | Status |
|------|------|--------|
| Product Owner | TBD | To be assigned |
| Data Engineering Lead | TBD | To be assigned |
| Sales Team Lead | TBD | To be assigned |
| Marketing Team Lead | TBD | To be assigned |

---

## Notes & Decisions

### Decision Log
- *2026-02-20*: Approved medallion architecture approach (bronze → silver → gold)
- *2026-02-20*: Confirmed batch processing model (no streaming)
- *2026-02-20*: Confirmed manual pipeline triggers (no automated scheduling in Phase 1)

### Risk Register
(To be maintained during implementation phases)

### Change Log
| Date | Phase | Change | Impact |
|------|-------|--------|--------|
| 2026-02-20 | 1 | Initial PRD created | Scope and requirements defined |

