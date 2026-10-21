# 📘 Blueprint v1 — Multi‑Instrument Simulation Framework  
**Version:** 1.0  
**Author:** Boyd  
**Date:** 2026‑10‑21  

---

# Table of Contents
1. Executive Summary  
2. Mission Statement  
3. Project Purpose  
4. Architecture Overview  
5. Unified Message Pipeline  
6. Device Protocol Table  
7. Client Layer  
8. Driver Layer  
9. Protocol Layer  
10. Encoding Layer  
11. Framing Layer  
12. Transport Layer  
13. Transport TDD Rules  
14. Transport Scaffolding Rules  
15. Adapter Layer  
16. Device Simulation Layer  
17. Unified Command Model  
18. Unified Error Taxonomy  
19. Simulation Fidelity Model  
20. Simulation Fidelity Formulas  
21. Device Capability Model  
22. Telemetry & Logging  
23. Configuration Hierarchy  
24. Dependency Injection Graph  
25. Router Layer  
26. FastAPI Integration  
27. FastAPI Deployment  
28. Test Suite  
29. Project Structure  
30. Developer Onboarding  
31. Device Metadata Schema  
32. Device Creation Checklist  
33. Device Registry Pattern  
34. Device Simulator Template  
35. Device State Machine Template  
36. Auto‑Generation Rules  
37. Device Roadmap  
38. AI Development Workflow  
39. Environment Setup Appendix  
40. Git Workflow Appendix  
41. Architecture Diagrams Appendix  
42. VS Code + GitHub Development Workflow  
43. Appendices  

---

# 1. Executive Summary
Blueprint v5 defines a modular, extensible simulation framework supporting multiple instruments, protocols, transports, and AI‑driven behavior. This version adds a complete Device Creation System, AI development workflow, transport TDD rules, environment setup, Git workflow, deployment, and architecture diagrams.

---

# 2. Mission Statement
Create a realistic, testable, multi‑instrument simulation environment supporting expert and abstract clients across SCPI, ASCII, JSON, and binary protocols.

---

# 3. Project Purpose
- Build a portfolio‑grade simulation framework  
- Demonstrate multi‑instrument, multi‑protocol control  
- Use AI‑accelerated development  
- Target high‑demand engineering domains  
- Support consulting and independent development  

---

# 4. Architecture Overview
```
Client → Driver → Protocol → Encoding → Framing → Transport → Device
```

---

# 5. Unified Message Pipeline
```
Client ↔ Driver ↔ Protocol ↔ Encoder ↔ Framer ↔ Transport ↔ Device
```

---

# 6. Device Protocol Table
| Device | Make | Model | Protocol | Encoding | Framing | Transport |
|--------|------|--------|----------|----------|---------|-----------|
| LaserSourceSim | Thorlabs | LDC205C | SCPI | ASCII | CRLF | Serial‑TCP |
| PowerMeterSim | Keysight | 81634B | SCPI | ASCII | CRLF | Serial‑TCP |
| KoradPSUSim | Korad | KA3005P | Vendor ASCII | ASCII | CRLF | Serial‑TCP |
| SwitchSim | Pickering | 40‑883A | Vendor ASCII | ASCII | CRLF | Serial‑TCP |
| TektronixMSOSim | Tektronix | MSO58 | SCPI+Binary | ASCII+Binary | CRLF+BinBlock | TCP |
| VirtualAISim | Virtual Device | AI‑Adaptive v1 | JSON | UTF‑8 JSON | Newline | In‑process/TCP |

---

# 7. Client Layer
Expert Client: raw SCPI/ASCII/Binary.  
Abstract Client: high‑level operations via drivers.

---

# 8. Driver Layer
Maps abstract operations to protocol commands. Transport‑agnostic, grammar‑aware, fully testable.

---

# 9. Protocol Layer
Supports SCPI, vendor ASCII, JSON, binary block. Parses, validates, normalizes, throws protocol errors.

---

# 10. Encoding Layer
ASCII, JSON, Binary. Stateless, deterministic.

---

# 11. Framing Layer
CRLF, newline, binary block headers. Detects incomplete frames.

---

# 12. Transport Layer
TCP, Serial‑over‑TCP, In‑process. Async I/O, timeouts, reconnection, transport errors.

---

