# TalkieWalkie

A portable 2.4 GHz digital walkie-talkie — designed with KiCad. TalkieWalkie is a compact, battery-powered two-way radio that transmits and receives voice over the 2.4 GHz ISM band using the Nordic nRF24L01 transceiver.

## Features

- **2.4 GHz Wireless Communication** — nRF24L01+ module for digital voice/data transmission
- **Full-Duplex Audio** — Nuvoton NAU8325 audio codec with dual-channel MEMS microphones
- **OLED Display** — 0.91" 128×32 I²C OLED for status, settings, and feedback
- **MicroSD Storage** — Onboard MicroSD slot for audio recording, firmware updates, or configuration
- **RGB LED Indicator** — Multi-color status LED for TX/RX/power indication
- **Battery Powered** — Single 18650 Li-Ion cell for portable operation
- **Compact Board** — 1.6 mm standard thickness, 2-layer PCB

## Hardware

### Bill of Materials

| Reference | Component | Description |
|-----------|-----------|-------------|
| U1 | GLYPH-S3 | Main MCU module (28-pin castellated) |
| U2 | nRF24L01 Breakout | 2.4 GHz RF transceiver module |
| U3 | GMOD-NAU8325 | Audio codec module (Nuvoton NAU8325) |
| U4 | Gsense-2CH-MIC | Dual-channel MEMS microphone module |
| U5 | ER-OLEDM0.91-1x-I2C | 0.91" 128×32 OLED display |
| U6 | GMOD-MICRO SD CARD | MicroSD card slot module |
| BT1 | BK-18650-PC2 | 18650 Li-Ion battery holder |
| D1 | LED_KRGB | RGB LED (common cathode) |
| SW1 | SW_Push | Tactile push button (Omron B3F-315x angled) |
| J1 | Conn_01x05_Pin | 5-pin programming/debug header (1.27 mm pitch) |
| J2,J3 | Conn_01x02_Pin | 2-pin headers (expansion/GPIO) |

### Board Specifications

| Parameter | Value |
|-----------|-------|
| Layers | 2 |
| Thickness | 1.6 mm |
| Min track width | 0.2 mm |
| Min clearance | 0.2 mm |
| Via diameter | 0.6 mm |
| Via drill | 0.3 mm |
| Power rails | +3.3 V, GND |
| Battery | 1× 18650 Li-Ion (3.7 V nominal) |

## System Architecture

```
┌─────────────────────────────────────────────────────┐
│                    TalkieWalkie                      │
│                                                      │
│  ┌──────────┐   ┌──────────┐   ┌───────────────┐   │
│  │ 18650    │──▶│ GLYPH-S3 │◀──│ OLED 0.91"    │   │
│  │ Battery  │   │  (MCU)   │   │ (I²C Display) │   │
│  └──────────┘   └─┬──┬──┬──┘   └───────────────┘   │
│                   │  │  │                           │
│         ┌─────────┘  │  └──────────┐               │
│         ▼            ▼             ▼               │
│  ┌───────────┐ ┌──────────┐ ┌───────────┐         │
│  │ nRF24L01  │ │ NAU8325  │ │ MicroSD   │         │
│  │ 2.4GHz RF │ │ Audio    │ │ Storage   │         │
│  └───────────┘ │ Codec    │ └───────────┘         │
│                └────┬─────┘                        │
│                     │                              │
│                ┌────▼─────┐                        │
│                │ Dual-MIC │                        │
│                │ Gsense   │                        │
│                └──────────┘                        │
│                                                      │
│  ┌──────────┐                      ┌────────────┐  │
│  │ RGB LED  │                      │ Debug/Exp  │  │
│  │ (Status) │                      │ Headers    │  │
│  └──────────┘                      └────────────┘  │
└─────────────────────────────────────────────────────┘
```

### Signal Flow

1. **TX Path**: Dual MEMS mics → NAU8325 ADC → GLYPH-S3 (encode/compress) → nRF24L01 (GFSK TX)
2. **RX Path**: nRF24L01 (GFSK RX) → GLYPH-S3 (decode/decompress) → NAU8325 DAC → speaker/headphone out
3. **Storage**: Audio streams can be recorded to MicroSD for later playback or logging
4. **UI**: OLED shows channel, battery level, signal strength; RGB LED indicates TX/RX/standby

## How It Works

The GLYPH-S3 MCU orchestrates all peripherals over SPI and I²C buses. During transmission, the NAU8325 captures and digitizes audio from the dual MEMS microphones. The MCU encodes the PCM stream (potentially with compression) and packets it out over the nRF24L01 in the 2.4 GHz band. On the receive side, the nRF24L01 demodulates incoming packets, the MCU decodes the stream, and the NAU8325 drives an external speaker.

The OLED provides a real-time interface showing channel selection, battery gauge, and link status. The RGB LED gives at-a-glance feedback — red for TX, green for RX, blue for idle/standby.

### Protocol Considerations

- nRF24L01 operates at up to 2 Mbps over-the-air data rate
- Enhanced ShockBurst™ for automatic packet handling and acknowledgements
- 125 RF channels (2.400 – 2.525 GHz), 1 MHz spacing
- Payload up to 32 bytes per packet — requires audio stream segmentation

## Project Status

- [x] Schematic design
- [x] Footprint assignment
- [x] Component placement
- [ ] PCB routing (in progress)
- [ ] Design rule check (DRC)
- [ ] Fabrication & assembly
- [ ] Firmware development

## Repository Structure

```
talkiewalkie/
├── walkietalkie.kicad_pro   # KiCad project file
├── walkietalkie.kicad_sch   # Schematic (top-level sheet)
├── walkietalkie.kicad_pcb   # PCB layout
├── walkietalkie.kicad_prl   # Local project settings
├── README.md                # This file
└── .history/                # Schematic revision history
```

## Tools

- **KiCad** — PCB design (schematic capture + board layout)
- Designed by **PCB CUPID**

## License

Hardware design files are provided as-is for reference and educational purposes.
