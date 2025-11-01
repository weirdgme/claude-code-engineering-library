# Infrastructure Showcase Expansion Plan

## Overview

Plan for adding 5 new infrastructure skills to complete the showcase's coverage of modern engineering disciplines.

**Current Status:** 14 skills (5 dev + 9 infrastructure), 116 resources
**Proposed:** 19 skills (5 dev + 14 infrastructure), ~160 resources

---

## Priority 1: Essential Additions

### 1. infrastructure-architecture

**Why it's needed:**
- High-level architecture design is distinct from implementation (platform-engineering)
- Architecture Decision Records (ADRs) are critical for documentation
- Multi-region, DR, and capacity planning need dedicated coverage
- Architectural patterns (event-driven, microservices, serverless) deserve deep dives

**Resource Files (11):**
1. `architecture-patterns.md` - Microservices, monolith, serverless, event-driven, CQRS
2. `multi-region-design.md` - Active-active, active-passive, data replication, latency
3. `disaster-recovery.md` - RTO/RPO, backup strategies, failover procedures
4. `capacity-planning.md` - Load testing, resource forecasting, scaling strategies
5. `architecture-decision-records.md` - ADR format, when to write, examples
6. `system-design-principles.md` - SOLID, 12-factor, CAP theorem, eventual consistency
7. `data-architecture.md` - Data flow, storage selection, caching strategies, CDC
8. `security-architecture.md` - Zero trust, defense in depth, threat modeling
9. `cost-architecture.md` - Cost-aware design, TCO analysis, FinOps integration
10. `migration-architecture.md` - Strangler fig, big bang, phased migration patterns
11. `reference-architectures.md` - E-commerce, SaaS, data platform, ML platform examples

**Skill Triggers:**
- Keywords: "architecture", "ADR", "multi-region", "disaster recovery", "capacity planning"
- File patterns: `architecture/`, `ADR/`, `docs/architecture/`

**Integration Points:**
- platform-engineering (implements the architecture)
- cloud-engineering (cloud-specific patterns)
- sre (reliability and DR)
- devsecops (security architecture)

**Estimated Effort:** 3-4 days

---

### 2. documentation-as-code

**Why it's needed:**
- Technical writing is a critical engineering skill
- Docs-as-code practices (version control, review, CI) are underutilized
- API documentation (OpenAPI/Swagger) needs dedicated coverage
- Diagram generation (PlantUML, Mermaid, diagrams-as-code) is essential

**Resource Files (10):**
1. `docs-as-code-principles.md` - Version control, review process, static site generators
2. `technical-writing-guide.md` - Structure, clarity, examples, audience targeting
3. `api-documentation.md` - OpenAPI/Swagger, REST docs, GraphQL docs, API design docs
4. `diagram-generation.md` - PlantUML, Mermaid, diagrams.net, C4 model, architecture diagrams
5. `documentation-sites.md` - Docusaurus, MkDocs, GitBook, VuePress, Astro Starlight
6. `markdown-best-practices.md` - GFM, formatting, linking, images, tables
7. `documentation-automation.md` - Auto-generation, linting, link checking, CI/CD
8. `readme-engineering.md` - Project READMEs, structure, badges, quick starts
9. `changelog-management.md` - Keep a Changelog, semantic versioning, release notes
10. `documentation-testing.md` - Link checking, code example testing, spell checking

**Skill Triggers:**
- Keywords: "documentation", "docs", "README", "OpenAPI", "Swagger", "diagram", "technical writing"
- File patterns: `docs/`, `*.md`, `openapi.yaml`, `swagger.json`, `README.md`

**Integration Points:**
- skill-developer (documenting skills)
- backend-dev-guidelines (API documentation)
- infrastructure-architecture (architecture docs and diagrams)
- All skills (documentation practices)

**Estimated Effort:** 2-3 days

---

## Priority 2: Deepening Coverage

### 3. database-engineering

**Why it's needed:**
- Database administration is a distinct discipline from ORM usage
- Query optimization, indexing, and performance tuning need dedicated coverage
- Replication, backup/restore, and migrations are critical operational topics
- PostgreSQL, MySQL, and cloud databases each have unique considerations

