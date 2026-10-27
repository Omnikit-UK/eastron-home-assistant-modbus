# Eastron energy meters in Home Assistant: copy-and-paste Modbus settings

Ready-made Home Assistant settings for three Eastron DIN-rail energy meters:

| Meter | What it measures | Settings file |
|---|---|---|
| **SDM230 Modbus** | One single-phase circuit, up to 100A | [`sdm230.yaml`](sdm230.yaml) |
| **SDM72D-M** | A three-phase supply wired straight through it, up to 100A | [`sdm72d-m.yaml`](sdm72d-m.yaml) |
| **SDM630MCT** | A three-phase supply of any size, read with clip-on CT clamps | [`sdm630mct.yaml`](sdm630mct.yaml) |

All three connect to Home Assistant the same way: two data wires (A and B) from the meter to a USB to RS485 adapter plugged into the Home Assistant machine. The register addresses in each file are taken from Eastron's own Modbus protocol documents.

Not sure which meter you need? Read [Which Eastron meter do I need?](https://omnikit.co.uk/blogs/news/which-eastron-meter-do-i-need)

## How to use it

1. **Fit the meter.** It connects to mains wiring, so it must be installed by a competent person to the current wiring regulations.
2. **Connect the data wires.** Run a twisted pair, such as a spare pair in a Cat5 or Cat6 cable, from the meter's A and B terminals to A and B on the adapter. On a long run, fit a 120-ohm resistor across A and B at the far end.
3. **Find the adapter's path.** In Home Assistant, go to **Settings > System > Hardware**, open the three-dot menu and choose **All hardware**. Copy the entry that starts `/dev/serial/by-id/`. It stays the same if you plug in another USB device later; `/dev/ttyUSB0` may not.
4. **Paste the settings.** Open `configuration.yaml` (the File editor add-on is the easiest way), paste in the file for your meter and replace the `port:` line with your path.
5. **Restart Home Assistant** from **Settings > System**, then search for the meter's name under **Settings > Devices & services > Entities**.
6. **Add it to the Energy dashboard.** Use the Import Energy sensor for grid consumption, and Export Energy for energy returned to the grid if you have solar.

The full step-by-step guide, written for the SDM230, is here: [How to connect an Eastron SDM230 to Home Assistant](https://omnikit.co.uk/blogs/news/connect-eastron-sdm230-to-home-assistant).

## Factory settings

These are the communication settings each meter leaves Eastron's factory with, taken from the manufacturer's manuals. If a meter has been set up before, check its own menu, because they may have been changed.

| Meter | Address | Baud rate | Parity | Stop bits | Data bits |
|---|---|---|---|---|---|
| SDM230 | 1 | 9600 | None | 1 or 2 (try 1 first) | 8 |
| SDM72D-M | 1 | 9600 | None | 1 | 8 |
| SDM630MCT | 1 | Not printed; check the menu | None | 1 or 2 | 8 |

## Notes for each meter

**SDM230.** The file reads voltage, current, power, power factor, frequency, and import and export energy.

**SDM72D-M.** Eastron's protocol document for the SDM72D-M lists totals only: total power, import and export energy, and import and export power. Those are what the file reads.

**SDM630MCT.** The file is set up for a three-phase four-wire supply and reads each phase's voltage, current and power, plus the totals. On a three-wire supply, the per-phase voltages and powers aren't available, so delete those sensors and keep the currents and totals. The SDM630MCT takes CT clamps with a **5A (or 1A) output**. Clamps with a 333mV output need the SDM630MCT-MV instead.

## More than one meter

Several meters can share one adapter. Give each meter its own Modbus address from its front menu, wire them all to the same A and B pair, keep one `modbus:` entry for the adapter, and put all the sensors under it with each meter's own `device_address`.

If you already have a `modbus:` section in `configuration.yaml`, add the new entry under it rather than a second `modbus:` line.

## If it doesn't work

1. **A and B the wrong way round.** Swap them. It's the most common fix.
2. **Settings don't match.** Check the address, baud rate and parity in the meter's menu. If they all match and there's still no reply, try `stopbits: 2`.
3. **Wrong adapter path.** Check it again under All hardware, and make sure nothing else in Home Assistant is using the same adapter.
4. **Long cable with no termination.** Fit a 120-ohm resistor across A and B at the far end.
5. **Data and mains cables run together.** Separate them.
6. **Older Home Assistant.** If it won't accept `device_address`, change each one to `slave`. It works the same way.

Home Assistant's log, under **Settings > System > Logs**, shows Modbus errors such as "no response received", which means the problem is wiring or settings rather than the file.

Home Assistant's [Modbus documentation](https://www.home-assistant.io/integrations/modbus/) lists every option.

## Using ESPHome instead

To put a meter on an ESP32 board with an RS485 module instead, ESPHome has a ready-made Eastron SDM component. See the [ESPHome Eastron SDM documentation](https://esphome.io/components/sensor/sdm_meter/). Check the baud rate and address against the meter's own menu.

## About

Written by [OmniKit](https://omnikit.co.uk), a UK supplier of Eastron meters, RS485 adapters and Raspberry Pi kits for Home Assistant. If you'd rather buy the parts together, the [Home Assistant Energy Monitoring Kit](https://omnikit.co.uk/products/home-assistant-energy-monitoring-kit-raspberry-pi-4-sdm230) has a Raspberry Pi 4, an SDM230, an adapter, a power supply and a memory card with Home Assistant installed. More background: [Home Assistant Energy Monitoring Kit: what you need](https://omnikit.co.uk/blogs/news/home-assistant-energy-monitoring-kit-uk).

Spotted a mistake or got a meter working with different settings? Open an issue and tell us.

Free to use and share under the MIT licence.
