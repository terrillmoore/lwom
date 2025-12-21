# LoRaWAN-over-Meshtastic (LWoM) — Implementation Details

- LMIC regions: CFG_Meshtastic_us915, CFG_Meshtastic_eu868.
- Payload cap: PHYPayload ≤ 233 B; FRMPayload ≤ 220 B.
- Soft RX windows: RX1 delay 5–10 s; RX2 fallback.
- ADR disabled.
- Transport adapter API:
```c
bool meshtastic_transport_send(const uint8_t* phy, size_t phy_len);
void meshtastic_transport_on_downlink(lmic_downlink_cb_t cb);
```
- Bridge: UDP first; Basics Station later.
- Classes: Class A and Class C semantics supported.
