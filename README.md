# Arduino-USB-HID-RetroJoystickAdapter
## What is this?
Very cheap and quite easy way to connect many non-USB joysticks and gamepads or DIY arcade cabinet controller to almost any machine with USB-port and HID-support. Arduino board with ATmega32u4 chip with help of Joystick-library does all the USB-work.
Tested with Windows and Linux desktop computers and ARM based computers like Raspberry Pi and Orange Pi.

ATmega32u4 can be easily changed to act as keyboard and/or mouse too. With keyboard device it is easy to make adapter for many mobile devices which don't have gamepad-support out of box. I have tested it with Samsung Galaxy S4 Android phone and Samsung Galaxy Tab 10.1 Android tablet with OTG-USB cable.

### Supported controllers
- Atari-joysticks (supports Sega Master System controller with 2-fire buttons) (ATARI) ²
- Nintendo Entertainment System controllers (NES) ²
- Super Nintendo controllers (SNES) ²
- Sega Genesis / Megadrive 3 and 6-button controllers (GENESIS_3 / GENESIS_6) ²
- Sony Playstation 1/2 controllers (doesn't support Dualshock or analog features) (PSX) ²
- Nintendo 64 controller (RetroJoystickAdapter_N64.ino) (3.3V!)
- Sony Playstation with Analog and Multitap 4 controllers support (RetroJoystickAdapter_Playstation.ino) \*
- Wii Classic Controller (and maybe Pro, not tested) (RetroJoystickAdapter_WiiClassicController.ino) (3.3V!) \*

² = 2 controllers supported

\* = doesn't need Joystick-library

If you use Linux and want to get more than 1 controller with one Arduino, check "Linux and more than one controller with one Arduino"-topic in this same page.

## Short version
- Get ATmega32u4 Arduino board
- Connect joystick or gamepad to Arduino (in most situations soldering is required)
- Install Arduino [IDE](https://www.arduino.cc/en/Main/Software)
- Install Joystick [library](https://github.com/MHeironimus/ArduinoJoystickLibrary)
- Open sourcecode to Arduino IDE and select controller by commenting and uncommenting and edit pin-numbers (if needed)
- Upload
- Done

## Long version
### What you need

#### Arduino board
Starting point is ATmega32u4 based Arduino board. There is two versions of ATmega32u4, IO-logic with 5V or 3.3V. Many controllers (like basic Atari-controller) doesn't include any logic, so 5V and 3.3V versions are fine. Most of controllers with some logic needs 5V (NES, SNES, Genesis) and some rare ones needs 3.3V (N64/Gamecube) (5V is 16MHz and 3.3V 8MHz, so timings are different and 16MHz code doesn't work for N64/Gamecube with 3.3V version). Make sure that Arduino includes USB-port. There are some versions which doesn't include USB-port and then you have to add USB-connector for it. Arduino Leonardo includes ATmega32u4, but cheapest and smallest ones are named Pro Micro. Arduino Pro Micro and ATmega32u4 are good words to use when searching from Ebay etc. Cheapest starts from about $4.

Example of Arduino Pro Micro:

![Arduino Pro Micro](https://github.com/mcgurk/Arduino-USB-HID-RetroJoystickAdapter/raw/master/Images/Arduino_ProMicro.jpg)


#### Computer with Arduino IDE
Arduino IDE works with Windows, OS X and Linux and ARM (beta). If you use small card computer like Rasperry Pi or Orange Pi, you can program adapter even with them (tested with Orange Pi PC and RetrOrangePi 2.0). That way you don't even have to unplug adapter from your emulator/game-platform if you want reprogram adapter.

#### Joystick library for Arduino IDE
https://github.com/MHeironimus/ArduinoJoystickLibrary

#### Joystick(s) or gamepad(s) and way to connect them to Arduino
Some joystick/gamepads uses their own connectors and original connectors can be very pricey. Sometimes you can find extension cord cheaper than plain connector and you can take cable and plug from that.
You can use any IO-pins, but try to avoid pins 0 and 1. They are serial port TX and RX pins and even though they are not used right now, they are handy for debugging and other uses.
Example in [Hardware](https://github.com/mcgurk/Arduino-USB-HID-RetroJoystickAdapter/tree/master/Hardware) folder.

Example of NES-controller wiring:

![NES wiring](https://github.com/mcgurk/Arduino-USB-HID-RetroJoystickAdapter/raw/master/Hardware/nes.png)

### Setup

- Download and install Arduino IDE: https://www.arduino.cc/en/Main/Software

- `Tools->Board->Arduino Leonardo`

  In Arduino IDE you have to select board. In my case some reason "Arduino Leonardo" selections works better than "Arduino/Genuino Micro". With Leonardo-setting RX and TX leds are normally off and blinks when data is transfered to/from USB. With Micro-setting LED's are normally on and they blinks to off when data is transferring.

- `Tools->Port->COMx`

  Arduino IDE shows available COM-ports. In most cases it's biggest numbered COM-port. COM-port number can change when you start to upload program first time (COM-port number can be different after joystick-features are enabled). If this happens, just change COM-port and start upload again. When I tested with RetrOrangePi (Armbian Linux), port was `/dev/ttyACM0`.

- Test your programming environment and upload

  You can now test your setup. One basic example is "blink". With this board, it doesn't blink, because there is no led in pin 13. Nevertheless you can use it as a test that your IDE and Arduino works. Select `File -> Examples -> 01.Basics -> Blink` and press `Upload` (Arrow to right -symbol top of code windows). If you get no errors, you are good to go.

- Install joystick library

  Go to https://github.com/MHeironimus/ArduinoJoystickLibrary and press `Clone or download -> Download ZIP`.
  Extract Joystick2 folder from zip to `%HOMEPATH%\Documents\Arduino\libraries` (Windows) or `~/Arduino/libraries` (Linux).
  
  After extraction check that you have files `%HOMEPATH%\Documents\Arduino\libraries\Joystick2\Joystick2.h and Joystick2.cpp` or `~/Arduino/libraries/Joystick2/Joystick2.h and Joystick2.cpp`.
  Close and start Arduino IDE again so it searches and finds library.

  Playstation analog controller `RetroJoystickAdapter_PlaystationAnalog.ino` needs `Joystick\Joystick.h` and `Joystick\Joystick.cpp`.
  
- Load `RetroJoystickAdapter.ino` to Arduino IDE

  Select controller by commenting and uncommenting and edit pin-numbers (if needed).

- Press Upload

- If you got no errors, test in Windows with `Control Panel -> Game Controllers` or in Linux with `jstest /dev/input/js0`

### Linux and more than one controller with one Arduino
Linux usbhid-module doesn't support out of box multiple controllers with one USB without USB-hub-features (I'm not sure about this, but I didn't manage to get multiple /dev/js-devices without this). 

#### Solution 1
You have to give parameter 
`quirks=0x2341:0x8036:0x40` (first numbers are VID and PID of Arduino) to usbhid-module. Here is example how to do it with RetrOrangePi 2.5.2:
- Make backup of `/boot/boot-retro.cmd` and `/boot/boot.scr`
- Edit `/boot/boot-retro.cmd` setenv bootargs -line and add `usbhid.quirks=0x2341:0x8036:0x40` to kernel parameters.
- `sudo mkimage -C none -A arm -T script -d /boot/boot-retro.cmd /boot/boot.scr`
- Reboot

#### Solution 2
Another possibility is change VID and PID to something that already has HID_QUIRK_MULTI_INPUT (0x40) activated in kernel.
Here you can see what quirks are activated to different VID/PIDs:
https://github.com/torvalds/linux/blob/master/drivers/hid/usbhid/hid-quirks.c

Edit VID/PID from `C:\Program Files (x86)\Arduino\hardware\arduino\avr\boards.txt` (after editing boards.txt, you have to close and reopen Arduino IDE). Example USB_VENDOR_ID_MOJO 0x8282, USB_DEVICE_ID_RETRO_ADAPTER 0x3201:
```
leonardo.build.vid=0x8282
leonardo.build.pid=0x3201
```

### Code
Select your controller by commenting and uncommenting stuff from beginning of source code and give your IO-pin numbers.
Every one uses same button numbering, so if you make multiple adapters for different controllers, you have to teach buttons to emulator/game (or whole emulation system like RetroPie or RetrOrangePi) only once.

#### Konami code
You can also enable or disable Konami-code-shortcut. For now it works only with Genesis 6-button controller (X+Y+Z+UP (or DOWN)), SNES-controller (X+Y+R1+UP (or DOWN)) and PSX-controller (rectangle+circle+triangle+UP (or DOWN)). "UP"-version is normal and "DOWN"-version swaps A and B. Some games demands that not other buttons are pressed before code and that's why only last pressed button can be button which have some function in emulator.

https://en.wikipedia.org/wiki/Konami_Code
https://en.wikipedia.org/wiki/List_of_Konami_code_games

## Tutorial
There is simple tutorial in [Tutorial](https://github.com/mcgurk/Arduino-USB-HID-RetroJoystickAdapter/tree/master/Tutorial) folder. I tried to make it as clear as possible, so even if you are not familiar with coding, you may can do modifications to it (example add a button).

## Polishing
If you want that less than 16 buttons are shown in joystick-settings, edit Joystick.cpp/Joystick2.cpp/Joystick3.cpp "USAGE_MAXIMUM"-line (0x10 = 16). You may to have create new project after that, because all libraries are not recompiled every time and I don't know how to force full recompile.

Adapter shows as "Arduino Leonardo", but if you want it to show some other name, edit `C:\Program Files (x86)\Arduino\hardware\arduino\avr\boards.txt`. You can even add sub menu to tools-menu by adding these lines (after editing boards.txt, you have to close and reopen Arduino IDE):

```
menu.usb_name=USB Product Name
leonardo.menu.usb_name.leonardo="Leonardo"
leonardo.menu.usb_name.leonardo.build.usb_product="Arduino Leonardo"
leonardo.menu.usb_name.leonardo_1="Genesis 6"
leonardo.menu.usb_name.leonardo_1.build.usb_product="Genesis 6-button adapter"
leonardo.menu.usb_name.leonardo_2="Genesis 3"
leonardo.menu.usb_name.leonardo_2.build.usb_product="Genesis 3-button adapter"
leonardo.menu.usb_name.leonardo_3="NES"
leonardo.menu.usb_name.leonardo_3.build.usb_product="NES-adapter"
leonardo.menu.usb_name.leonardo_4="SNES"
leonardo.menu.usb_name.leonardo_4.build.usb_product="SNES-adapter"
```

## Misc

### Arduino Uno R3
Arduino UNO R3 includes ATmega16u2 (along with ATmega328p) and it can be used too, but it needs some extra work (code doesn't work without modifications with UNO):
https://github.com/NicoHood/HoodLoader2

### Software USB
USB-device without ATmega16u2/32u4. Needs 3 resistors and 2 zener diodes.

https://www.obdev.at/products/vusb/index.html

### PS/2
PS/2-keyboard can be connected directly to Arduino and there is library for that.

http://playground.arduino.cc/Main/PS2Keyboard

### USB host
You can also use Arduino vice versa. You cannot read USB with normal Arduino, but there is shields to make Arduino as USB host. With that you can connect modern USB-joysticks and gamepads to old consoles.

https://github.com/felis/USB_Host_Shield_2.0

### Playstation, 3.3V, 3.7V or 5V?
There is contradictory data about Playstation controller voltages. I have one commercial adapter and it's 5V. I haven't measured official Playstation, but it is propably 3.3V.

### Other ideas
Of course Arduino can be used many other ways. Add a PIR motion detector module from Ebay (under $2) and your computer wakes up from sleep when you walk to room. Just read PIR-sensor data pin and if it is 1, send some keyboard button (which normally does nothing) to computer so it wakes up. Or make info-board to your kitchen or hallway wall from old Android tablet and it turns on backlight only when someone goes to kitchen.

## Links

#### Arduino
http://playground.arduino.cc/Main/InterfacingWithHardware

#### NES
http://www.mit.edu/~tarvizo/nes-controller.html

#### SNES
http://wiki.superfamicom.org/snes/show/Schematics,+Ports,+and+Pinouts

https://github.com/burks10/Arduino-SNES-Controller

#### Sega Megadrive / Genesis
https://www.cs.cmu.edu/~chuck/infopg/segasix.txt

https://jonthysell.com/2014/07/26/reading-sega-genesis-controllers-with-arduino/

#### Playstation

http://www.gamesx.com/controldata/psxcont/psxcont.htm

http://playground.arduino.cc/Main/PSXLibrary

http://problemkaputt.de/psx-spx.htm#controllersandmemorycards

#### Playstation Dualshock:

https://gist.github.com/scanlime/5042071

#### Nintendo 64 / Gamecube:

https://github.com/NicoHood/Nintendo

https://github.com/brownan/Gamecube-N64-Controller

#### Wii Classic Controller:

https://github.com/robotgeek/WiiClassy

http://wiibrew.org/wiki/Wiimote/Extension_Controllers/Classic_Controller
