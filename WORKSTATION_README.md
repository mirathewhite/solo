# fido2key
Implements a FIDO2 keytoken on the platform. When it runs, browsers will detect a new FIOO2 key.

This project is based on Solo, which is meant to be flashed to a USB stick. The software version of Solo uses UDP HID, while browsers only support USB HID.  The file `pc/device.c` has a file `use_udp = true` that switches from USB to UDP. Running in USB mode on the Mac fails because there is no file `/dev/hidg0`.  This is a system file that can only be created by the OS.

Linux USB support relies on a command `udevadm` that registers the USB device and creates rules that are automatically triggered by the device. We would need to write a similar  USB plug-in for Mac. Unclear if/how this would work on PC.


## Build and run Solo
This project is based on solokey. Even though it is technically cross-platform, in practice, only Linux has all the features Gradient needs.

### Linux

```
// install dependencies
sudo apt install libsodium-dev
brew install libsodium

// Build the solo key program
git clone https://github.com/solokeys/solo.git
cd solo
make update
make all
```

To run the program so that the browser can detect the FIDO2 device:
```buildoutcfg
./main -b hidg
```

### Mac
The Mac can only talk to the FIDO2 device over UDP. This means you cannot test it in the browser.

```
// install dependencies
brew install libsodium

// Build the solo key program
git clone https://github.com/solokeys/solo.git
cd solo
make update
make all

// Register solo as a USB device
cd solo/udev
sudo mkdir /etc/udev/rules.d
make setup
```

To run the program:
```
./main
```