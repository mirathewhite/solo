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

// Build the solo key program
git clone https://github.com/solokeys/solo.git
cd solo
make update
make all
```

# Configure USB integration - In Progress
```
// Register solo as a USB device
cd udev
sudo mkdir /etc/udev/rules.d
make setup
cd ..
```
You will need to enter your password.

```
cd tools/gadgetfs
make all
```

To run the program so that the browser can detect the FIDO2 device:
```
./main -b hidg
```

### Troubleshooting
Right now `make` fails to install `gadgetfs`.
Looking at https://www.duboucher.eu/gadgetfs-ubuntu.html


The problem in the Makefile is:
```
dummy_hcd.c: /usr/src/linux-source-$(SVERSION)/linux-source-$(SVERSION).tar.bz2
tar -xjvf $^ linux-source-$(SVERSION)/drivers/usb/gadget/udc/dummy_hcd.c &&\
    cp linux-source-$(SVERSION)/drivers/usb/gadget/udc/dummy_hcd.c $@
```
Get an error that cannot build dependency `/usr/src/linux-source-$(SVERSION)/linux-source-$(SVERSION).tar.bz2`
This source file does not exist in the system.

There appear to be two options
1. https://blog.soutade.fr/post/2016/07/create-your-own-usb-gadget-with-gadgetfs.html
which shows how to automatically install GadgetFS on Linux.
2.https://askubuntu.com/questions/1268618/trying-to-build-mariadb-i-get-the-error-you-must-put-some-deb-src-uris-in-yo
this requires fiddling with low level configuration files and running lots of scripts.
https://askubuntu.com/questions/795668/adding-source-uris


This installs some package managers
```
sudo apt-get install aptitude
sudo aptitude install dkms


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
```

To run the program:
```
./main
```
