# Input devices

## PS/2 Keyboards

![Keyboard][img1]

It is not possible to use USB keyboards with BlackIce but PS/2 keyboards work as long as they are OK with 3.3v logic. Microsoft wireless keyboards work (as shown above), as do most HP ones.

[Here][] is an example based on code on code by David Banks (@hoglet67).

[img1]:									./Keyboard.jpg "Keyboard"
[Here]:									https://github.com/lawrie/verilog_examples/tree/master/fpga/ps2

## Gamepads

![Gamepad][img2]

Gamepads can be useful for playing some games. Here is [a BlackSoC example][] of getting input from the buttons on a gamepad connected to a 15-pin PC gameport which is then connected to a Pmod.

[img2]:									./Gamepad.jpg "Gamepad"
[a BlackSoC example]:					https://github.com/lawrie/icotools/tree/master/icosoc/examples/gamepad

## DIP switches

![DIP Switches][img3]

Dip switches can  be useful for configuration. Sometimes the 4 built-in ones are insufficient or can’t be used as the SD card is in use. Here is [an example of using the DIP switches][] or a homemade Pmod to set LEDs on a Digilent 8 LED strip. The example has the dip switches Pmod in PMOD 7 / 8 and the Digilent 8 LED Pmod in PMOD 11/!”.

[img3]:									./DIPSwitches.jpg "DIP Switches"
[an example of using the DIP switches]:	https://github.com/lawrie/verilog_examples/tree/master/ebook/input/switches8

## Keypads

Digilent make a keypad Pmod, but it is easy to make your own. 

![Keypad][img4]

There is an output pin for each column of the keypad, and an input with a pull-up resistor for each row. 3 columns and 4 rows in this case. You need to apply a voltage to the columns in turn and then read the input pins. This needs to be done repeatedly to read the input key in a timely way.

Here is [an example][] that reads the keys and displays the latest key pressed on the leds. The value is represented in 4 bits with 0-9 taking their naturals values and & being 10, and #, 11.


Numeric keypads are useful for some applications where a full keyboard is overkill.

[img4]:									./Keypad.jpg "Keypad"
[an example]:							https://github.com/lawrie/verilog_examples/tree/master/fpga/keypad

## Mice

PS/2 mice can be connected to the BlackIce II using the same Digilent PS/2 Pmos that is used for keyboards.
