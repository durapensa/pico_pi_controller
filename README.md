# pico_pi_controller
> A service interface for one or more Raspberry Pi\* SBCs to be monitored & controlled by a Raspberry Pi Pico<sup>†</sup>, *using i2c*.


## Requirements


*Work in progress.* *See Hardware & Software Instructions below.*

1. The RPi\* must have GPIO pins 18 & 19 free for use by `pigpio` as SDA & SCL of secondary i2c controller.
2. RPi4 must have manual installaltion of `pigpio` for compatible i2c use.
3. Pico<sup>†</sup> must be running CircuitPython-6.x.
4. Pico<sup>†</sup> must have `adafruit_bus_device` & `pico_pi_controller` CircuitPython libraries installed.

\* RPi 4/3/Zero, RPi4: GPIO pins 10 & 11; `pigpio` enables use of the Broadcom SoC secondary i2c controller as a 'peripheral' device.

<sup>†</sup> or any CircuitPython-capable MCU.

## Install

`pip install pico_pi_controller`

## Hardware & Software Instructions

### Quick Start

*If using the simple setup of one RPi controlled by a Pico. Additinal RPi may be added by repeating steps 1 & 4.*
1. Connect RPi secondary i2c pins to Pico primary i2c pins **SDA↔SDA  SCL↔SCL  GND↔GND**:
   1. On RPi\*, connect [GPIO pins](https://pinout.xyz/) 18 (SDA), 19 (SCL) and GND (any GND pin), e.g. using [Female/Female Jumper Wires](https://www.adafruit.com/product/1951); color conventions may be found [here](https://learn.adafruit.com/introducing-adafruit-stemma-qt/technical-specs).
   2. On Pico†, connect wires from RPi (above) to the primary i2c bus, [board pins](https://learn.adafruit.com/getting-started-with-raspberry-pi-pico-circuitpython/pinouts) 4 (SDA), 5 (SCL), and GND (any GND pin).
   
2. Plug Pico into RPi USB port, e.g. using [MicroB to USB A cable](https://www.adafruit.com/product/898).

3. From RPi, install CircuitPython & CircuitPython Library Bundle onto the Pico:
    1. On RPi, download [CircuitPython](https://circuitpython.org/downloads) & [install it](https://learn.adafruit.com/welcome-to-circuitpython/installing-circuitpython) on your Pico.
    2. On RPi, download & unzip [CircuitPython Library Bundle](https://circuitpython.org/libraries), then copy from the `lib` folder `adafruit_bus_device` to the `lib` folder on your Pico (CIRCUITPY drive).
    
4. On RPi, install `pico_pi_controller` components.
    1. On RPi, install `pico_pi_controller` (e.g. `pip3 install pico_pi_controller`)
    2. On RPi, locate‡ & copy `ppc-daemon.py` to the location of your choice and configure your system(s) to run the script on startup (it's a daemon), then reboot or manually run the daemon.
5. From RPi, install `pico_pi_controller` for CircuitPython onto the Pico.
    2. On RPi, download & unzip `pico_pi_controller` for CircuitPython, then copy from the `lib` folder `pico_pi_controller` to the `lib` folder on your Pico (CIRCUITPY drive).
6. Unplug the Pico from the RPi USB port and connect it to a reguar USB power source.

\* RPi4 uses GPIO pins 10 (SDA), 11 (SCL).

† Other MCU boards will have different pinouts. If using an MCU board with a Qwiic or STEMMA QT connector, e.g. [Adafruit Feather RP2040](https://www.adafruit.com/product/4884), [this cable](https://www.adafruit.com/product/4397) is useful.

‡ locate the installed files, e.g. in `~/.local/lib/python3.7/site-packages/pico_pi_controller/examples`.


## How to use

Module examples

## Next

cleanup logging & add more status types.

add Pico initiated RPi shutdown, later poweroff (using PEN)

* if constant scanning of i2c is unworkable (mem frag from object create/delete), consider using button or message from RPi (specified RPi i2c address as 'console'?)

logging with adafruit_logging.mpy

expand LED status (NeoPixels)

settings with config.py (just import the damed thing!) - less need with new classes

add convention for RPi to report to Pico (over i2c) additional connections (gpio-poweroff, etc.), this to keep the Pico hands-off and config-free.

notes: [.mpy](https://docs.micropython.org/en/latest/reference/mpyfiles.html)

### Pseudo-code

<code>
import adafruit_logging as logging
import config
...

DEBUG = True
LOGGING_LEVEL = logging.DEBUG
BASE_SPEED = 0.5
LOG_FILE = '/log.txt'
logger.setLevel(config.LOGGING_LEVEL)
</code>

https://circuitpython.readthedocs.io/en/latest/shared-bindings/rtc/index.html
<code>
    t = time.struct_time((2019,   7,    10,   17,  00,   0,    0,   -1,    -1))
    # you must set year, mon, date, hour, min, sec and weekday
    # yearday is not supported
    # isdst can be set but we don't do anything with it at this time
    print("Setting time to:", t)     # uncomment for debugging
    rtc.datetime = t
    print("Done!")
</code>

## Future

### Packaging

RPi: project name: pico_pi_controller (daemon exists in examples/)

https://nbdev.fast.ai/ (does most of the stuff in the-hitchhikers-guide-to-packaging) 

https://the-hitchhikers-guide-to-packaging.readthedocs.io/en/latest/creation.html

https://packaging.python.org/guides/using-manifest-in/

Pico: project name: pico_pi_controller (use same name? simple program exists in code.py)

[Creating a library](https://learn.adafruit.com/creating-and-sharing-a-circuitpython-library/creating-a-library) with [CookieCutter](https://cookiecutter.readthedocs.io/en/latest/)

`pip3 install cookiecutter`
`cookiecutter gh:adafruit/cookiecutter-adafruit-circuitpython`

library_description: CircuitPython library for Pi SBCs to be monitored & controlled by a microcontroller board, e.g. a Pi Pico, using i2c.

library_keywords: i2c pico pi sensors system shutdown load wifi PowerENable

https://nbdev.fast.ai/ ?


### Pico LEDs (using NeoPixel Stick, strip, string, etc.)

status mode: (get from Keep), booting, power, heartbeat (require 3 misses?), wifi, etc.

button activates selection mode, lights activate from 0 for each connected pi, dim green with a short off blink, all at the same time. bright solid green indicates the selected device (pi blinks something too?), power button will poweroff selected pi. power'd off pi blinks red, power button powers on pi.

### Pico display (SPI or I2C)
TBD: display status for one or more pi. auto-scaled to display size & number of pi. hostname?

future future: use for selection, fwd, back.


### Pico registry of connected RPis
2 modes, non-stored & stored.

Non-stored: Pico will register any connected Pi on first connect and remember until reset.

Stored: Pico will will register any connected Pi on first connect and remember thru resets.

See [CircuitPython Storage](https://learn.adafruit.com/circuitpython-essentials/circuitpython-storage)

### Capabilities with other hardware add-ons

#### Safe shutdown & startup with a single button

**Can be used with single button for all connected RPis**
Configure from RPi? i2c reports the GPIOs connected on the Pico side.


Pico 2 GPIO:

On→Off: RPi GPIO 3 (pin 5) to GND, using any Pico GPIO, only when RPi in PPC registry is offline (assumed off), prime registry?

Off→On: RPi GPIO 26 to GND, using any Pico GPIO, only when RPi in PPC registry is online
use gpio-shutdown,gpio_pin=26,active_low=1,gpio_pull=up

Pico 1 GPIO:

On: RPi GPIO 3 to GND, using any Pico GPIO, only when RPi in PPC registry is offline (assumed off), prime registry?

Off: use i2c command to trigger systemd shutdown

#### Full poweroff & poweron with a single button

On→Off:
1. above (safe shtudown for raspian), then 
2. `dtoverlay=gpio-poweroff,active_low,gpiopin=14` to detect completed shutdown
3. PEN pull low

Off→On
1. detect state (PPC registry will have state of RPi's PEN)
2. PEN open (power detectable on 3.3V, RPi should boot, reappear on i2c bus)

If PEN is pulled low, then the PMIC shuts down the on-board power supplies in a controlled manner. The current consumption is limited to about 3mA (the PWR LED is still illuminated, and the pull-up on PEN consumes current). It is intended to be used in conjunction with an external board (HAT) that provides a UPS-like or battery-operated function.

Works like the [exampe](https://www.instructables.com/HOW-TO-TURN-OFF-RASPBERRY-PI-PROPERLY/) using a [Pololu Mini Pushbutton Power Switch with Reverse Voltage Protection, LV](https://www.pololu.com/product/2808) bus uses the PEN RPi board pin.

#### Full poweroff & poweron with selection buttons
**Requires LEDs or display above**

`pigpio` use: great if this encourages the RPi Foundation to implement an I2C peripheral kernel driver & coresponding dtoveray for the Broadcom SoC's secondary i2c controller!
