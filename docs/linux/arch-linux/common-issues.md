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