**Resource Files (11):**
1. `database-fundamentals.md` - ACID, transactions, isolation levels, normalization
2. `postgresql-administration.md` - Installation, configuration, vacuuming, extensions
3. `mysql-administration.md` - InnoDB, replication, backup/restore
4. `query-optimization.md` - EXPLAIN, indexing strategies, query planning, statistics
5. `database-indexing.md` - B-tree, hash, GiST, index types, when to index
6. `database-replication.md` - Streaming, logical, multi-master, conflict resolution
7. `database-backup-restore.md` - pg_dump, point-in-time recovery, backup strategies
8. `database-migrations.md` - Schema migrations, zero-downtime changes, Flyway, Liquibase
9. `database-monitoring.md` - Slow query logs, connection pooling, metrics
10. `cloud-databases.md` - RDS, Aurora, CloudSQL, Azure Database, managed vs self-hosted
11. `database-security.md` - Authentication, encryption at rest/transit, row-level security

**Skill Triggers:**
- Keywords: "database", "PostgreSQL", "MySQL", "query", "indexing", "replication"
- File patterns: `migrations/`, `*.sql`, `schema.sql`

**Integration Points:**
- backend-dev-guidelines (Prisma ORM)
- sre (database monitoring)
- cloud-engineering (managed databases)
- systems-engineering (database server administration)

**Estimated Effort:** 3-4 days

---

### 4. observability-engineering

**Why it's needed:**
- Observability is deeper than monitoring (covered in SRE)
- Distributed tracing (Jaeger, Tempo) needs dedicated coverage
- APM tools (DataDog, New Relic) have specific patterns
- Logs aggregation (ELK, Loki) and correlation strategies are complex
- OpenTelemetry is becoming the standard

**Resource Files (11):**
1. `observability-principles.md` - Three pillars (logs, metrics, traces), cardinality, sampling
2. `distributed-tracing.md` - Jaeger, Tempo, Zipkin, trace propagation, span design
3. `opentelemetry.md` - OTEL SDK, auto-instrumentation, collectors, exporters
4. `apm-tools.md` - DataDog, New Relic, Dynatrace, AppDynamics comparison
5. `logs-aggregation.md` - ELK Stack, Loki, structured logging, log retention
6. `metrics-design.md` - Prometheus, counters, gauges, histograms, cardinality management
7. `correlation-strategies.md` - Trace IDs, correlation fields, unified observability
8. `service-mesh-observability.md` - Istio metrics, distributed tracing, service graphs
9. `cost-of-observability.md` - Data volume, retention, sampling strategies, cost optimization
10. `observability-as-code.md` - Dashboard as code, alert as code, SLO definitions
11. `debugging-production.md` - Live debugging, profiling, incident investigation workflows

**Skill Triggers:**
- Keywords: "observability", "tracing", "APM", "OpenTelemetry", "Jaeger", "distributed tracing"
- File patterns: `observability/`, `otel-config.yaml`

**Integration Points:**
- sre (monitoring and alerting overlap)
- devsecops (security monitoring)
- platform-engineering (service mesh observability)
- backend-dev-guidelines (instrumentation)

**Estimated Effort:** 3-4 days

---

## Priority 3: Nice-to-Have

### 5. api-engineering

**Why it's needed:**
- API design patterns deserve dedicated focus beyond backend-dev-guidelines
- GraphQL, gRPC, and WebSocket patterns need coverage
- API gateways (Kong, Ambassador, AWS API Gateway) are complex
- Rate limiting, throttling, and versioning strategies are critical

**Note:** Some overlap with backend-dev-guidelines (which covers Express REST APIs). This skill would focus on API architecture, not implementation.

