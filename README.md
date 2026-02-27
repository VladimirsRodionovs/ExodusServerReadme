# ExodusServer

Real-time backend for a persistent space game.

This is the public showcase repository. ExodusServer runs a Netty-based realtime server, validates and routes `mT` protocol messages, executes core gameplay operations, and persists state to MySQL.

## Highlights
- Low-latency persistent session model.
- Guard-first ingress pipeline (protocol/auth/rate/security checks).
- Message routing by `mT` handlers.
- Server-authoritative world ticks (flight, production, time).
- Integrated data flow with generation tools (`Starforge`, `PlanetSurfaceGenerator`).

## Tech Stack
- Java 23
- Maven
- Netty 4.1.x
- MySQL (`EXOLOG`)
- FastJSON / org.json

## Quick Start
1. Provision MySQL and schema in `EXOLOG`.
2. Create local env:
   - `cp .env.example .env`
3. Configure credentials in `.env`.
4. Build:
   - `mvn -q -DskipTests compile`
5. Run:
   - `./scripts/start-netty.sh`

## Documentation
- Internal design: `ARCHITECTURE.md`
- Protocol map: `docs/NETTY_MESSAGE_MAP.md`
- StarSystems contract: `docs/STARSYSTEMS_SINGLE_TABLE.md`
- Technical debt: `docs/TECH_DEBT.md`

## Contact
vladimirs.rodionovs@gmail.com
