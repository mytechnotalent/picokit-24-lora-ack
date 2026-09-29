![picokit-24-lora-ack](https://raw.githubusercontent.com/mytechnotalent/picokit-24-lora-ack/main/picokit-24-lora-ack.png)

<br>

## FREE Reverse Engineering Self-Study Course [HERE](https://github.com/mytechnotalent/reverse-engineering)
## FREE Embedded Hacking Course [HERE](https://github.com/mytechnotalent/Embedded-Hacking)

<br>

# PICOKIT-24 LORA ACK

### ACK Timeout and Retry and Authenticated Heartbeat
#### Lesson 24 of the Picokit Series

<br>

***
**LEGAL DISCLAIMER:**
The information, tools, and code provided in this repository and course are strictly for educational, research, and defensive purposes only.

You are explicitly prohibited from using any materials contained herein to access, test, modify, or exploit any device, network, or system that you do not own 100% or for which you do not have explicit, documented, and legally binding authorization to interact with.

By using this repository and course, you acknowledge and agree that:

1. Any illegal, unauthorized, or malicious use of this information is solely your responsibility.
2. The author(s) and contributor(s) of this repository and course shall not be held liable for any damages, legal repercussions, criminal charges, or unauthorized actions resulting from the use, misuse, or abuse of the contents herein.
3. You will comply with all applicable local, state, national, and international laws regarding cybersecurity and computer fraud.

**IF YOU DO NOT AGREE WITH THESE TERMS, DO NOT USE THIS REPOSITORY AND COURSE.**
***

<br>
<br>

## Overview

The twenty fourth Picokit lesson. The node applies a downlink command and
then requires an acknowledgement from the gateway. When the acknowledgement
does not arrive inside the timeout window the node resends the heartbeat up
to a fixed retry limit and reports the retry count in its authenticated
heartbeat.

<br>

## What it teaches

- Requiring an acknowledgement for every applied command.
- Applying a timeout window to the acknowledgement wait.
- Retrying the heartbeat up to a fixed limit and counting each retry.
- Reporting the retry count in the authenticated heartbeat.

<br>

## Hardware

| Peripheral | Pico 2 pin | Role |
| --- | --- | --- |
| Onboard LED | GP25 | heartbeat, one blink per transmit |
| Red LED | GP16 | command 0 |
| Yellow LED | GP18 | command 1 |
| Green LED | GP17 | command 2 |
| RYLR998 | GP8 TX / GP9 RX | command downlink and heartbeat uplink |
| Debug Probe | SWCLK/SWDIO/GND, GP0/GP1 | SWD and the console |

<br>

## How it works

The node runs `monitor_step` in a loop. When the gateway sends `CMD <n>` the
node lights the matching status LED, arms a one second acknowledgement
timeout, and resends the heartbeat on every timeout until an `ACK` arrives
or three retries have been spent. Every 5 seconds it seals
`{"n":24,"s":<seq>,"r":<retries>}` with the field key and sends it over
LoRa. The gateway authenticates each frame and only then parses it.

<br>

## Build and flash

```bash
cd firmware
cmake -S . -B build -G Ninja -DPICO_BOARD=pico2 -DPICO_PLATFORM=rp2350-arm-s
cmake --build build
openocd -f interface/cmsis-dap.cfg -f target/rp2350.cfg \
  -c "program build/picokit_24_lora_ack.elf verify reset exit"
```

<br>

## Watch the node

Open the console at 115200 and reset:

```text
BOOT
=== PICOKIT-24 LORA ACK // ACK TIMEOUT + RETRY + AUTHENTICATED HEARTBEAT ===
RX from 0x0001, 5 bytes
CMD 2
RETRY 1
ACK
```

<br>

## The gateway

```bash
cd gateway
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python3 listen.py --port /dev/cu.usbserial-A50285BI --hub 0001 --network 18 --db gateway.db
```

Send a command with `python3 cli.py send --port /dev/cu.usbserial-A50285BI
--node 24 --command 2`, then acknowledge with `python3 cli.py ack --port
/dev/cu.usbserial-A50285BI --node 24 --seq 0`. The terminal dashboard
`python3 tui.py --db gateway.db` and the web dashboard
`python3 web/app.py --db gateway.db` show the same rows.

<br>

## Verify

```bash
python3 .opencode/skill/embedded-c-standard/audit_c_standard.py
python3 .opencode/skill/embedded-python-standard/audit_python_standard.py
python3 .opencode/skill/iot-readme-standard/validate_readme.py
python3 .opencode/skill/iot-banner-standard/validate_banner.py
python3 scripts/run_tests.py
python3 scripts/check_coverage.py
```

<br>

# Next
[picokit-25-lora-addressing](https://github.com/mytechnotalent/picokit-25-lora-addressing)

<br>

# License
[MIT License](https://github.com/mytechnotalent/picokit-24-lora-ack/blob/main/LICENSE)
