# Bluetooth
```shell
bluetoothctl
devices # get MAC address of device to pair
scan on # find devices
pair MAC_address
trust MAC_address # optional
cononect MAC_address
```

# Unity Hub
```shell
yay -S unityhub
yay -S gconf libxm12-legacy cpio # without these, unity hub crashes silently on launch
```

# Steam
Steam didn't work due to a Segmentation Fault. However, this was found to be due to missing drivers. This will vary from device to device.
```
# ASUS ROG G14
sudo pacman -S linux-headers nvidia-open-dkms nvidia-utils nvidia-settings
```

# Package Management
```
pacman -Q # lists every packages
pacman -Qe # only explicitly installed packages
pacman -Qet # true top-level packages with no requirements
pacman -Qm # foreign packages installed using AUR

pacman -Rns # deletes a package and its unneeded dependencies
```

# Booting from USB
On ASUS ROG G14, when powering on, spam ESC.

# Connecting to WIFI normaally
Use the NetworkManager CLI (nmcli)

```
nmcli device wifi list
nmcli device wifi connect "YOUR_SSID" password
```

# Connecting to WIFI from Boot
Check whether you have a network connection
```shell
ip a or ip addr show
```

Then run
```
iwctl
# incide iwtcl
device list
station <wifi-device> scan
station <wifi-device> get-networks
station <wifi-device> connect "Wifi Name"
exit
# test with
ping 1.1.1.1 or ping archlinux.org
```

# Audio Devices

I ran into a problem where audio was not working. Audio relies on three packages: `wpctl`, `pipewire`, `wireplumber`.

```shell
wpctl status
```

Showed that there were no audio or video devices.

It seems like I was missing the packages for my AMD audio drivers.

I ran the following, from ChatGPT

```bash
sudo pacman -Syu \
  alsa-utils alsa-lib alsa-firmware \
  sof-firmware linux-firmware \
  pipewire pipewire-alsa pipewire-pulse wireplumber
```

Upon rebooting, everything worked fine. It seems like my mute button was not working though
