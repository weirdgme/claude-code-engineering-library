# Architecture Decision Records (ADRs)

Guide to documenting architecture decisions using ADRs for transparency, knowledge sharing, and future reference.

## What Are ADRs?

**Architecture Decision Records** are lightweight documents that capture important architectural decisions, their context, and consequences.

**Purpose:**
- Document **why** decisions were made (not just **what**)
- Preserve decision context for future team members
- Enable informed reversal of decisions
- Create audit trail of architectural evolution

## ADR Template

### Standard Format

```markdown
# ADR-NNN: [Short title of solved problem and solution]

## Status
[Proposed | Accepted | Deprecated | Superseded by ADR-XXX]

## Context
What is the issue we're seeing that is motivating this decision or change?
- Current state
- Problems/challenges
- Constraints
- Requirements

## Decision
What is the change we're proposing and/or doing?
- Chosen solution
- Key implementation details
- Why this approach?

## Consequences
What becomes easier or more difficult because of this change?

Positive:
- Benefit 1
- Benefit 2

Negative:
- Trade-off 1
- Trade-off 2

Neutral:
- Other implication 1

## Alternatives Considered
What other options did we evaluate?
- Alternative 1: Why rejected
- Alternative 2: Why rejected
- Alternative 3: Why rejected
```

## Real-World Examples

### Example 1: Database Choice

```markdown
# ADR-001: Use PostgreSQL as Primary Database

## Status
Accepted (2024-01-15)

## Context
We need to choose a database for our new microservices platform.

Current state:
- Starting fresh, no legacy database
- Team has SQL experience (MySQL, PostgreSQL)
- Data is relational (users, orders, products)
- Need ACID transactions for payments
- Budget: ~$500/month

Requirements:
- Strong consistency for financial data
- JSON support for flexible schemas
- Full-text search capability
- Good cloud-managed options (RDS)
- Open-source (no licensing costs)

## Decision
Use PostgreSQL (Amazon RDS Multi-AZ) as our primary database.

Implementation:
- RDS PostgreSQL 15
- Multi-AZ for high availability
- Read replicas for analytics workloads
- Connection pooling via PgBouncer
- Prisma ORM for type-safe access

## Consequences

Positive:
- ✅ Strong ACID guarantees (critical for payments)
- ✅ Excellent JSON support (JSONB)
- ✅ Built-in full-text search
- ✅ Team already familiar
- ✅ RDS handles backups, patches, scaling
- ✅ No licensing costs

Negative:
- ❌ Vertical scaling limits (max instance size)
- ❌ More expensive than NoSQL for large scale
- ❌ Schema migrations require planning

Neutral:
- SQL-based (requires schema design upfront)
- Relational model (good fit for our domain)

## Alternatives Considered

### MongoDB
❌ Rejected
- Eventual consistency risky for payments
- Team less familiar with document model
- Weaker transaction support

### DynamoDB
❌ Rejected
- Single-table design too complex for team
- Difficult to query ad-hoc (analytics)
- Vendor lock-in to AWS

### MySQL
✅ Close second
- Similar features to PostgreSQL
- Slightly better replication performance
- ❌ Weaker JSON support
- ❌ Less advanced features

## References
- [PostgreSQL vs MySQL Comparison](https://...)
- [Team Database Survey Results](https://...)
- [Cost Analysis Spreadsheet](https://...)
```

---

### Example 2: API Gateway Selection

