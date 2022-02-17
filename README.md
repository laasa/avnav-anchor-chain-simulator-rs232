# avnav-anchor-chain-simulator-rs232
These plugin simulates the anchor chain pulses via "outputs" DTR (pulses) and RTS (direction) of RS232 device

![grafik](https://user-images.githubusercontent.com/98450191/153689785-2802730f-096a-490d-aaaf-1e5c5fca7244.png)

# General

The plugin simulates pulses of the reed relais on the anchor winch.
Addtionally it need the Up (and Down) information of the anchor windlass relais.

It is widely based on the
- seatalk remote plugin (https://github.com/wellenvogel/avnav-seatalk-remote-plugin),
- more nmea plugin      (https://github.com/kdschmidt1/avnav-more-nmea-plugin).

# Parameter

- device: e.g. '/dev/ttyUSB0'
- usbid: as alternative for devive name
- debuglevel: debug level

# Details
- every second a pulse (5 ms duration) on DTR "output" (read as DSR in reader plugin) is generated.
- After 100 pulses the direction changes via changing the level on RTS "output" (read as CTS in reader plugin).
- It is possible that these plugin and avnav-seatalk1-simulator-rs232 plugin can share the same device (e.g. "/dev/ttyUSB_SeatalkOut")

# Hardware needs
- second USB-RS232-adapater
- A gender changer between USB-RS232-adapater of plugin seavnav-anchor-chain-reader-rs232 and the adapter of these plugin

# Software installation

To install this plugin please 
- create directory '/usr/lib/avnav/plugins/avnav-anchor-chain-simulator-rs232' and 
- copy the file plugin.py to this directory.

# TODOs
- only tested with linux

# Helpers
Setup the serial devices by their serial numbers
- Label your first USB serial device (e.g SeatalkOut)
- Connect the first USB serial device to the PC
- Get the vendorID, deviceID and serial number of the tty device (here "/dev/ttyUSB0")
   udevadm info -a -n /dev/ttyUSB0 | grep {idVendor} | head -n1  => ATTRS{idVendor}=="0403" 
   udevadm info -a -n /dev/ttyUSB0 | grep {bcdDevice} | head -n1 => ATTRS{bcdDevice}=="0600"
   udevadm info -a -n /dev/ttyUSB0 | grep {serial} | head -n1    => ATTRS{serial}=="A10KKBM3"
- creates an udev rule
  mcedit sudo mcedit /etc/udev/rules.d/10-local.rules
   SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{serial}=="A10KKBM3", MODE="0666", SYMLINK+="ttyUSB_SeatalkOut"
- Continue with the next devices
- at the end the file /etc/udev/rules.d/10-local.rules may look like that
    SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{serial}=="A10KKF9V", MODE="0666", SYMLINK+="ttyUSB_SeatalkInp"
    SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{serial}=="A10KKBM3", MODE="0666", SYMLINK+="ttyUSB_SeatalkOut"
- Use this names in avnav (e.g: "/dev/ttyUSB_SeatalkInp")
