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

## 4. Fingerprint unlock
The Framework 16 has a standard fingerprint sensor so just follow [this](https://wiki.archlinux.org/title/Fprint) guide to enroll your fingerprint
In the installation only `fprintd` and `imagemagick` are needed

- [This thread](https://community.frame.work/t/guide-solved-sudo-and-login-with-fingerprint-reader-under-kde-arch-linux/37009/6) on the framework forum might help you 

### 4.1 Fingerprint unlock on SDDM
Edit the file `/etc/pam.d/sddm` and add on top under `#%PAM-1.0`

`auth        sufficient  pam_fprintd.so`

### 4.2 Fingerprint for sudo auth

I have added the fingerprint as a second-way auth in sudo by adding it under the first rule

`auth            sufficient      pam_fprintd.so` this is the line I've added and this is how my `/etc/pam.d/sudo` file looks now:

```
#%PAM-1.0
auth            sufficient      pam_unix.so try_first_pass likeauth nullok
auth            sufficient      pam_fprintd.so
auth            include         system-auth
account         include         system-auth
session         include         system-auth
```

## 5. Eduroam Wi-Fi
Download the python script from:
https://cat.eduroam.org/#

Run it with `python <scriptname>.py` if you get an error like: _NetworkManager configuration failed_ probably your missing `python-dbus`
All done.

## 6. Zsh, powerlevel10k
Just follow [this guide](https://github.com/romkatv/powerlevel10k?tab=readme-ov-file#installation), I used the oh-my-zsh method

**Remember:** change on Konsole the font `Open Settings` → `Edit Current Profile` → `Appearance`, `Select Font` and change to `MesloLGS NF Regular`.

## 7. Keyboard backlight
Framework laptop keyboard as we know is divided in modules and each module is configurable using a copy of the VIA app provided by framework itself at [keyboard.frame.work](https://keyboard.frame.work)
First of all you'll need a chromium based browser, sadly firefox has decided not to implement this kind of API because they believe it's dangerous (even if things such as camera access and others permissions are "perfectly fine") follow [this discussion](https://connect.mozilla.org/t5/discussions/fully-support-web-usb-and-web-serial/m-p/62) to see more.

To access the devices you need to edit udev rules, by placing [this file](https://github.com/qmk/qmk_firmware/blob/master/util/udev/50-qmk.rules) in `/etc/udev/rules.d/50-qmk.rules`

```
sudo nano /etc/udev/rules.d/50-qmk.rules
sudo udevadm control --reload-rules
sudo udevadm trigger
```

- [Source](https://docs.qmk.fm/faq_build#linux-udev-rules)

## 8. Fan profiles
[I use this](https://github.com/TamtamHero/fw-fanctrl)

Useful links:
- [Arch Wiki](https://bbs.archlinux.org/viewtopic.php?id=285709)

## 9. BTRFS Backup
I personally use timeshift with grub-btrfs and timeshift-autosnap:

Follow the [Arch guide](https://wiki.archlinux.org/title/Timeshift)
then install [timeshift-autosnap](https://aur.archlinux.org/packages/timeshift-autosnap)
at the end to make sure grub-btrfs works you need to run `sudo grub-mkconfig` the first time, after that you will find all the bootable snapshots in a sub directory in the GRUB menu

## 10. Offsite data backup
As of today I have a nextcloud instance so I use the nextcloud-client to backup some folders in the system to my server. Since this is a public repo I'll not include any further info but remember to add to excluded files:
`node_modules`
`__pycache__`
`.env`

## 11. Grub theme
Literally follow the guide from [the repo](https://github.com/HeinrichZurHorstMeyer/Framework-Grub-Theme)

## TODO: hardware acceleration on firefox (doesn't work), disk unlock with TPM
