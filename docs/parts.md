# walky: parts list

Four-legged spider walker, three joints per leg (hip swing, hip lift, knee): 12 bus servos, a
Pico 2 W as the real-time leg controller, a Raspberry Pi 5 as the brain. Wiring is in
`docs/wiring.md`.

Prices are list prices seen in November 2026 and will drift. Weights and load figures are estimates
until the parts are on a scale.

## Legs and leg controller

| Qty | Part | Price | Notes |
|---|---|---|---|
| 12 + spares | Feetech STS3215 serial bus servo, 12 V, 30 kg.cm (maker's code ST-3215-C018). Waveshare sells the same servo as ST3215 | about £14.50 each in six-packs on AliExpress; £21-25 each from UK shops | 45 RPM, 6-12.6 V, 4096-step magnetic encoder, reports position, load, temperature and voltage. Comes with two aluminium horns, screws and one bus cable. A 7.4 V version is sold under almost the same name |
| 1 | Waveshare ST/SC Serial Bus Servo Driver Board (Bus Servo Adapter (A)) | about £5-11 | Translates USB or UART to the servos' one-wire bus. 9-12.6 V input. A jumper selects USB or UART |
| 1 | Raspberry Pi Pico 2 W, with headers (Pico 2 WH) | £7.70 | The WH has the debug socket for the Raspberry Pi Debug Probe |
| 1 | Adafruit PiCowbell Proto for Pico (ADA5200) | £1.90 | Permanent board under the Pico: STEMMA QT socket, reset button, solder pads. Needs two 20-pin female header strips |
| 1 | SparkFun VR IMU Breakout, BNO086 (SEN-22857) | £37.40 | Motion sensor with built-in orientation fusion. The Adafruit BNO085 (ADA4754, £27) is the same family |
| 1 | STEMMA QT / Qwiic cable, 100 mm | £1.00 | Motion sensor to the PiCowbell |

## Brain

| Qty | Part | Price | Notes |
|---|---|---|---|
| 1 | Raspberry Pi 5, 4 GB | £105.60 | 2 GB (£74.40) would run camera, voice and a learned gait; 4 GB leaves room for mapping |
| 1 | Active Cooler for Raspberry Pi 5 | £4.80 | |
| 1 | microSD card, 64 GB, A2 class (SanDisk Extreme) | about £18 | |
| 1 | Raspberry Pi Camera Module 3, standard lens | £24.00 | The Wide version (£33.60) is a drop-in swap for a broader view of the floor |
| 1 | Camera Adapter Cable for Raspberry Pi 5, 200 mm | £0.90 | 22-pin to 15-pin |
| 1 | Witty Pi 5 HAT+ (UUGear) | £43.50 | Takes battery voltage, feeds the Pi 5 V through the header, button with clean shutdown, reports battery voltage |

## Voice, sensing, face

| Qty | Part | Price | Notes |
|---|---|---|---|
| 1 | Seeed reSpeaker XVF3800 USB 4-microphone array | £50-68 | Echo cancellation and beamforming in hardware, speaker amplifier on board. USB audio, no driver |
| 1 | Small speaker, 4 ohm, 3 W, JST-PH 2.0 plug | about £3-6 | Plugs into the voice board. Plug type to be confirmed against the board |
| 1 | SparkFun Qwiic Mini ToF Imager, VL53L5CX (SEN-19013) | £25.50 | 8x8-zone distance sensor for obstacles |
| 1 | STEMMA QT / Qwiic cable to female jumper wires, 150 mm | £1.00 | Distance sensor to the Pi's header pins. Qwiic is JST-SH (1 mm pitch), not XH |
| 1 | RCWL-1601 ultrasonic sensor | £3.90 | 3.3 V native, HC-SR04 footprint |
| 1 | Adafruit Monochrome 1.3" 128x64 OLED, STEMMA QT (ADA938) | £19.20 | Eyes and emotions, driven by the Pico |

## Power

| Qty | Part | Price | Notes |
|---|---|---|---|
| 1 | 3S LiPo pack, 11.1 V, 3300 mAh, XT60 plug (HRB, soft case, 135 x 42 x 23 mm, 260 g) | about £25 | No protection board: the fuse and a firmware low-voltage rule are mandatory. The printed body must enclose it |
| 1 | Balance charger for 2S-3S packs, mains powered (HTRC 20 W) | about £18 | Charges through the balance plug at about 1 A |
| 1 | LiPo safe bag | about £6 | For charging and storage |
| 1 | Rocker switch, 12-24 V DC, rated 20 A | about £2 | Master switch in the battery line, after the fuse. Measure its voltage drop before trusting the rating |
| 2 | Inline blade fuse holder, 14 AWG leads, with 5 A, 10 A and 15 A fuses | about £10 for a kit | One at the battery, one on the Pi branch |
| 2+ | XT60 connector pairs with 14 AWG leads | about £8 for five pairs | |
| 1 | Silicone wire, 16 AWG, red and black | about £8 | Battery line. 22 AWG is too thin for it |
| 1 | Wago 221 lever connectors: two 3-way, three 5-way | about £11 for a mixed pack | Rail split, and the servo hub (power, ground and data to four legs) |

## Bench

| Qty | Part | Price | Notes |
|---|---|---|---|
| 1 | Bench power supply, 30 V / 10 A, adjustable current limit (Wanptek) | about £45 | First power-up of every servo and board happens here at a low current limit, never on the battery |

General workshop stock, not listed: 22 AWG and 26 AWG wire, a JST-XH connector kit, JST-PH leads,
M3 hardware, heat-shrink, a breadboard and jumper wires, a micro-USB cable, a Raspberry Pi Debug
Probe, a multimeter, a soldering kit. The Pi 5's mounting holes are M2.5.

## Considered and not used

- **Waveshare ST3215-HS (20 kg.cm, 106 RPM, 68 g):** faster but weaker and heavier. For a walker,
  torque margin matters more than top speed.
- **Hobby PWM servos with a PCA9685 board:** no position, load or temperature feedback, which
  removes the basis for learned gaits and for most fault-finding. A PCA9685 cannot drive bus servos.
- **Waveshare General Driver for Robots, Bus Servo Driver HAT (A):** servo port limited to about
  5 A, no debug header, serial port shared with USB.
- **Pololu Big Pushbutton Power Switch HP (item 2813):** the tidier soft switch for the battery
  line. Replaced by a plain rocker switch for sourcing reasons.
- **6 Port AX/MX Power Hub (Dynamixel):** a ready-made hub with the same Molex 3-pin socket. Its pin
  order (GND, VDD, DATA) must be checked against the servo cable before use. Replaced by Wago
  connectors.
- **Generic 12 V to 5 V converter for the Pi:** cheaper than the Witty Pi, but of unknown quality
  and with no clean shutdown.
- **Separate battery current monitor:** the servos report their own load and input voltage, and the
  bench supply shows current during development.
- **Spinning lidar:** only if room mapping needs 360 degrees later.

## Planned upgrade

- **ST VL53L9CX 3D lidar** (54x42 zones, 5 cm to 9 m, 55 x 42 degrees): sold only on ST's
  STEVAL-VL53L9 evaluation board (about £55), with community drivers for the Pi 5. Would replace the
  VL53L5CX and use the Pi 5's second camera port. Leave room for it on the head.

## Checks when parts arrive

- Servos: confirm the 12 V variant on the bench (about 45 RPM at 12 V).
- Charger: mains plug type.
- Speaker: plug against the voice board's speaker socket.
- Motion sensor: the Rust driver against the BNO086.
- Everything in the "Not verified" list of `docs/wiring.md`.

## Bench order

1. One servo on the bench supply at a 1 A limit, driven from a computer through the servo board's USB.
2. Measure the servo board's signal voltage with the meter before connecting it to the Pico.
3. One servo from the Pico in Rust.
4. One printed leg, three servos, loaded with its share of the robot's weight.
5. Only then the body, the other legs, the Pi and the battery.