**Resource Files (10):**
1. `api-design-principles.md` - REST, RESTful design, resource modeling, URL design
2. `graphql-patterns.md` - Schema design, resolvers, N+1 problem, DataLoader
3. `grpc-patterns.md` - Protocol Buffers, streaming, error handling, load balancing
4. `api-gateways.md` - Kong, Ambassador, AWS API Gateway, Envoy Gateway
5. `api-versioning.md` - URI versioning, header versioning, semantic versioning
6. `rate-limiting.md` - Token bucket, leaky bucket, sliding window, distributed rate limiting
7. `api-security.md` - OAuth2, JWT, API keys, CORS, CSRF protection
8. `api-documentation.md` - OpenAPI, Swagger UI, Redoc, Postman collections
9. `websocket-patterns.md` - Real-time APIs, Socket.IO, connection management
10. `api-testing.md` - Contract testing, Pact, API testing frameworks

**Skill Triggers:**
- Keywords: "API", "GraphQL", "gRPC", "REST", "API gateway", "rate limiting"
- File patterns: `api/`, `graphql/`, `*.proto`, `openapi.yaml`

**Integration Points:**
- backend-dev-guidelines (API implementation)
- documentation-as-code (API documentation)
- devsecops (API security)
- cloud-engineering (API gateway services)

**Estimated Effort:** 2-3 days

---

## Implementation Strategy

### Phase 1: Essential Additions (Week 1-2)
1. infrastructure-architecture (4 days)
2. documentation-as-code (3 days)

**Result:** 16 skills, ~137 resources

### Phase 2: Deepening Coverage (Week 3-4)
3. database-engineering (4 days)
4. observability-engineering (4 days)

**Result:** 18 skills, ~159 resources

### Phase 3: Nice-to-Have (Week 5)
5. api-engineering (3 days)

**Result:** 19 skills, ~169 resources

### Total Timeline: 5 weeks

---

## Resource Requirements

### Per Skill Creation:
- Research and outline: 4-6 hours
- Resource file writing: 8-12 hours (11 files × ~500 lines each)
- SKILL.md creation: 2-3 hours
- Integration points: 1-2 hours
- Testing activation: 1 hour
- **Total: 16-24 hours per skill (2-3 days)**

### Additional Work:
- Agent creation (optional): 1-2 agents per skill (2-4 hours each)
- Slash command creation (optional): 1 command per skill (1-2 hours)
- Hook creation (if needed): Varies
- Documentation updates: 2-3 hours
- Testing: 1-2 hours

---

## Success Criteria

### For Each New Skill:
- [ ] 10-11 resource files, each <500 lines
- [ ] SKILL.md with "When to Use This Skill" section
- [ ] Integration points documented
- [ ] skill-rules.json triggers configured
- [ ] Tested activation via hooks
- [ ] Cross-references to other skills
- [ ] Examples and anti-patterns included

### For Overall Expansion:
- [ ] README updated with new skills
- [ ] Component counts updated
- [ ] Test report updated
- [ ] All cross-references valid
- [ ] No orphaned resources
- [ ] Consistent formatting across all skills

---

## Alternative: Community Contributions

Instead of creating all 5 skills immediately, consider:
1. **Open issues** for each missing skill
2. **Create templates** for contributors
3. **Document contribution guidelines**
4. **Label as "good first issue"** or "help wanted"
5. **Review and integrate** community PRs

**Benefits:**
- Community engagement
- Diverse perspectives
- Lower maintenance burden
- Faster coverage expansion

**Risks:**
- Quality variance
- Longer timeline
- More review overhead

---

## Recommendation

**Immediate Action:**
1. Create **infrastructure-architecture** skill (highest value, clear gap)
2. Create **documentation-as-code** skill (complements all other skills)
3. Document remaining gaps in README (done ✅)
4. Open issues for community contributions on remaining 3 skills

**Rationale:**
- 16 skills (5 dev + 11 infrastructure) is comprehensive
- infrastructure-architecture fills the biggest gap
- documentation-as-code benefits all skills
- Remaining 3 are valuable but less critical
- Community can help expand further

---

## Next Steps

If proceeding with expansion:
1. Review and approve this plan
2. Create infrastructure-architecture skill first
3. Test activation and integration
4. Create documentation-as-code skill
5. Update all documentation
6. Create comprehensive test report
7. Commit and push

**Estimated time for steps 2-7:** 1-2 weeks
