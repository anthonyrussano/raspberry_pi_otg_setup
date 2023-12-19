# raspberry_pi_otg_setup

Here is a very rough draft of how I got things setup...

## Raspberry Pi Setup

- Setup a new SD Card using Raspberry Pi Imager
  - Set custom hostname
  - Configure username / password
  - Configure WiFi settings
- After writing the Raspberry Pi OS to the SD Card, edit the following files:
  - /boot/config.txt
    - Add the following to the end of the file:
    - `dtoverlay=dwc2`
  - /boot/cmdline.txt
    - Add the following after rootwait
    - `modules-load=dwc2,g_ether`
- Boot the pi up by plugging the Pi into a host machine via USB
  - First boot may take a few minutes
- Attempt to SSH into the Pi once it's booted up using `hostname.local`
  - Alternatively you can use the wifi IP address (can be obtained from router).
- Set a static IP address for the USB0 interface
  - I did this by editing the `/etc/rc.local` file.
    - `sudo chmod a+x /etc/rc.local`
    - `sudo nano /etc/rc.local`
    - Add the following line before `exit 0`
      - `ifconfig usb0 169.254.8.61 netmask 255.255.0.0 up`
- Reboot the Pi

## Host Setup (Linux)

- The output from `ip a` on your host machine should show a new Ethernet interface.
  - Example: `enxeaafd1291b56`
    - The name `enx` followed by a MAC address format suggests it's a USB Ethernet device.
    - This should be the direct USB interface connection to the Raspberry Pi device configured as an Ethernet gadget.
  - You should notice that it currently has no IP address configured.
  - This command sets a static IP address for the interface with a subnet mask of 255.255.0.0
    - `sudo ip addr add 169.254.8.62/16 dev enxeaafd1291b56`
- Now you should be able to SSH into the PI using the ip address configured for the `usb0` interface (above)
  - Example: `ssh 169.254.8.61`
    - This connection does not require wifi, and the ip address should stay the same between reboots of the pi.
