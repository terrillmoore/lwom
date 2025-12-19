# Project Knowledge Summary: LoRaWAN-over-Meshtastic (LWoM)

## Project Goal
Enable **LoRaWAN Class A devices** (using LMIC) to communicate when out of range of a LoRaWAN gateway by tunneling LoRaWAN traffic over **Meshtastic**. The LMIC API remains unchanged for upper-level firmware.

---

## Architecture Overview
- **Device-side LMIC Shim**: Implements LMIC radio HAL, encapsulates PHYPayload into Meshtastic `Data` messages on a private port.
- **Dedicated Meshtastic Receiver**: Always-on node connected via USB to Linux.
- **Linux Bridge (Pseudo-Gateway)**: Emulates Semtech UDP Packet Forwarder initially; later supports LoRa Basics Station.
- **LNS**: ChirpStack or The Things Stack.

### System Diagram
```
End Device (LMIC) -> Meshtastic Node -> Linux Bridge -> LNS
```

---

## Components
1. **LMIC Shim**
   - TX: Wrap PHYPayload + meta into LwomFrame; send via Meshtastic.
   - RX: Open RX1/RX2 windows; inject downlink bytes from Meshtastic.
   - Power: Keep Meshtastic mostly off; no relay.

2. **Meshtastic Receiver**
   - USB link to Linux; forwards uplinks and downlinks.

3. **Linux Bridge**
   - Semtech UDP: `rxpk` uplinks, `txpk` downlinks.
   - Downlink scheduler: compute deadlines; forward via Meshtastic.

---

## Wire Format
**LwomFrame fields**:
- dir: UPLINK | DOWNLINK
- phy_payload: LoRaWAN PHYPayload (opaque)
- freq_mhz, datr, codr, txpwr_dbm
- tmst_hint
- optional: dev_eui, fcnt, rssi_dbm, lsnr_db

---

## Timing Strategy
- Default RX1=1s, RX2=2s after TX end.
- Plan: Increase RX1 delay to 5s via MAC (RXTimingSetupReq).
- Use RX2 as fallback.

---

## US915 Region Details
- Uplink DRs: DR0–DR4 (SF10–SF7 @125kHz; SF8@500kHz).
- Downlink DRs: DR8–DR13 (SF12–SF7 @500kHz).
- Downlink freqs: 923.3–927.5 MHz; RX2 default: 923.3 MHz @ DR8.

---

## Risks & Limitations
- ADR unreliable (synthetic metrics; no gateway diversity).
- No geolocation; single virtual gateway.
- Class B/C out of scope.
- Regulatory compliance: ensure Meshtastic RF usage is legal.

---

## Testing Plan
- **Unit**: DR/freq mapping; LwomFrame serialization; rxpk JSON generation.
- **Integration**: ChirpStack/TTS with UDP gateway; OTAA join; latency injection.
- **HIL**: Real Meshtastic node + LMIC device.

---

## Deliverables
- `LwomFrame.proto` + nanopb generation.
- LMIC shim implementation.
- Linux UDP bridge.
- Dockerized LNS for tests.
- CI with unit/integration tests.

---

## Next Steps
1. Confirm LNS choice (ChirpStack vs TTS).
2. Decide ADR policy (disable initially).
3. Implement LMIC shim and UDP bridge.
4. Integration tests with OTAA join.
5. Add Basics Station backend later.
