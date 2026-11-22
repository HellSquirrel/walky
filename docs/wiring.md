# walky: electrical schema, r3 (2026-11-22)

Status: design on paper. Nothing here has been built or measured. Every pin choice is a proposal to
be proven on the breadboard first; the "Not verified" list at the end says what must be checked
with the meter before power.

## 1. Power

```
                 10 A blade fuse          rocker switch 20 A
 BATTERY  (+) ───[ F1 ]──────────────── IN          OUT ───────────┬────────────── 12 V RAIL (+)
 3S LiPo                                                            │
 11.1 V   (−) ──────────────────────────────────────────────────────│──┬─────────── GROUND RAIL (−)
 XT60                                   (the switch breaks the      │  │
                                         + line only)               │  │
                                                                    │  │
        ┌───────────────────────────────────────────────────────────┤  │
        │                                                           │  │
        │  SERVO BRANCH (16 AWG)                    PI BRANCH       │  │
        ▼                                                           ▼  ▼
  ┌──────────────────────────┐                       [ F2 5 A ]──► WITTY PI 5 HAT+ (battery input)
  │ SERVO HUB (Wago, 5 ways) │                                          │ sits on the Pi's 40-pin header
  │ screw terminal: + and −  │                                          ▼ 5 V through the header
  │                          │                                    RASPBERRY PI 5 (USB-C left empty)
  │ socket 1 ── servo board  │                                          │ USB-A port
  │ socket 2 ── leg 1 chain  │                                          ▼
  │ socket 3 ── leg 2 chain  │                                    PICO 2 WH (micro-USB: 5 V + data)
  │ socket 4 ── leg 3 chain  │
  │ socket 5 ── leg 4 chain  │
  │ socket 6 ── spare        │
  └──────────────────────────┘
```

| Wire | From | To | Gauge | Notes |
|---|---|---|---|---|
| Battery + | Battery XT60 | F1 | 16 AWG | F1 within 10 cm of the battery. Start with the 10 A fuse (the pack is rated 10 A continuous); fit 15 A only if 10 A blows in normal walking |
| Switched + | F1 | Switch input | 16 AWG | |
| 12 V rail | Switch output | Hub 12 V Wago, F2 | 16 AWG | Split with a Wago 221 |
| Ground rail | Battery − | Hub ground Wago, Witty Pi input − | 16 AWG | Split with a Wago 221 |
| Pi branch | F2 (5 A) | Witty Pi input + | 20-22 AWG | F2 protects this thinner wire |
| Pi power | Witty Pi | Pi 5 header | none (HAT) | The Witty Pi's own button starts and cleanly stops the Pi |
| Pico power | Pi 5 USB-A | Pico micro-USB | USB cable | Carries 5 V and the Pi-Pico data link |

Rules:

- The Pico never powers a servo. Servo power is the 12 V rail only.
- One ground everywhere: battery −, hub, Witty Pi, Pi, Pico and servo board all meet.
- Two power controls: the rocker switch is the master for the whole robot; the Witty Pi's button
  controls only the Pi. To turn off: Witty Pi button, wait for the Pi to stop, then the master.
- Never plug a USB-C supply into the Pi while the Witty Pi is powering it.
- The battery charges only through its 4-pin balance plug, off the robot's circuit.

## 2. Servo bus

```
 PICO GP0 (TX) ──┐
 PICO GP1 (RX) ──┼──► SERVO BOARD (Waveshare Bus Servo Adapter (A)) ──► hub socket 1
 PICO GND ───────┘        serial pins            servo socket: GND / 12 V / DATA

 hub socket 2 ──► servo 1 (hip swing) ──► servo 2 (hip lift) ──► servo 3 (knee)      leg 1
 hub socket 3 ──► servo 4 ──► servo 5 ──► servo 6                                    leg 2
 hub socket 4 ──► servo 7 ──► servo 8 ──► servo 9                                    leg 3
 hub socket 5 ──► servo 10 ──► servo 11 ──► servo 12                                 leg 4
```

- The hub is three Wago 221 five-way connectors, one each for 12 V, ground and data. Each takes
  one wire in and four out. "Hub socket" above means one set of three leads; each leg lead is a
  servo cable cut in half and joined through a JST-XH connector so the leg unplugs.