```markdown
# ADR-002: Use Kong as API Gateway

## Status
Accepted (2024-02-01)

## Context
We have 15 microservices and need a unified API gateway.

Current state:
- Each service exposed directly to internet
- No centralized authentication
- Inconsistent rate limiting
- CORS configuration duplicated

Requirements:
- Authentication/authorization
- Rate limiting and throttling
- Request/response transformation
- Observability (metrics, tracing)
- Plugin ecosystem
- Kubernetes-native

## Decision
Deploy Kong as our API gateway in Kubernetes.

Implementation:
- Kong Ingress Controller for Kubernetes
- OAuth2/JWT authentication plugin
- Rate limiting plugin (Redis-backed)
- Prometheus metrics plugin
- Deploy in gateway namespace
- 3 replicas for high availability

## Consequences

Positive:
- ✅ Centralized authentication (no more per-service)
- ✅ Consistent rate limiting
- ✅ Kubernetes-native (Ingress resource)
- ✅ Rich plugin ecosystem (200+ plugins)
- ✅ Open-source (no licensing for core)
- ✅ Good documentation and community

Negative:
- ❌ Single point of failure (need HA setup)
- ❌ Additional latency (~10ms per request)
- ❌ Learning curve for team
- ❌ PostgreSQL/DB dependency for config

Neutral:
- Lua-based plugins (new language for team)
- Requires Redis for rate limiting

## Alternatives Considered

### AWS API Gateway
❌ Rejected
- Too expensive at scale ($3.50 per million requests)
- Vendor lock-in
- ✅ Fully managed (lower ops overhead)

### Envoy + Ambassador
❌ Rejected
- More complex to configure
- Smaller community
- ✅ More flexible/powerful
- ✅ CNCF project

### Nginx + Custom Lua
❌ Rejected
- Have to build everything ourselves
- High maintenance burden
- ✅ Maximum flexibility

### Traefik
✅ Close second
- Simpler configuration
- Kubernetes-native
- ❌ Fewer plugins
- ❌ Weaker authentication options

## Migration Plan
1. Deploy Kong in parallel with direct service access
2. Migrate authentication to Kong (2 weeks)
3. Implement rate limiting (1 week)
4. Switch DNS to Kong (phased rollout)
5. Remove direct service access (after 2 weeks validation)

## Success Metrics
- < 15ms P95 latency added by gateway
- Zero authentication-related incidents
- 99.9% gateway uptime
- 50% reduction in rate limiting config across services
```

---

### Example 3: Monorepo Decision

```markdown
# ADR-003: Adopt Monorepo for All Services

## Status
Accepted (2024-03-15)

## Context
We currently have 15 separate Git repositories, one per microservice.

Problems:
- Hard to coordinate changes across services
- Dependency version drift
- Code duplication (shared utilities copied)
- Complex CI/CD (15 different pipelines)
- Difficult refactoring across service boundaries

Team size: 12 engineers
Services: 15 (expected to grow to 30)

## Decision
Consolidate all services into a single monorepo using Turborepo.

Structure:
```
monorepo/
├── services/
│   ├── user-service/
│   ├── order-service/
│   └── ...
├── packages/
│   ├── shared-types/
│   ├── auth-lib/
│   └── ...
├── infrastructure/
│   └── terraform/
└── package.json
```

Tooling:
- Turborepo for build orchestration
- Yarn workspaces for dependency management
- CODEOWNERS for code ownership
- GitHub Actions for CI/CD

## Consequences

Positive:
- ✅ Atomic cross-service changes
- ✅ Shared code reuse (no duplication)
- ✅ Consistent dependency versions
- ✅ Single CI/CD pipeline (with affected detection)
- ✅ Easier refactoring across services
- ✅ Single source of truth

Negative:
- ❌ Larger repository size (slower clones)
- ❌ Requires build orchestration tooling
- ❌ Git history from separate repos lost
- ❌ Need clear ownership rules (CODEOWNERS)
- ❌ Risk of coupling if not disciplined

Neutral:
- Services still deployed independently
- Team needs to learn monorepo workflows

## Alternatives Considered

### Polyrepo (Current State)
❌ Rejected
- Current pain points unresolved
- Coordination overhead too high

### Git Submodules
❌ Rejected
- Complex and error-prone
- Poor developer experience
- Doesn't solve dependency drift

### Meta/Lerna (without Turborepo)
❌ Rejected
- Slower builds (no caching)
- Turborepo better performance

## Migration Plan
1. Create new monorepo repository
2. Move shared libraries first (week 1)
3. Migrate services one-by-one (weeks 2-6)
4. Update CI/CD pipelines (week 7)
5. Decommission old repos (week 8)

## Rollback Plan
If monorepo doesn't work after 3 months:
- Extract services back to separate repos
- Use Git subtree split to preserve history
- Estimated effort: 2 weeks
```

