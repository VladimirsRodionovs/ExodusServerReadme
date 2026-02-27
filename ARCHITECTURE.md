# ARCHITECTURE — ExodusServer

## Purpose
`ExodusServer` is the real-time game backend built on Netty + MySQL. It accepts client `mT` messages, routes them to handlers, executes business logic, and returns JSON responses.

## Tech stack
- Java 23
- Netty (`netty-all`)
- MySQL (`EXOLOG`)
- FastJSON / org.json
- Maven

## High-level flow
1. Client connects to Netty server (`MainServer`).
2. Pipeline applies protocol and security guards.
3. Router resolves `mT` and calls the corresponding handler.
4. Handler uses services/controllers/modules and DB layer.
5. Response is serialized and sent back to client.
6. Background loops (`WorldTimerNetty`, `FacilityProcessor`, `ShipFlightPlanController`) update world state.

## Layers
### 1) Transport / protocol
- Package: `org.test.server.NettyHandlers`
- Responsibilities:
  - receive/decode JSON frames (`JsonFrameDecoder`);
  - session/protocol guards (`InitialProtocolGuardHandler`, `SessionAuthGuardHandler`, `LoginRateLimitHandler`, `SecurityTrapHandler`);
  - route to app handlers (`MessageTypeRouterHandler`, `ProtocolMuxExoV1Handler`).

### 2) Application / handlers
- Package: `org.test.server.NettyHandlers` (`Handler*`)
- Responsibility: use-case handling by message type (login, data loading, cargo, flights, facilities, etc.).

### 3) Domain / modules / controllers
- Packages: `modules`, `controllers`
- Examples:
  - `FacilityProcessor`, `ProductionTransferService`, `CargoOperation`;
  - domain controllers: bases, contracts, exchange, population, agents.
- Responsibility: business rules and periodic computations.

### 4) Runtime state
- Package: `runtime`
- Class: `ServerRuntimeState`
- Responsibility: in-memory runtime process state.

### 5) Persistence
- Classes: `DBconnection`, `config/DbConfig`, `config/DbConnections`
- Responsibility: MySQL connection management and SQL access.

### 6) Security & observability
- Package: `security`
- Classes: `AuthSupport`, `SecurityEventLogger`
- Responsibility: security audit logs, auth support, anti-abuse.

## Key processes
### Bootstrap
- Entry point: `org.test.server.MainServer`
- Starts Netty listener and configures the pipeline.

### World ticks
- `WorldTimerNetty` coordinates periodic updates.
- `ShipFlightPlanController` updates flight plans/status.
- `FacilityProcessor` processes production cycles.

### Star system data
- Runtime operates on unified `StarSystems` table (`StarSystemID` partitioning).

## Contracts and integrations
- Client contract: `mT`-oriented JSON protocol.
- DB: MySQL `EXOLOG`.
- Neighbor projects:
  - `Starforge` writes systems into `StarSystems`.
  - `PlanetSurfaceGenerator` writes surface payloads into `PlanetsSurfaces2`.

## Extension points
- New network operation: add `Handler*` + register `mT` route.
- New world cycle: add module in `modules` + trigger from world timer.
- Stronger security: add new guard/security rules.

## Risks / tech debt
- Tight coupling between handler layer and SQL.
- Possible duplicate JavaFX dependencies in `pom.xml`.
- Limited separation between transport and domain logic.

## Quick navigation
- Entry point: `src/main/java/org/test/server/MainServer.java`
- Netty handlers: `src/main/java/org/test/server/NettyHandlers/`
- Domain modules: `src/main/java/org/test/server/modules/`
- World timer: `src/main/java/org/test/server/WorldTimer/WorldTimerNetty.java`
