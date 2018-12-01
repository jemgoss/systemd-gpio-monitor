# systemd-gpio-monitor
Systemd-based monitoring of a GPIO pin (for Raspberry Pi).

The basic idea here is to monitor an input pin at the end of the Pi's main GPIO header for a short to the GND pin next to it and initiate an orderly shutdown. This is useful to shut down a headless Raspberry Pi without having to log in through the serial interface or ssh: just touch a paper clip to the pins at the end of the GPIO header. NOT THE 3V3 / 5V END!!

I had originally done this with a small python script but wanted to achieve it with minimal resources. Hence using only systemd and no other shell or script-based polling of the pin's value.

It works by setting up an input pin with internal pullup, assigning a falling edge detection interrupt and monitoring the pin's value through sysfs using a systemd.path unit.

This can easily be adpated to perform other tasks when a GPIO pin change is detected.

Improvements are certainly welcome.

Usage:

1. Copy all systemd unit files to /etc/systemd/system.

2. Enable and start monitoring:<br>
* 40 pin header:
>       # systemctl enable --now gpio_poweroff@21.path

* 26 pin header:
>        # systemctl enable --now gpio_poweroff@7.path

You may want to first test the action without actually shutting down:

1. Edit <code>gpio_poweroff@.service</code> to echo the poweroff command.
2. Use the correct pin number for the activation service:
>       # journalctl -f -u gpio_poweroff@7.service