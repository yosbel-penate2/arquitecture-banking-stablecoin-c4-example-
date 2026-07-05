# Documentación de Arquitectura LikeC4

Guía de referencia de los diagramas del proyecto LikeC4 para la plataforma de pagos con stablecoins.

## Diagramas Disponibles

### 1. Diagrama de Contexto
Fuente: [../diagrams/context.c4](../diagrams/context.c4)

Muestra el sistema **Plataforma Stablecoin Banking** y sus interacciones con actores externos:
- Clientes (personas)
- Comercios
- Reguladores (MiCA, GENIUS Act)
- Banco Socio (reservas fiduciarias)
- Red Blockchain (Ethereum/L2)
- Core Bancario Legacy

![Diagrama de contexto](../images/context.svg)

### 2. Diagrama de Contenedores
Fuente: [../diagrams/containers.c4](../diagrams/containers.c4)

Descomposición del sistema en contenedores:
- **Frontend:** App Web (Angular), App Móvil (React Native)
- **Gateway:** API Gateway (Kong)
- **Core:** Adaptador Core Bancario, Motor Stablecoin, Servicio de Emisión, Gestor de Reservas
- **Blockchain:** Nodo Blockchain, Indexador On-Chain, Servicio de Wallets
- **Compliance:** Servicio de Compliance con KYC/AML/Travel Rule
- **Datos:** Base de Datos PostgreSQL

![Diagrama de contenedores](../images/containers.svg)

### 3. Diagrama de Componentes
Fuente: [../diagrams/components.c4](../diagrams/components.c4)

Descomposición interna de los contenedores clave:
- **Motor Stablecoin:** PaymentOrchestrator, FX Provider, Pool de Liquidez, On-Ramp/Off-Ramp Adapters, StatusTracker
- **Servicio de Compliance:** IdentityVerifier, AML Screener, TravelRuleEngine, SanctionsChecker, AuditLogger

![Diagrama de componentes](../images/components.svg)

## Flujo de Trabajo Recomendado

1. Actualiza el archivo `.c4` correspondiente.
2. Exporta o guarda la imagen equivalente en `images/`.
3. Referencia la imagen desde esta documentación para mantenerla visible.
