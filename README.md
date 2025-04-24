# 3DP-RSR-Pi
The 3D Printer Remote Shutdown Relay with Raspberry Pi - a concept for a relay that turns a 3D printer on or off via a Raspberry Pi

# BOM
1x Solid State 40A DC to AC relay - [https://www.amazon.com/dp/B08GPB7N2T](https://www.amazon.com/gp/product/B08GPB7N2T/)  
1x DC Relay - [https://www.amazon.com/AEDIKO-Channel-Optocoupler-Isolation-Support/dp/B095YD3732/](https://www.amazon.com/AEDIKO-Channel-Optocoupler-Isolation-Support/dp/B095YD3732/) (pack of 4 but cheaper than any others)  
1x (Optional, for a physical switch) Push Button - any kind
1x brain cell  

That should be all you need! The last part can be very hard to source, depending on your location. I was able to find a few of them in my box of loose screws.

# Wiring

![wiring](https://github.com/invictus-anic3tus/3DP-RSR-Pi/images/relaywiring.png)

Raspberry Pi GND -> DC Relay D- -> AC Relay Control-

Raspberry Pi +5V -> DC Relay D+ -> DC Relay COM

Raspberry Pi GPIO Pin (Output pin) -> DC Relay In

DC Relay NC -> AC Relay Control+

(Optional) Raspberry Pi GPIO Pin (Button pin) -> Pushbutton In, Pushbutton out -> Raspberry Pi GND

# Code

Make this script in /home/pi

relaycontrol.py
```
import RPi.GPIO as GPIO

relay_pin = (insert your output pin here)
try:
    GPIO.setmode(GPIO.BCM)
    GPIO.setup(relay_pin, GPIO.OUT)
    GPIO.output(relay_pin, GPIO.HIGH)
    # Keep the pin high indefinitely
    while True:
        pass
except KeyboardInterrupt:
    print("\nKeyboard interruption")
    GPIO.cleanup()
except:
    print("Exiting Program...")
    GPIO.cleanup()
finally:
    # Ensure GPIO pins are cleaned up
    GPIO.cleanup()
```
