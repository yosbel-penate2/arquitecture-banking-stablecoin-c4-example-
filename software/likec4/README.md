# LikeC4 Project for Stablecoin Banking

This subproject organizes the architecture of the stablecoin payment system using LikeC4 diagrams.

## Structure

- `diagrams/` - Source `.c4` files by abstraction level
- `images/` - Exported images for documentation
- `docs/` - Documentation referencing the generated images

## Included Diagrams

| Level | File | Description |
|-------|------|-------------|
| Specification | `diagrams/specification.c4` | System scope, deployment nodes, infrastructure |
| Context | `diagrams/context.c4` | External actors, system boundaries, relationships |
| Containers | `diagrams/containers.c4` | 12 containers with tech stack & tech boundaries |
| Components | `diagrams/components.c4` | 3 component views: Saga Engine, Compliance, Issuance |
| Deployment | `diagrams/deployment.c4` | AWS EKS infra — K8s, RDS, MSK, monitoring |
| Dynamic | `diagrams/dynamic.c4` | Payment saga flow with compensating TXs |

## Architecture Docs

- `docs/architecture.md` — ADRs, bounded contexts, pattern deep-dives, success metrics

## Recommended Usage

1. Edit the `.c4` files in `diagrams/`.
2. Generate or update the images in `images/`.
3. Reference the images from documents in `docs/`.
