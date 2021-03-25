# pico_pi_controller
> A service interface for one or more Raspberry Pi\* SBCs to be monitored & controlled by a Raspberry Pi Pico<sup>†</sup>, *using i2c*.


## Requirements


*Work in progress.* *See Hardware & [Software Instructions below](#Hardware-&-Software-Instructions).*

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

On each RPi, with `pigpiod` running, run this example `ppc-daemon.py` script:

```python
import pico_pi_controller

mydevice = pico_pi_controller.PPPeripheral(0x13)
```

Where `0x13` is the chosen *unique* I2C address of your RPi on the I2C bus where an MCU controller is running (e.g. a Pico running `CircuitPython_pico_pi_controller`.

A more sophisticated example:

```python
import pico_pi_controller
import time
import threading
import logging
    
address=0x13
    
def i2c_peripheral(address):
    global mydevice
    mydevice = pico_pi_controller.PPPeripheral(*args, **kwargs)
    logging.info("PPC: starting I2C peripheral ",hex(address))
    
t = threading.Thread(target=i2c_peripheral(address, bosmang=True, UART_TX=8, UART_RX=9, PWR=10, PEN=11)
threads.append(t)
t.start()

while True:
    logging.info("PPC: last controller contact",mydevice.last_contact(),"seconds ago.")
    sleep(1)
```

When your device declares itself to be *bosmang*, it sends its own datetime for setting the controller's RTC. Optionally, a *bosmang* can send commands to the controller, e.g. to reboot or shutdown any connected RPi. Additionally, *bosmang* may have its console UART, gpio-poweroff, and PEN pins connected to GPIO pins on the controller which enables the controller to execute a safe shutdown & poweroff plus full poweronn of *bosmang*.
