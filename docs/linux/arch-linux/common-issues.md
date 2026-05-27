# Package Management
```
pacman -Q # lists every packages
pacman -Qe # only explicitly installed packages
pacman -Qet # true top-level packages with no requirements
pacman -Qm # foreign packages installed using AUR
```

## Booting from USB
On ASUS ROG G14, when powering on, spam ESC.

# Connecting to WIFI
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