# LoRaWAN-over-Meshtastic (LWoM) — Overview

This document explains the updated architecture: Meshtastic as transport, preserving LoRaWAN semantics without PHY emulation.

- Purpose: Tunnel LoRaWAN traffic over Meshtastic; LMIC high-level API unchanged.
- Constraints: Meshtastic Data.payload ≤ 233 B → FRMPayload ≤ 220 B.
- Timing: Soft RX windows (RX1 delay 5–10 s).
- ADR: Disabled.
- Classes: Class A and Class C supported.

See README.md for diagrams and high-level details.
