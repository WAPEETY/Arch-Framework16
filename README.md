# Arch-Framework16
stuff that might help if you want to run arch on a Framework Laptop 16,
I might change stuff in future so check this repo multiple times :)

## 0. Other useful links
- [Framework Laptop 16 on Arch Wiki](https://wiki.archlinux.org/title/Framework_Laptop_16)
- [Framework Community](https://community.frame.work/c/framework-laptop-16/136)
- in specific chapters maybe I'll add more
## 1. My setup
```
wapeety@purple-dragon
--------------------- 
OS: Arch Linux x86_64
Host: Laptop 16 (AMD Ryzen 7040 Series) AG
CPU: AMD Ryzen 7 7840HS w/ Radeon 780M Graphics (16) @ 5.137GHz
GPU: AMD ATI c4:00.0 Phoenix1
GPU: AMD ATI Radeon RX 7700S/7600/7600S/7600M XT/PRO W7600
Memory: 3268MiB / 15175MiB
```

I have archlinux with encrypted disk and secureboot (I will not make a tutorial on how to do this since there are already enought)
As of today I couldn't use the TPM to unlock the disk (if you want to help, below you have the error)
```bash
[wapeety@purple-dragon ~]$ systemd-cryptenroll /dev/gpt-auto-root-luks --recovery-key
Device /dev/gpt-auto-root-luks does not exist or access denied.
Failed to allocate libcryptsetup context: Block device required
```

## 2. Read the F-ing manual
Remember to configure the regulatory domain, more info [here](https://wiki.archlinux.org/title/Framework_Laptop_16#WiFi_performance_on_AMD_edition)

## 3. Ambient light sensor
I used [illuminanced-git<sup>AUR</sup>](https://aur.archlinux.org/packages/illuminanced-git/).

To make it work correctly you have to tweak the config a little bit:
create a file called `/usr/local/etc/illuminanced.toml` and paste this:
```toml
[daemonize]
# log_to = "syslog" or /file/path
log_to = "syslog"
pid_file = "/var/run/illuminanced.pid"
# log_level = "OFF", "ERROR", "WARN", "INFO", "DEBUG", "TRACE"
log_level = "ERROR"

[general]
check_period_in_seconds = 1
light_steps = 10
min_backlight = 0
step_barrier = 0.1
max_backlight_file = "/sys/class/backlight/amdgpu_bl2/max_brightness"
backlight_file = "/sys/class/backlight/amdgpu_bl2/brightness"
illuminance_file = "/sys/bus/iio/devices/iio:device0/in_illuminance_raw"
event_device_mask = "/dev/input/event*"
event_device_name = "Asus WMI hotkeys"
enable_max_brightness_mode = true
filename_for_sensor_activation = ""

[kalman]
q = 1
r = 20
covariance = 10

[light]
points_count = 6

illuminance_0 = 0
light_0 = 0

illuminance_1 = 20
light_1 = 2

illuminance_2 = 200
light_2 = 4

illuminance_3 = 700
light_3 = 5

illuminance_4 = 1100
light_4 = 9

illuminance_5 = 3984
light_5 = 10
```

and then link the config in the right place with `sudo ln -s /usr/local/etc/illuminanced.toml /etc/illuminanced.toml` if `/etc/illuminanced.toml` already exists just delete it.

after this `sudo systemctl enable illuminanced.service && sudo systemctl start illuminanced.service` and should work.

## TODO: Fingerprint unlock, verify hardware acceleration on firefox, disk unlock with TPM, configure backups for my data and the system
