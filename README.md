# Stablecoin Banking Architecture con LikeC4

## Visión General

Este proyecto modela la arquitectura de una **Plataforma Bancaria de Pagos con Stablecoins** utilizando el modelo C4 y LikeC4 como herramienta de modelado.

La arquitectura sigue el patrón **Lift-and-Shift** (Crypto-Rails, Fiat at the Edges), donde el usuario final envía y recibe moneda fiduciaria mientras que la liquidación ocurre mediante stablecoins en blockchain.

## Estructura

- `software/likec4/diagrams/` - Archivos fuente `.c4` por nivel de abstracción
- `software/likec4/images/` - Imágenes exportadas
- `software/likec4/docs/` - Documentación de arquitectura

## Diagramas incluidos

| Diagrama | Archivo | Descripción |
|----------|---------|-------------|
| Contexto | `diagrams/context.c4` | Sistema, actores externos y relaciones |
| Contenedores | `diagrams/containers.c4` | Descomposición en contenedores con componentes internos |
| Componentes | `diagrams/components.c4` | Motor Stablecoin y Servicio de Compliance |

## Patrón de Arquitectura

### Lift-and-Shift (Crypto-Rails, Fiat at the Edges)

1. El cliente inicia un pago en EUR/USD (sin cambios en UX)
2. El sistema convierte fiat a stablecoin (on-ramp)
3. La liquidación ocurre on-chain
4. El destinatario recibe fiat (off-ramp)

### Cinco Capas de Infraestructura

1. **Reserve Management** - Reservas 1:1, informes de atestación
2. **Issuance & Redemption** - Mint/burn contra reservas
3. **Payment Rails & Settlement** - Enrutamiento y conciliación
4. **Compliance & Identity** - KYC/AML, Travel Rule, sanciones
5. **Reporting & Attestation** - Auditoría y reportes regulatorios

## Referencias

- [LikeC4](https://likec4.dev/)
- [C4 Model](https://c4model.com/)
- [How to Architect a Stablecoin Payment System - PaymentTalks](https://paymenttalks.com/how-to-architect-a-stablecoin-payment-system-three-design-patterns-for-solution-architects-part-5-of-5/)
- [Stablecoin Infrastructure: What Banks Actually Need to Build in 2026 - Nextrope](https://nextrope.com/stablecoin-infrastructure-what-banks-actually-need-to-build-in-2026)
