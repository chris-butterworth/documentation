# Debian Gnome install on Windows Surface Pro

All instructions are from [this](https://github.com/linux-surface/linux-surface/wiki/Installation-and-Setup) guide

This is a backup of my exact steps for Debian Gnome on a surface pro 7

Windows partition was fragged and couldn't be shrunk, so I just binned it off and installed on full disk

~~[Debian KDE Live](https://www.debian.org/CD/live/)~~

Don't use the Live install image, caused all sorts of issues. Use the full offline standard [Debian ISO](https://www.debian.org/distrib/), non graphical install.  

As of 10/2025 the [camera is not working](https://github.com/linux-surface/linux-surface/wiki/Supported-Devices-and-Features#surface-tablets) on the SP7

- `sudo apt update`
- `sudo apt upgrade -y`
- `sudo apt install wget git`
```text 
wget -qO - https://raw.githubusercontent.com/linux-surface/linux-surface/master/pkg/keys/surface.asc \
    | gpg --dearmor | sudo dd of=/etc/apt/trusted.gpg.d/linux-surface.gpg`
```

``` text
echo "deb [arch=amd64] https://pkg.surfacelinux.com/debian release main" \
	| sudo tee /etc/apt/sources.list.d/linux-surface.list
```
- `sudo apt update`
- `sudo apt install linux-image-surface linux-headers-surface libwacom-surface iptsd`
- `sudo apt install linux-surface-secureboot-mok`
- restart system, when blue box shows up press any key, then enroll the key. The password is `surface` 
- `sudo update-grub`

Done! Touchscreen etc should work

To do - [post install steps](https://github.com/linux-surface/linux-surface/wiki/Installation-and-Setup#post-installation)