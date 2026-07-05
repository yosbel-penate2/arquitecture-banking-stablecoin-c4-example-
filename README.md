# Stablecoin Banking Architecture with LikeC4

## Overview

This project models the architecture of a **Stablecoin Banking Payment Platform** using the C4 model with LikeC4 as the modeling tool.

The architecture follows the **Lift-and-Shift** pattern (Crypto-Rails, Fiat at the Edges), where the end user sends and receives fiat currency while settlement occurs via stablecoins on blockchain.

## Structure

- `software/likec4/diagrams/` - Source `.c4` files by abstraction level
- `software/likec4/images/` - Exported images
- `software/likec4/docs/` - Architecture documentation

## Included Diagrams

| Diagram | File | Description |
|---------|------|-------------|
| Context | `diagrams/context.c4` | System, external actors, and relationships |
| Containers | `diagrams/containers.c4` | Decomposition into containers with internal components |
| Components | `diagrams/components.c4` | Stablecoin Engine and Compliance Service |

## Architecture Pattern

### Lift-and-Shift (Crypto-Rails, Fiat at the Edges)

1. Customer initiates a payment in EUR/USD (no UX changes)
2. System converts fiat to stablecoin (on-ramp)
3. Settlement occurs on-chain
4. Recipient receives fiat (off-ramp)

### Five Infrastructure Layers

1. **Reserve Management** - 1:1 reserves, attestation reports
2. **Issuance & Redemption** - Mint/burn against reserves
3. **Payment Rails & Settlement** - Routing and reconciliation
4. **Compliance & Identity** - KYC/AML, Travel Rule, sanctions
5. **Reporting & Attestation** - Audit and regulatory reporting

## References

- [LikeC4](https://likec4.dev/)
- [C4 Model](https://c4model.com/)
- [How to Architect a Stablecoin Payment System - PaymentTalks](https://paymenttalks.com/how-to-architect-a-stablecoin-payment-system-three-design-patterns-for-solution-architects-part-5-of-5/)
- [Stablecoin Infrastructure: What Banks Actually Need to Build in 2026 - Nextrope](https://nextrope.com/stablecoin-infrastructure-what-banks-actually-need-to-build-in-2026)
