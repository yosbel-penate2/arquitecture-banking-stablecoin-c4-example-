# Architecture Documentation - LikeC4

Reference guide for the LikeC4 architecture diagrams of the Stablecoin Payment Platform.

## Available Diagrams

### 1. Context Diagram
Source: [../diagrams/context.c4](../diagrams/context.c4)

![Context diagram](../images/context.svg)

### 2. Container Diagram
Source: [../diagrams/containers.c4](../diagrams/containers.c4)

![Container diagram](../images/containers.svg)

### 3. Component Diagrams
Source: [../diagrams/components.c4](../diagrams/components.c4)

![Component diagrams](../images/components.svg)

## Applied Architectural Patterns

### Lift-and-Shift (Crypto-Rails, Fiat at the Edges)
Main system pattern. The user sends and receives fiat currency without changing their experience. Settlement occurs via stablecoins on blockchain. The On-Ramp and Off-Ramp Adapters convert between fiat and crypto worlds.

### Process Manager / Saga
The `PaymentOrchestrator` within the Stablecoin Engine orchestrates a 6-step saga: fetch FX rate, check liquidity, deposit fiat, send TX on-chain, monitor confirmation, withdraw to fiat. The `SagaLogger` records each step to enable compensating transactions.

### Anti-Corruption Layer
The `Core Banking Adapter` isolates the stablecoin domain from legacy core systems (Temenos, SAP), translating protocols and semantics between both worlds.

### Ports & Adapters (Hexagonal Architecture)
Each adapter in the Stablecoin Engine (`OnRampAdapter`, `OffRampAdapter`, `BlockchainAdapter`, `FX Provider`, `Liquidity Pool`) is a port that encapsulates external technology behind a clean interface.

### Event-Driven Architecture
The `Event Bus` (Redis/Kafka) decouples services via domain and blockchain topics. The `On-Chain Indexer` publishes confirmations, the `StatusTracker` consumes them, and the `NotificationDispatcher` triggers alerts.

### CQRS (Command Query Responsibility Segregation)
Blockchain acts as the source of truth (event store / write model). PostgreSQL serves as the read model for status queries, accounting, and reporting.

### Off-chain Compliance + On-chain Audit
Compliance logic (KYC, AML, Travel Rule, sanctions) runs off-chain for performance and flexibility. Each result is anchored on blockchain via the `AuditLogger` as an immutable notary (on-chain anchor).

### Factory Pattern
`MintController` and `BurnController` implement the Factory pattern to create and destroy stablecoins against 1:1 reserves.

## Recommended Workflow

1. Update the corresponding `.c4` file.
2. Export or save the equivalent image in `images/`.
3. Reference the image from this documentation to keep it visible.
