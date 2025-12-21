# **Conversation Summary: LoRaWAN-over-Meshtastic Project**

## **Initial Goal**

*   Tunnel **LoRaWAN traffic** over **Meshtastic** so devices out of gateway range can reach a LoRaWAN Network Server (**LNS**) without changing the higher-level device implementation.
*   First implementation target: <https://github.com/mcci-catena/arduino-lmic>.
*   Bridge traffic via a dedicated Meshtastic node → Linux process → LNS (Semtech UDP first; LoRa Basics Station later).

## **Key Decisions & Pivot**

*   Dropped strict **PHY emulation** at LMIC `radio()` layer (timing constraints too brittle).
*   Adopted **Meshtastic-region model**:
    *   Preserve LoRaWAN **semantics** (join, security, Class A ordering).
    *   Constraints defined by Meshtastic transport, not LoRa PHY.
*   **Payload cap**: Meshtastic `Data.payload` = 233 B → LoRaWAN **FRMPayload ≤ 220 B** (no FOpts).
*   **Soft RX windows**: RX1 delay 5–10 s; RX2 fallback.
*   **ADR disabled** (no RF metrics).
*   **Classes supported**: Class A (initial) and Class C (possible with minimal complexity).
*   Region naming follows LMIC convention: `CFG_Meshtastic_us915`, `CFG_Meshtastic_eu868`.

## **Architecture**

    Device (LoRaWAN MAC, e.g., LMIC)
        ↓ Meshtastic transport adapter (PHYPayload bytes)
    Meshtastic node (dedicated, no relay)
        ↓ USB/Serial
    Linux bridge
        ↓ Semtech UDP / Basics Station
    The Things Stack (TTN/TTS) or ChirpStack

## **Docs Delivered**

*   `README.md`: Purpose, high-level architecture, pivot rationale.
*   `CHANGELOG.md`: Records pivot and new constraints.
*   `docs/lwom-overview.md`: Overview with diagrams, payload cap, timing.
*   `docs/lwom-implementation.md`: Implementation details, adapter API, region naming.
*   `docs/conversation_summary.md` and `docs/project_knowledge_summary.md`: Context for future contributors.

## **Next Steps**

1.  Implement LMIC Meshtastic regions (`CFG_Meshtastic_*`) with caps and soft RX semantics.
2.  Build device-side **Meshtastic transport adapter** (send/receive PHYPayload).
3.  Develop Linux bridge:
    *   Start with **Semtech UDP** for TTS.
    *   Plan migration to **Basics Station** for production.
4.  Integration tests:
    *   OTAA join and confirmed uplinks via TTS.
    *   Latency injection to validate soft RX windows.
5.  Later: Generalize to other LoRaWAN MAC stacks.

