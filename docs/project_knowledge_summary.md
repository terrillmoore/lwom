# Project Knowledge Summary

Goal: Tunnel LoRaWAN traffic over Meshtastic; LMIC first target.
Architecture: Device → Meshtastic → Linux bridge → LNS.
Constraints: Meshtastic Data.payload ≤ 233 B; FRMPayload ≤ 220 B.
Timing: Soft RX windows; ADR disabled.
Classes: Class A and Class C supported.
Next: Implement LMIC regions, transport adapter, bridge to TTS.
