# Architecture Documentation - LikeC4

Reference guide for the LikeC4 architecture diagrams of the Stablecoin Payment Platform.

## Diagrams

| Diagram | File | Description |
|---------|------|-------------|
| Context | `diagrams/context.c4` | System boundaries, external actors (customer, merchant, regulator, auditor, bank, blockchain, custody) |
| Containers | `diagrams/containers.c4` | 16 containers with bounded contexts, technology stack, and relationships |
| Components | `diagrams/components.c4` | 3 component views: Stablecoin Engine (Saga), Compliance (On-chain Anchor), Issuance (Factory) |
| Deployment | `diagrams/deployment.c4` | AWS EKS deployment — pods, RDS, MSK, observability stack |
| Dynamic | `diagrams/dynamic.c4` | Payment saga flow with compensating transactions |

## Architecture Decision Records (ADRs)

### ADR-001: Apache Kafka as Event Broker

**Status:** Accepted

**Context:** The system needs a durable, append-only event log for event sourcing, saga orchestration, and CQRS projections. Redis Pub/Sub is ephemeral and lacks offset management.

**Decision:** Use Apache Kafka 3.x with MSK. Topics: `platform.payment.*`, `blockchain.tx.*`. Schema registry for event evolution. Outbox pattern for at-least-once delivery.

**Consequences:**
- + Durable event log, replayable for audit and recovery
- + Consumer groups for independent scaling
- - Additional operational complexity (KSQL, schema registry)
- - Not ideal for low-latency RPC (keep gRPC for sync calls)

### ADR-002: Rust/Go for Payment Engine, Python for Compliance

**Status:** Accepted

**Context:** Payment orchestration requires high throughput and low latency. Compliance logic changes frequently (regulatory updates) and needs fast iteration.

**Decision:** Rust (Axum) for the Stablecoin Engine core (performance-critical saga orchestration). Go (Chi) for issuance and wallet services (good library support for Ethereum SDK). Python (FastAPI) for compliance (ML libraries, fast prototyping). Spring Boot for core banking adapter (existing enterprise integration libraries).

**Consequences:**
- + Right tool for each domain
- - Polyglot operational overhead (monitoring, CI/CD)
- - Team must span multiple ecosystems

### ADR-003: CQRS with Blockchain as Write Model

**Status:** Accepted

**Context:** The system has two distinct access patterns: transactional writes (immutable log of state transitions) and analytical reads (reporting, accounting, status queries).

**Decision:** Blockchain (Ethereum/Solana) serves as the CQRS write model (source of truth, event store). PostgreSQL serves as the denormalized read model optimized for queries. The On-Chain Indexer projects blockchain events to the read model.

**Consequences:**
- + Immutable audit trail
- + Read model can be optimized without affecting write path
- - Eventual consistency between write and read models
- - Indexer must handle reorgs and duplicate events gracefully

### ADR-004: Process Manager Saga vs Choreography

**Status:** Accepted

**Context:** Payment lifecycle spans 6 steps across multiple services (FX, liquidity, on-ramp, TX submission, confirmation, off-ramp). Need clear orchestration and rollback.

**Decision:** Use **Process Manager** (orchestrator pattern) via the PaymentOrchestrator component. Choreography (event-based coordination) would create circular dependencies and make rollback logic harder to trace.

**Consequences:**
- + Single orchestrator makes saga flow testable and observable
- + Clear compensating transaction logic
- - Orchestrator is a potential single point of failure (mitigate with idempotency + retries)

## Bounded Contexts (DDD)

| Context | Description | Containers |
|---------|-------------|------------|
| **Payment Orchestration** | Initiates and tracks the 6-step payment saga | Stablecoin Engine, StatusTracker |
| **Token Issuance** | Manages stablecoin supply (mint/burn) against 1:1 reserves | Issuance Service, Reserve Manager |
| **Compliance & Identity** | KYC/AML, Travel Rule, sanctions screening, on-chain audit anchoring | Compliance Service |
| **Wallet & Signing** | Key management, TX construction and signing via HSM | Wallet Service |
| **Core Banking Integration** | Anti-Corruption Layer for legacy core banking (Temenos/SAP) | Core Banking Adapter |
| **On-Chain Data** | Blockchain event indexing and normalization | Indexer, Blockchain Node |
| **Notifications** | Multi-channel alerting (email, SMS, push, webhook) | Notification Service |

## Architecture Patterns (Detailed)

### Lift-and-Shift (Crypto-Rails, Fiat at the Edges)
End users send/receive fiat (EUR/USD) with no crypto UX. Settlement occurs via USDC/USDT on Ethereum/Solana. On-Ramp/Off-Ramp Adapters convert between fiat and stablecoin transparently.

### Process Manager / Saga (Orchestrator)
6-step saga implemented as a state machine in Rust: `fx rate → liquidity check → fiat deposit → on-chain TX → confirmation → fiat withdrawal`. Each step persists to SagaLogger for compensating transactions.

### Hexagonal Architecture (Ports & Adapters)
Core domain (Stablecoin Engine) depends on ports/interfaces, not external implementations. Adapters: `OnRampAdapter`, `OffRampAdapter`, `BlockchainAdapter`, `FX Provider`, `Liquidity Pool`.

### Event-Driven Architecture (Apache Kafka)
Domain events (`PaymentStarted`, `PaymentCompleted`, `TXConfirmed`) decouple services. Outbox pattern ensures at-least-once delivery. Schema registry enforces compatibility.

### CQRS (Blockchain + PostgreSQL)
- **Write side:** Blockchain (immutable event log, source of truth)
- **Read side:** PostgreSQL 16 (denormalized tables, optimized for queries and reporting)
- **Projection:** On-Chain Indexer subscribes to blockchain logs and syncs read model

### Anti-Corruption Layer
Core Banking Adapter (Spring Boot) translates between stablecoin domain semantics and legacy core banking protocols (Temenos, SAP). Prevents domain corruption from legacy system quirks.

### Off-chain Compliance + On-chain Anchor
Compliance runs off-chain (Python/FastAPI) for flexibility and performance. Each check result (KYC, AML, sanctions, Travel Rule) is hashed and anchored on Ethereum via AuditLogger — satisfying regulatory audit without exposing PII.

### Factory Pattern (Mint/Burn)
MintController creates ERC-20/SPL tokens against 1:1 fiat reserve. BurnController destroys tokens and releases reserve. AttestationProvider generates monthly third-party attestation reports.

## Success Metrics

| Metric | Target | How Measured |
|--------|--------|--------------|
| Payment settlement time | < 60s (fiat to stablecoin) | Distributed tracing (Tempo) |
| Saga completion rate | > 99.5% | Grafana dashboard per step |
| Compliance check latency | < 500ms p95 | Prometheus histograms |
| Read model freshness | < 3s behind blockchain | Consumer lag monitoring |
| Deployment frequency | Daily | CI/CD pipeline metrics |

## Recommended Workflow

1. Update the corresponding `.c4` file.
2. Export or save the equivalent image in `images/`.
3. Reference the image from this documentation to keep it visible.