# 13. Transport TDD Rules
- Transport tests must be written before implementation.  
- Timeout behavior must be fully tested.  
- Transport errors must propagate through all layers.  
- No transport code may be written without tests.

---

# 14. Transport Scaffolding Rules
- Transport must expose async send/receive.  
- Must support reconnect.  
- Must support configurable timeouts.  
- Must emit telemetry events.  
- Must integrate with DI graph.

---

# 15. Adapter Layer
Composition of encoder + framer + transport. Provides `send()` and `receive()`.

---

# 16. Device Simulation Layer
Each device includes internal state, timing behavior, error codes, busy states, warm‑up profiles, noise models, drift models, self‑test, snapshot/restore, deterministic/stochastic modes, unified error register, metadata schema.

---

# 17. Unified Command Model
```
Command(name="MEAS:VOLT?", args={}, device="PowerMeterSim")
```

---

# 18. Unified Error Taxonomy
Categories: Encoding, Framing, Transport, Protocol, Device, Driver, Client. Includes code, message, origin, propagation.

---

# 19. Simulation Fidelity Model
Simulates warm‑up, delays, settling, busy states, noise, drift, error injection, time‑dependent behavior.

---

# 20. Simulation Fidelity Formulas
Gaussian noise:
```
value = ideal + random.gauss(0, stddev)
```

Drift:
```
value += drift_rate * time
```

Warm‑up:
```
output = target * (1 - exp(-t / tau))
```

---

# 21. Device Capability Model
Declares commands, ranges, modes, timing, errors, measurement types.

---

# 22. Telemetry & Logging
Logs: command, response, error, state, transport, timing. Levels: TRACE, DEBUG, INFO, WARN, ERROR.

---

# 23. Configuration Hierarchy
Global → Device → Transport → Protocol → Driver → Logging. Formats: JSON, YAML.

---

# 24. Dependency Injection Graph
Defines component graph, lifetimes, factories, injection points.

---

# 25. Router Layer
Selects device, protocol, adapter, driver. Supports expert and abstract paths.

---

# 26. FastAPI Integration
Endpoints for expert, abstract, metadata, logging, configuration.

---

# 27. FastAPI Deployment
- Run with uvicorn  
- Dockerfile for containerization  
- Expose ports 8000/8001  
- WSL2 networking notes  
- Optional reverse proxy  

---

# 28. Test Suite
Tests for encoding, framing, transport, protocol, drivers, devices, router, API, integration, fidelity.

---

# 29. Project Structure
```
/src
  /clients
  /drivers
  /protocols
  /encoding
  /framing
  /transport
  /adapters
  /devices
  /router
  /api
  /config
  /errors
  /telemetry
/tests
/docs
```

---

# 30. Developer Onboarding
How to add devices, protocols, transports, drivers, clients.

---

# 31. Device Metadata Schema
```
device:
  name: LaserSourceSim
  make: Thorlabs
  model: LDC205C

protocol:
  type: SCPI
  encoding: ASCII
  framing: CRLF

transport:
  type: serial_tcp
  port: 5025

capabilities:
  commands:
    - "SOUR:POW?"
    - "SOUR:POW <value>"
  ranges:
    power_mw: [0, 100]
  modes:
    - constant_power

timing:
  warmup_seconds: 3
  settling_ms: 150
  measurement_delay_ms: 50

noise:
  type: gaussian
  stddev: 0.01

lifecycle:
  initial_state: OFF
  states:
    - OFF
    - INIT
    - READY
    - BUSY
    - ERROR

errors:
  - code: -100
    message: "Invalid command"
  - code: -200
    message: "Execution error"
```

---

# 32. Device Creation Checklist
```
1. Create folder: /src/devices/<DeviceName>/
2. Add metadata.yaml using the standard schema
3. Implement simulator.py using the template
4. Register device in DeviceRegistry
5. Run auto‑generation for:
   - driver
   - protocol handler
   - FastAPI endpoints
6. Add tests in /src/devices/<DeviceName>/tests/
7. Start simulation
```

---

# 33. Device Registry Pattern
```
DeviceRegistry.register(
    "LaserSourceSim",
    metadata_path="devices/LaserSourceSim/metadata.yaml"
)
```

---

# 34. Device Simulator Template
```
class DeviceSimulator:
    def __init__(self, metadata):
        self.metadata = metadata
        self.state = metadata.lifecycle.initial_state
        self.error_queue = []
        self.timebase = GlobalClock()

    def handle_command(self, cmd):
        # validate command
        # update state machine
        # apply timing model
        # apply noise model
        # return response
        pass
```

