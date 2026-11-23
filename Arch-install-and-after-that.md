# Arch Linux configs
This are the configs that I made after installing EndeavourOS (Same as Arch Linux with some preinstalled packages)

At the bottom, there's also a part for regular tasks and maintenance that's need to be done regularly.
 
## Tasks:

- [x] Linux LTS kernel
- [x] Mirrors
- [x] Bluetooth
- [x] Graphic drivers
	- [x] Drivers installation
	- [x] Video Acceleration
	- [x] Apps hardware acceleration configs
- [ ] Firefox tweaks
- [x] Fonts and readability
- [x] Wayland
	- [x] Install plasma wayland
	- [x] Set environmental variables for apps
- [ ] Dolphin and KIO
- [ ] Add Kernel parameter resume variable
- [x] Backup dotfiles

## Installation and Configs

### installation

Installing Arch Linux is not easy. That's why a distribution of it was installed. [EndeavourOS](https://endeavouros.com/), which is an Arch-based distribution without almost any modification, sounds  probably good!

There was nothing special about the process.

But to be in the safe side, let's check all the things mentioned here:

[https://wiki.archlinux.org/title/Installation\\\_guide](https://wiki.archlinux.org/title/Installation%5C_guide)

### mirrors

Arch has many mirrors. But not all of them are as fast as the others (The closest ones).

Using mirrors can be found at `/etc/pacman.d/` . There are two of them here! One for Arch main repositories, and one for EOS.

A list of all available Arch main repositories along with their status could be found [here](https://archlinux.org/mirrors/status/).

They can be sorted by [reflector](https://wiki.archlinux.org/title/Reflector). It also has a service, which can be run at boot, or be triggered by timer.

I run it with this command to get list of 5 latest synced https and http mirrors and sort them by their score:

```
reflector --verbose --country IR --protocol https --protocol http --sort score --latest 5
```

### fstab

* Add swap (wasn't done by EOS installer)
* Add bind mounts (For home folders)

Note: I installed OS on SSD, also `/home` on SSD (Because of cache and stuff) But I put all home folders on HDD and bind-mounted them in `/home` directories.

There's usually a problem if you mount a folder with bind option. They will be shown as monted folder and could even be unmounted. To hide this, add \`x-gvfs-hide\` option into fstab.

### trim

Enable `fstrim` timer on SSD. Also don't use discard option in `/etc/fstab` in this case.

```
systemctl enable fstrim.timer
systemctl start fstrim.timer
```

### Kernels

There are several official kernels for Arch. The main ones are `linux` and `linux-lts`.

Install `linux-lts` kernel and headers.

```
sudo pacman -S linux-lts linux-lts-headers
```

And re-generate grub configuration file:

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

By default, when turning on and before booting, you can use your kernel (in grub menu). It would automatically use the latest kernel if you wait a few seconds and don't choose anything else.

But wait, you can also do something!

edit `/etc/default/grub` and add/modify following options to these values:

```
GRUB_DISABLE_SUBMENU=y
GRUB_DEFAULT=saved
GRUB_SAVEDEFAULT=true
```

And them re-generate grub configs. So now your last chosen option will be saved!

### Bluetooth

Make sure that `bluez` and `bluedevil` is installed. Then start and enable bluetooth service.

```
systemctl start bluetooth.service
systemctl enable bluetooth.service
```

### Remove old boot entries from UEFI

I've you've already installed some other operation systems, you've probably see them in boot menu. 

You can list them with efibootmgr command. It also has options to delete it. 

### Fix fonts

#### Set emoji fonts
add this to `~/.config/fontconfig/conf.d/99-noto-mono-color-emoji.conf`

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
  <alias>
    <family>serif</family>
    <prefer>
      <family>Noto Color Emoji</family>
    </prefer>
  </alias>
  <alias>
    <family>sans-serif</family>
    <prefer>
      <family>Noto Color Emoji</family>
    </prefer>
  </alias>
  <alias>
    <family>monospace</family>
    <prefer>
      <family>Noto Color Emoji</family>
    </prefer>
  </alias>
</fontconfig>
```

[Source](https://community.kde.org/Distributions/Packaging_Recommendations#Fontconfig_configuration)

#### Set Persian fonts
Here's the complete config with details:
[Set Vazirmatn font globally for Persian (Farsi) text](./Set-Persian-Font-Globally.md)


### Fix Hard Disk standby/spindown
Since I'm tired to describe the situation, it's [here](https://bbs.archlinux.org/viewtopic.php?pid=2068766). 
To summarize, Hard disks has two value to save power.
1. APM (Advanced power management)
2. Spindown

Read `man hdparm` for more information.

The problem is for APM 127 and below, disks goes to standby after 10s, regardless of the value. for above it, it never goes to standby!

It can be set to desired value by making a udev rule for hdparm, or by making a config file for udisks2. Both do the same thing (They're only a wrapper HDD manufacturer).

If I make udev rule and systemd service for hdparm, udisks access it every 10min for SMART test, so prevent it from going to standby. If I set value for udisks itself, it respect its own configs.

```
$ cat /etc/udisks2/TOSHIBA-MQ04ABF100-58QAPWL2T.conf
[ATA]
APMLevel=128
StandbyTimeout=241
```


### Graphics and Hardware Video Acceleration

#### Drivers
For intel graphics, `mesa` package should be installed. 
And I'll remove `xf86-video-intel`.
Also install `Vulkan-intel` for more support. [Source](https://wiki.archlinux.org/title/intel_graphics)

My laptop has two GPU (one Intel UHD Graphics 620 and one NVIDIA GM108M). It looks NVIDIA is not enable by default when only nouveau driver is installed. But let's try to turn it of. Since there wasn't any option in BIOS, so I used [this](https://wiki.archlinux.org/title/Hybrid_graphics#Using_udev_rules) modprobe and udev rules to prevent it from being active. 

#### Video Acceleration
install `intel-media-driver` for newer Intel GPUs. it's iHD driver. There's also package `libva-intel-driver` which is i965 driver. newer intel gpus (from 2014) are supported by the first one and the last only supports up to 2017 GPUs. So I'll use the first.

This "VAAPI" can be verified in multiple ways. I used `vainfo` to get information about all capabilities and also `mpv` flag `--hwdec=auto` to play a video. It seems working!

Each application (FFmpeg, VLC, mpv, ...) has it's own way of enabling hardware acceleration. 

[Source](https://wiki.archlinux.org/title/Hardware_video_acceleration)


### DNS encryption
https://wiki.archlinux.org/title/Dnscrypt-proxy

Note: We can set DNS in `/etc/resolv.conf` but it's not presistant. It can be modified by [NetworkManager](https://wiki.archlinux.org/title/NetworkManager#/etc/resolv.conf) or [openresolv](https://wiki.archlinux.org/title/Openresolv). To make sure, we'll prevent both from editing `resolv.conf` file.

For example [here](https://wiki.archlinux.org/title/NetworkManager#Unmanaged_/etc/resolv.conf) is a way to disable NetworkManager changing DNS.

Also add `resolvconf=NO` to the `/etc/resolvconf.conf`.

**Update: ** The previous approach had issues when using VPNs (In my case, university VPN to access some journals). Here's a better method:

1. Make sure `dnscrypt-proxy` and `systemd-resolvconf` are installed and enabled.
2. Make `systemd-resolved` handle `resolv.conf` by: 

	```
	sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
	```

3. Config `systemd-resolved` like this:

	```
	$ cat /etc/systemd/resolved.conf.d/dnscrypt-dns.conf 
	[Resolve]
	DNS=::1 127.0.0.1
	FallbackDNS=
	DNSStubListener=yes
	# Cache=no  # Default is disabled for local adresses based on the man page
	CacheFromLocalhost=no
	Domains=~.

	```

4. Set `NetworkManager` to use `systemd-resolved`:
	```
	cat /etc/NetworkManager/conf.d/dns.conf 
	[main]
	dns=systemd-resolved
	```

5. If using VPN connection (Like PPTP), don't forget to add internal domains by setting "search domains" or something like this in the VPN config settings.

6. Finally, I ended up making `resolv.conf` immutable (`chattr +i`) to make sure DNS is applied and nothing overwrites the file:

	```
	sudo chattr +i /etc/resolv.conf
	```

	If you need to edit it again, unlock it with `sudo chattr -i /etc/resolv.conf`.

### Setting Locales
To set measurement units and papersize to metric and standard units.

First add or uncomment `en_AU.UTF-8 UTF-8` in /etc/locale.gen and then run `sudo locale-gen` to enable en_AU locale on the system (Which uses metric units). Run `locale -a` to make sure it's enabled.

Now edit this file and set variables to desired ones:

```
/etc/locale.conf
---

LANG=en_US.UTF-8
LC_ADDRESS=en_US.UTF-8
LC_IDENTIFICATION=en_US.UTF-8
LC_MEASUREMENT=en_AU.UTF-8
LC_MONETARY=en_US.UTF-8
LC_NAME=en_US.UTF-8
LC_NUMERIC=en_US.UTF-8
LC_PAPER=en_AU.UTF-8
LC_TELEPHONE=en_AU.UTF-8
LC_TIME=en_AU.UTF-8
```

Check it's set correctly by running `locale` command. (Also delete `~/.config/plasma-localerc` in Plasma desktop).
Then rebooting the system should apply the changes.
([Source](https://wiki.archlinux.org/title/Locale))

### Firefox tweaks

**Native Wayland:**
set `MOZ_ENABLE_WAYLAND=1` environmental variable

**Hardware Video Acceleration:** 
set `media.ffmpeg.vaapi.enabled` to `True` in `about:config`

**Store Session Interval:** 
Set `browser.sessionstore.interval` to `600000` in `about:config`

**KDE Integration:**
Install `xdg-desktop-portal` and `xdg-desktop-portal-kde`.
Then Set `widget.use-xdg-desktop-portal.file-picker` from 2 to 1.

NOTE: Arch wiki also says `widget.use-xdg-desktop-portal.mime-handle` to 1 but it make Forefox ask every time if you want to set it as default browser. So I didn't changed it. 

[Source 1](wiki.archlinux.org/title/firefox), [Source 1](https://wiki.archlinux.org/title/Firefox/Tweaks) 

### Change from mkinitcpio to Dracut

New EndeavourOS release and many other distros uses Dracut (Which I'm not sure yet what it is!). But anyways, EOS has a guide how to do it, which is [here](https://discovery.endeavouros.com/installation/dracut/2022/12/). I did it since my EOS installation is not new.

### Hibernation
Kernel module should be load (this is the guide for Draucut):
https://wiki.archlinux.org/title/Dracut#Hibernation

In my case, it worked just by loading module and having enough swap, without doing anything else.

(Also [this](https://wiki.archlinux.org/title/Power_management/Suspend_and_hibernate#Hibernation) in case of mkinitcpio instead)

### Add resume variable 

Draft (not done yet):

https://wiki.archlinux.org/title/kernel_parameters

### Enable Zram or Zswap

Both [Zram](https://wiki.archlinux.org/title/Zram) and [Zswap](https://wiki.archlinux.org/title/Zswap) looks similar but different in how they work. Both of them compresses some part of the data in RAM. But for example, Zswap needs also a swap space to be available. Enable one of them, but note that **ONLY ONE** of them should be enabled.

### `aursh`

I don't use `rua` anymore. I wrote my own minimal AUR helper script instead: `aursh`, which lives in my [dotfiles repo](https://codeberg.org/mokazemi/dotfiles) (managed by [yadm](https://yadm.io)) at `bin/aursh`. It builds packages with `git` + `makepkg` and installs them only after reviewing and confirming. It's only used to install and update AUR packages, so it should be used along with `pacman` (Which is what I prefer).

### Enable user repositories

I enabled [chaotic-aur](https://wiki.archlinux.org/title/Unofficial_user_repositories#chaotic-aur) repository, since it looks promissiong and secure. 

## Things to Install

_TO BE COMPLETED._

**KDE Plasma desktop + integrations**\:

* `plasma-meta` (meta package) OR `plasma` (group package) OR `plasma-desktop` (minimal)
* `plasma-wayland-session`
* `xdg-desktop-portal` and `xdg-desktop-portal-kde` and `kdialog`
* `kaccounts-providers`
* `noto-fonts-emoji`
* `ffmpegthumbs` and `qt6-imageformats` and `kimageformats` and `kdegraphics-thumbnailers`

**Security**

* firewall

## Regular tasks

### Upgrading

it's simply done by a command:

```
sudo pacman -Syu
```

### Clear cache

```
sudo paccache -rk2
```
