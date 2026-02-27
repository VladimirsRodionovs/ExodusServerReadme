# ExodusServer

Game backend server (Java 23), focused on a Netty realtime pipeline.

This repository currently runs in a **Netty-only** mode (Spring layer removed).

## Stack

- Java 23
- Maven
- Netty `4.1.114.Final`
- MySQL (`EXOLOG`)
- FastJSON / org.json

## Core Architecture

- Main entry point: `src/main/java/org/test/server/MainServer.java`
- Netty handlers: `src/main/java/org/test/server/NettyHandlers/`
- Flight plan processor: `src/main/java/org/test/server/ShipFlightPlanController.java`
- Facility production processor: `src/main/java/org/test/server/modules/FacilityProcessor.java`
- World timer (Netty branch): `src/main/java/org/test/server/WorldTimer/WorldTimerNetty.java`

## Data Model

Star system runtime logic uses a unified table:

- `StarSystems`
- partition key: `StarSystemID`

Legacy runtime access through per-system tables (`StarSystem_X`) has been replaced.

## Security Guards (Netty)

- `InitialProtocolGuardHandler`:
  - closes connection if no valid first protocol packet arrives in time (15s);
  - closes connection when first `mT` is unsupported.
- `SessionAuthGuardHandler`:
  - blocks protected message types for non-authenticated sessions.
- `LoginRateLimitHandler`:
  - rate-limits `MT1/MT2` (anti brute-force).
- `SecurityTrapHandler`:
  - detects SQLi-like patterns / anomalous payloads / honeypot `mT`;
  - temporary IP block + security audit logging.
- `JsonFrameDecoder`:
  - max inbound frame: `64KB`.

## Local Setup

1. Provision MySQL and required schema in database `EXOLOG`.
2. Create local env file from template:

```bash
cp .env.example .env
```

3. Fill credentials in `.env`:

```env
EXO_DB_URL=jdbc:mysql://localhost:3306/EXOLOG
EXO_DB_USER=YOUR_DB_USER
EXO_DB_PASSWORD=YOUR_DB_PASSWORD
```

## Run

Preferred startup script:

```bash
./scripts/start-netty.sh
```

Manual compile:

```bash
mvn -q compile
```

Manual entry point (IDE/CLI):

- `org.test.server.MainServer`

## Additional Docs

- Netty message map: `docs/NETTY_MESSAGE_MAP.md`
- Single-table StarSystems contract: `docs/STARSYSTEMS_SINGLE_TABLE.md`
- Player request types: `docs/PLAYER_REQUEST_TYPES.md`
- Technical debt list: `docs/TECH_DEBT.md`

## Notes

-
