# 3DP-RSR-Pi
The 3D Printer Remote Shutdown Relay with Raspberry Pi - a concept for a relay that turns a 3D printer on or off via a Raspberry Pi

Obviously, I'm not the first person to make one of these, but sourcing stuff and doing research was sorta difficult and I wanted to make it easy for people to find information.

# BOM
1x Solid State 40A DC to AC relay - [https://www.amazon.com/dp/B08GPB7N2T](https://www.amazon.com/gp/product/B08GPB7N2T/)  
1x DC Relay - [https://www.amazon.com/AEDIKO-Channel-Optocoupler-Isolation-Support/dp/B095YD3732/](https://www.amazon.com/AEDIKO-Channel-Optocoupler-Isolation-Support/dp/B095YD3732/) (pack of 4 but cheaper than any others)  
1x (Optional, for a physical switch) Push Button - any kind
2x brain cells  

That should be all you need! The last part2 can be very hard to source, depending on your location. I was able to find a few of them in my box of loose screws.

# Wiring

## Warning
Never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never never EVER TOUCH MAINS POWER!!!!!!!!! ALWAYS attach anything that has to do with AC voltage to a terminal, and once everything is done and protected THEN you can plug it into an outlet. I don't want to be the death of anybody!

![wiring](https://github.com/invictus-anic3tus/3DP-RSR-Pi/blob/main/images/relaywiring.png)

Raspberry Pi GND -> DC Relay D- -> AC Relay Control-

Raspberry Pi +5V -> DC Relay D+ -> DC Relay COM

Raspberry Pi GPIO Pin (Output pin) -> DC Relay In

DC Relay NC -> AC Relay Control+

AC Relay 1 -> Positive AC terminal (UNPLUG THE TERMINAL FIRST!)

AC Relay 2 -> PSU positive input

(Optional) Raspberry Pi GPIO Pin (Button pin) -> Pushbutton In, Pushbutton out -> Raspberry Pi GND

here's how this works: first, whenever you turn on the GPIO, the DC relay gets a 3.3V signal and passes the Pi's 5V line across to the AC relay. This is necessary because the GPIO pins themselves don't have enough power to run a 40A relay, and the 5V from the pi comes straight from the power source and triggers the AC relay. For this reason, you're going to need a relatively powerful power source for the pi, to provide the extra power for the relay. 3A is probably fine as long as you don't have very many other things connected to it, like touchscreens and cameras. My setup uses a 5" touchscreen, a pi cam, and a usb cam, so I'm going to go 3.6A. The cool thing about this is setup that whenever you shut down the pi, all of the GPIOs are set to LOW. This means that to turn off the printer and the pi in one go, just run your pi shutdown command. Easy!

# Code

Make this script in /home/pi

relaycontrol.py
```
import sys
import RPi.GPIO as GPIO

relay_pin = (insert your pin number)

if __name__ == "__main__": # this here makes sure that if you ever want to call the script from somewhere else, you can import it without calling it immediately
    GPIO.setmode(GPIO.BCM)
    GPIO.setup(relay_pin, GPIO.OUT)
    GPIO.setwarnings(False)
    if len(sys.argv) != 2:
        print("Usage: gpio_control.py [on|off]")
        sys.exit(1)

    if sys.argv[1] == "on":
        GPIO.output(relay_pin, GPIO.HIGH)
    elif sys.argv[1] == "off":
        GPIO.output(relay_pin, GPIO.LOW)
    else:
        print("Please use 'on' or 'off'")
        sys.exit(1)

```
Now run `chmod +x /home/pi/relaycontrol.py` to make it executable.

To run, use `python relaycontrol.py on` or `python relaycontrol.py off`. To turn on the printer, you can set the "on" script to run on startup, so when you power the pi, the printer turns on. To turn it off without turning the Pi off, add these lines to moonraker.conf:
```
[action_command printer_off]
command: python relaycontrol.py off
```
Which should add a button to run the command via the interface. Haven't tested yet, will do soon!

## That should be it!!!!

Feel free to open an issue or PR if you have any questions or things to add!