- Every cable is 3 wires: ground, 12 V, data. All twelve servos are in parallel on those three.
- Bus speed 1 Mbps. IDs 1-12 are set one servo at a time from the Mac before the chain is built.
- The servo board's USB socket goes to the Mac for bench tests. Never have its USB and the Pico
  driving the bus at the same time.

## 3. Pico 2 WH (leg controller)

| Pico pin | GPIO | Goes to | Notes |
|---|---|---|---|
| 1 | GP0, UART0 TX | Servo board serial | Which label (RX or TX) it meets is checked on the board's wiki and with the meter |
| 2 | GP1, UART0 RX | Servo board serial | Measure the board's signal voltage first: must not exceed 3.3 V into this pin |
| 3 | GND | Servo board GND | |
| 4 | GP2, I2C1 SDA | OLED SDA | Display on its own bus |
| 5 | GP3, I2C1 SCL | OLED SCL | |
| 6 | GP4, I2C0 SDA | BNO086 SDA | Motion sensor on its own bus |
| 7 | GP5, I2C0 SCL | BNO086 SCL | |
| 9 | GP6 | BNO086 INT | "New data ready" |
| 10 | GP7 | BNO086 RST | Lets firmware reset the sensor |
| 21 | GP16 | Sonar Trig | RCWL-1601 at 3.3 V |
| 22 | GP17 | Sonar Echo | Pull-down in firmware, as on wheely |
| 36 | 3V3 OUT | OLED VCC, BNO086 VIN, sonar VCC | |
| 38 | GND | OLED, BNO086, sonar GND | |
| micro-USB | | Pi 5 USB-A | 5 V in, serial link to the Pi |
| SWD socket | | Raspberry Pi Debug Probe | Flashing and logs |

## 4. Raspberry Pi 5 (brain)

| Pi 5 port | Goes to | Notes |
|---|---|---|
| 40-pin header | Witty Pi 5 HAT+ | Power in (5 V) and its control signals |
| USB-C | nothing | Left empty |
| CAM/DISP 0 | Camera Module 3 | 22-pin to 15-pin cable |
| CAM/DISP 1 | free | Reserved for the VL53L9CX lidar upgrade |
| USB-A | Pico 2 WH | Power and serial link |
| USB-A | ReSpeaker XVF3800 | Microphones and speaker amplifier |
| Fan header | Active Cooler | |
| Header pin 1 (3V3) | VL53L5CX VIN | These four pins are reached through the Witty Pi's pass-through header |
| Header pin 3 (GPIO2, SDA) | VL53L5CX SDA | |
| Header pin 5 (GPIO3, SCL) | VL53L5CX SCL | |
| Header pin 6 (GND) | VL53L5CX GND | |

The speaker plugs into the XVF3800's speaker connector, not into the Pi.

## 5. Not verified: check before power

1. Servo board serial pins: signal voltage (must suit the Pico's 3.3 V pins) and which label
   connects to the Pico's TX and RX.
2. Servo board power: whether it runs correctly when fed only through its servo socket from the
   hub, or needs its own power terminal wired to the 12 V rail.
3. Servo cable wire order (ground, 12 V, data) by continuity with power off, before any lead is
   cut and joined into the hub.
4. Rocker switch: voltage drop at 5 A on the bench supply; replace it if it runs warm. Servo
   cable wire gauge in the Wago clamps: tug test on the first lead.
5. Witty Pi: that its header passes pins 1, 3, 5 and 6 through for the distance sensor; which
   I2C address it uses (must differ from the VL53L5CX); its input connector type and voltage
   range against a full 12.6 V pack; whether it can start the Pi automatically when the master
   switch is pressed.
6. Whether the Pi 5 powers its USB-A ports immediately at power-on (decides how fast the eyes
   appear). If not, the Pico gets 5 V from the Pi's header pin 2 into VSYS through a Schottky diode.
7. I2C addresses: BNO086 expected 0x4A, OLED expected 0x3C (from memory).
8. Fuse sizes: F1 10 A and F2 5 A are starting values; the first leg on the bench supply gives
   real currents.

Parts are listed in `docs/parts.md`.
