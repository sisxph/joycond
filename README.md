joycond is a linux daemon which uses the evdev devices provided by hid-joycon to implement joycon pairing.

# Installation
1. clone the repo
2. `cmake .`
3. `sudo make install`
4. `sudo systemctl enable --now joycond`

# Usage
When a joy-con or pro controller is connected via bluetooth or USB, the player LEDs should start blinking periodically. This signals that the controller is in pairing mode.

For the pro controller, pressing both triggers will "pair" it.

With the joy-cons, to use a single contoller alone, hold ZL and L at the same time (ZR and R for the right joy-con). Alternatively, hold both S triggers at once.

To combine two joy-cons into a virtual input device, press a *single* trigger on both of them at the same time. A new uinput device will be created called "Nintendo Switch Combined Joy-Cons".

Rumble support is now functional for the combined joy-con uinput device.
