# Anycubic Kobra 2 — Klipper / HC32F460

A working Klipper 0.11.0 port for the Anycubic Kobra 2 using the stock HC32F460 mainboard and a Creality Sonic Pad.

## Status

This configuration has been physically tested on an Anycubic Kobra 2.

Known-good setup:

- Printer: Anycubic Kobra 2
- MCU: Huada Semiconductor HC32F460
- Host: Creality Sonic Pad
- Klipper: 0.11.0
- MCU serial: PA15 / PA09 (USART1)
- Serial baud: 250000
- Firmware application address: `0x10000`
- Flash size: `0x40000`
- Tested clock: 168 MHz

The supplied `firmware/klipper.bin` is the known-good firmware used on the printer.

## Repository contents

- `firmware/klipper.bin` — known-good firmware binary
- `config-kobra2/printer.cfg` — working Kobra 2 configuration
- `build/klipper-hc32f460.config` — firmware build configuration
- `build/Dockerfile` — Docker build environment
- `build/docker-packages.txt` — package inventory from the original build environment
- `src/hc32f460/` — Klipper HC32F460 platform support
- `lib/hc32f460/` — Huada HC32F460 SDK sources
- `docs/` — upstream Klipper documentation

## Using the supplied firmware

The supplied binary is named `klipper.bin` in the repository.

For the Kobra 2 SD-card flashing procedure, rename it to `firmware.bin` and use the normal Kobra 2 firmware flashing process.

Do not use the Sonic Pad printer-firmware upgrade tools to replace this custom MCU firmware.

## Printer configuration

Copy `config-kobra2/printer.cfg` into the appropriate Klipper configuration location on the Sonic Pad.

The working MCU configuration is:

    [mcu]
    serial: /dev/serial/by-id/usb_serial_1
    baud: 250000
    restart_method: command

The serial device name may need to be changed if the Sonic Pad assigns a different device path.

## Building the firmware

A Docker environment is provided so the HC32F460 build can be reproduced without manually reconstructing the original development environment.

From the repository root:

    docker build -f build/Dockerfile -t kobra2-klipper-hc32f460 .

Build the firmware:

    docker run --rm kobra2-klipper-hc32f460 bash -lc "cp build/klipper-hc32f460.config .config && make olddefconfig && make -j4"

The resulting firmware is:

    out/klipper.bin

The build environment has been successfully tested from the repository itself.

Note: Klipper embeds build information in the firmware, so a rebuilt binary will not necessarily be byte-for-byte identical to the supplied known-good binary.

## Important hardware warning

This HC32F460 port disables the JTAG/SWD debug pins during startup so that PA15 can be used for the printer's serial interface.

The firmware source contains this operation in `src/hc32f460/main.c`.

If an incorrect firmware configuration prevents the board from booting, recovery may require the proprietary HC32F460 programming method rather than normal SWD debugging.

**Use the supplied known-good firmware first.**

## HC32F460 serial pin variants

The HC32F460 platform code contains several possible serial pin mappings:

- PA7 / PA8
- PA13 / PA14
- PA3 / PA2
- PH2 / PB10
- PA15 / PA09
- PC0 / PC1

The supplied Kobra 2 configuration uses **PA15 / PA09**.

Different Kobra 2 board revisions may use different connections. Verify the board before changing the serial configuration.

## License

Klipper is distributed under the GNU GPL v3.

The included Huada Semiconductor HC32F460 SDK files retain their original licensing and copyright notices, including the applicable BSD 3-Clause license.

See the individual source files and `COPYING` for the applicable license terms.