---

## When to Write an ADR

### Write ADRs for:
✅ **Significant technical decisions**
- Technology selection (database, framework, language)
- Architecture patterns (microservices, event-driven)
- Infrastructure choices (cloud provider, Kubernetes)

✅ **Decisions with long-term impact**
- Hard to reverse later
- Affects multiple teams
- Large cost implications

✅ **Decisions with trade-offs**
- No obviously "right" answer
- Multiple stakeholders with different priorities

✅ **Changes to existing architecture**
- Deprecating a service
- Migrating to new technology
- Changing fundamental patterns

### Don't write ADRs for:
❌ **Trivial decisions**
- Library versions (use dependency management)
- Code style (use linters)
- Individual feature implementations

❌ **Obvious choices**
- No alternatives
- No trade-offs
- Standard industry practices

## ADR Workflow

### 1. Proposal Phase
```
Status: Proposed
```
- Draft ADR
- Share with team for feedback
- Iterate on alternatives and consequences
- Update based on discussion

### 2. Decision Phase
```
Status: Accepted
```
- Team/architect approves
- Commit ADR to version control
- Announce decision to team
- Begin implementation

### 3. Evolution Phase
```
Status: Deprecated
Status: Superseded by ADR-025
```
- Mark deprecated if no longer relevant
- Link to superseding ADR if replaced
- Keep historical record (don't delete)

## Tools and Organization

### File Structure
```
docs/
└── architecture/
    └── decisions/
        ├── 0001-record-architecture-decisions.md
        ├── 0002-use-postgresql-database.md
        ├── 0003-adopt-microservices.md
        ├── 0004-select-kong-api-gateway.md
        └── README.md
```

### Naming Convention
- Zero-padded numbers: `0001`, `0002`, `0003`
- Descriptive titles: `use-postgresql-database`
- Format: `NNNN-short-descriptive-title.md`

### Tools
- **adr-tools** - CLI for creating/managing ADRs
- **log4brains** - Web UI for browsing ADRs
- **ADR Manager** - VS Code extension
- **Markdown** - Keep it simple

### Installation
```bash
# adr-tools (bash)
brew install adr-tools

# Initialize ADRs in project
adr init docs/architecture/decisions

# Create new ADR
adr new "Use PostgreSQL as primary database"

# Supersede old ADR
adr new -s 5 "Use MongoDB for analytics data"
```

## ADR Best Practices

### 1. Write Early
Document decisions when they're fresh. Don't wait until implementation is done.

### 2. Be Concise
ADRs should be readable in 5-10 minutes. Link to detailed docs if needed.

### 3. Include Alternatives
Show you considered multiple options. Explain why alternatives were rejected.

### 4. Quantify Trade-offs
Use numbers when possible:
- "Reduces latency by ~50ms"
- "Costs $500/month more"
- "Requires 2 additional engineers"

### 5. Link to Context
Reference:
- Slack discussions
- Design docs
- Proof-of-concept code
- Cost analyses

### 6. Review Regularly
Revisit ADRs every 6-12 months. Update status if deprecated.

### 7. Involve Stakeholders
Get input from:
- Architects
- Team leads
- Operations
- Product (for user-facing impact)

### 8. Version Control
Commit ADRs to Git. Use pull requests for review.

## Common Mistakes

❌ **Too detailed** - ADRs aren't design docs. Keep focused.
❌ **No alternatives** - Shows lack of evaluation.
❌ **Missing consequences** - Trade-offs are key.
❌ **Deleting old ADRs** - Keep as historical record.
❌ **Not updating status** - Mark as deprecated when superseded.
❌ **Technology-focused only** - Include business context.

## Integration with Other Practices

### Relationship to:
- **RFCs** - ADRs are lighter weight, RFCs for complex proposals
- **Design docs** - ADRs capture decision, design docs capture implementation
- **Runbooks** - ADRs explain "why", runbooks explain "how to operate"

---

**Related Resources:**
- system-design-principles.md - Principles to guide decisions
- reference-architectures.md - Example architectures with implicit ADRs
- migration-architecture.md - Migration decisions to document
