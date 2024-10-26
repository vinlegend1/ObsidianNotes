+++
title = 'Fedora Post Install'
date = 2024-02-29T05:32:38+08:00
draft = false
+++

# Fedora Post Install Essentials

## Configuring Package Manager
`sudo vi /etc/dnf.conf`

```
fastestmirror=True
max_parallel_downloads=10
defaultyes=True
keepcache=True
```

## RPM Fusion and Better Media Codecs
This step is *important* because I could barely play videos (media in general) on browsers. In this sense, fedora is a gigantic pain in the butt. You need to go to [RPM Fusion](https://rpmfusion.org/Configuration)

Summary of commands

```
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

`sudo dnf config-manager --enable fedora-cisco-openh264`

### Media codecs
`sudo dnf swap ffmpeg-free ffmpeg --allowerasing` (if this works out of the box)

`sudo dnf groupupdate multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin --allowerasing`

`sudo dnf groupupdate sound-and-video`

recent Intel hardware
`sudo dnf install intel-media-driver`

old Intel hardware
`sudo dnf install libva-intel-driver`