---

# 35. Device State Machine Template
```
states:
  OFF:
    on_init: INIT
  INIT:
    on_ready: READY
  READY:
    on_measure: BUSY
  BUSY:
    on_complete: READY
  ERROR:
    on_reset: READY
```

---

# 36. Auto‑Generation Rules
From metadata.yaml, generate:

- protocol handler  
- driver  
- adapter wiring  
- FastAPI endpoints  
- capability responses  
- error register  
- state machine  
- warm‑up behavior  
- noise model  

---

# 37. Device Roadmap
- SCPI optical instruments  
- SCPI RF instruments  
- Vendor ASCII power supplies  
- Virtual AI‑adaptive devices  
- Binary‑heavy oscilloscopes  
- Multi‑device synchronization systems  

---

# 38. AI Development Workflow
- Blueprint → AI → scaffolding  
- metadata.yaml → AI → driver/protocol/endpoint generation  
- AI‑assisted refactoring  
- AI‑assisted testing  
- AI‑assisted device creation  

---

# 39. Environment Setup Appendix
- WSL2 installation  
- Virtualization enabling  
- Docker Desktop setup  
- Git installation  
- Git identity configuration  
- Local bare remote creation  
- Repo initialization  

---

# 40. Git Workflow Appendix
- Commit discipline  
- Branching model  
- Repo structure  
- WSL2 + Windows integration  
- Using `code .` in WSL2  

---

# 41. Architecture Diagrams Appendix

Pipeline:
```
flowchart LR
    Client --> Driver --> Protocol --> Encoding --> Framing --> Transport --> Device
```

Device creation:
```
flowchart TD
    Metadata --> Autogen --> Driver
    Metadata --> Autogen --> ProtocolHandler
    Metadata --> Autogen --> FastAPI
    Metadata --> Simulator
```

---

# 42. VS Code + GitHub Development Workflow

## 42.1 Overview  
The project is developed using VS Code as the primary editor and GitHub as the remote repository and documentation host. Code is stored and executed inside WSL2, while VS Code provides the Windows UI.

## 42.2 Required VS Code Extensions  
- Python  
- Pylance  
- YAML  
- Markdown All in One  
- Markdown Preview Enhanced  
- Mermaid Markdown Syntax Highlighting  
- GitLens  
- Docker  
- WSL  

## 42.3 Recommended VS Code Settings  
```
{
  "editor.formatOnSave": true,
  "files.trimTrailingWhitespace": true,
  "files.insertFinalNewline": true,
  "python.analysis.typeCheckingMode": "basic",
  "yaml.validate": true,
  "markdown.preview.breaks": true,
  "markdown.extension.toc.updateOnSave": true
}
```

## 42.4 GitHub Repository Structure  
```
multi-instrument-sim-framework/
  README.md
  Blueprint_v5.md
  LICENSE
  /src
  /tests
  /docs
  .gitignore
  .vscode/
```

## 42.5 Git Workflow  
Commit conventions:  
- feat:  
- fix:  
- docs:  
- test:  
- refactor:  

Branching model:  
- main  
- dev  
- feature/<device-name>  
- feature/autogen  

## 42.6 WSL2 Integration  
All code lives inside WSL2:

```
~/projects/multi-instrument-sim-framework
```

Open the project in VS Code:

```
code .
```

## 42.7 GitHub as Portfolio  
GitHub serves as documentation host, portfolio showcase, CI/CD pipeline, issue tracker, and device catalog browser.

## 42.8 Device Development Workflow  
1. Create device folder  
2. Add metadata.yaml  
3. Run autogen scripts  
4. Implement simulator.py  
5. Add tests  
6. Commit + push  
7. GitHub renders everything  

## 42.9 Blueprint Integration  
Blueprint_v5.md is stored at the root of the repo and acts as the master architecture document, AI ingestion source, developer onboarding guide, and device creation reference.

## 42.10 Summary  
VS Code + GitHub + WSL2 provides a modern, reproducible, AI‑accelerated development environment for the multi‑instrument simulation framework.

---

# 43. Appendices
- SCPI grammar  
- JSON schema  
- Binary block format  
- Capability definitions  
- Error code tables  
