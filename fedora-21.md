# Install guide for a minimal Fedora 21 desktop

These are my notes for setting up a Fedora 21-installation but with a smaller set of packages. It's mainly for my own use but it's out there in case anyone else needs it.

## Reasons for using this guide

This document guides you through the processes of setting up a non-standard install. Why in the world would anyone do that?

- Smaller footprint means fewer updates and security-holes.
- Your webbrowser or terminal emulator of choice might not be the ones in a default install. Resulting in multiple applications for the same purpose.
- You will not be bothered with apps you never use (cheese, gnome-maps, gnome-weather, gnome-documents, brasero).
- People nowadays use web-based applications for e-mail, word-processing, chatting and music (evolution, libreoffice, empathy, rhythmbox).
- A lot of applications aren't used in virtual machines or in front of the TV (scanning, printing, vpn, wifi, input-support, language-support, hardware-support, localized fonts).
- Better performance, debatable but bootup-time is definitely improved.
- Why not use respins (eg Korora) or third-party installers (eg Fedy)? Official packages and scripts tend to be better maintained and of higher quality. Security/trust. Keeping it vanilla makes it easier to use upstream bug-tracking, mailing-lists, chats and forums. Longer support. Safer upgrade-path.

## Installing the base system

Download [Fedora Server Netinstall ISO](https://getfedora.org/en/server/download/) and [transfer it onto onto a USB-disk](http://docs.fedoraproject.org/en-US/Fedora/21/html/Installation_Guide/sect-preparing-boot-media.html).

In the installer under *Software Selection*, select **Minimal Install**.

To use `sudo` instead of root: once the install starts choose the **User Creation** dialog (with *Make this user administrator* checked), don't use the *Root Password* dialog.

### Graphical environment

Run these commands to install X, GNOME and a terminal.

```sh
$ sudo yum install @base-x gnome-shell
$ sudo yum install gnome-terminal dejavu-sans-mono-fonts bash-completion # terminal
$ sudo yum install NetworkManager-wifi # if you're on a laptop
$ sudo systemctl set-default graphical.target # enable on boot
$ sudo systemctl isolate graphical.target # start
```

At this point you will have a fully functional desktop but with ~820 fewer packages compared to Fedora Workstation.

### Web browser

Next you'll probably want a web-browser so that you can copy-and-paste commands. This will install Firefox

```sh
$ sudo yum install firefox dejavu-sans-fonts mozilla-adblockplus
```

## Hardware and software support

For additional hardware- and software-support you can add a third-party repository called RPM Fusion containing packages that are not available in the standard Fedora repos (firmware for your wifi-card, graphics-drivers, video-codecs and more).

```sh
$ sudo yum localinstall --nogpgcheck http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm http://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

### Better looking fonts

Fedora doesn't come prepackaged with some patches and settings you'll need for your fonts to look sharp. Install RPM Fusion's version of FreeType (that comes with subpixel anti-aliasing) and configure hinting.

```sh
$ sudo yum install freetype-freeworld
$ sudo curl -o /etc/fonts/local.conf https://raw.githubusercontent.com/benmat/fedora-install/master/fontconfig.xml
```

Restart any applications to use the new changes.

Many webpages will have missing fonts, one solution for complete coverage is to install the Microsoft fonts.

```sh
$ sudo yum install cabextract xorg-x11-font-utils
$ sudo rpm -i https://downloads.sourceforge.net/project/mscorefonts2/rpms/msttcore-fonts-installer-2.6-1.noarch.rpm
```

## Other utilities

You might need some more apps, these are the GNOME defaults.

```sh
$ sudo yum install gedit # Editor
$ sudo yum install nautilus # File manager
$ sudo yum install gnome-system-monitor # System monitor
$ sudo yum install eog # Image viewer
```

### Chrome

```sh
$ sudo cat << EOF > /etc/yum.repos.d/google-chrome.repo
[google-chrome]
name=google-chrome - \$basearch
baseurl=http://dl.google.com/linux/chrome/rpm/stable/\$basearch
enabled=1
gpgcheck=1
gpgkey=https://dl-ssl.google.com/linux/linux_signing_key.pub
EOF
$ sudo yum install google-chrome-stable
```

I recommend installing the extensions *AdBlock* and *Adwaita (GNOME 3)*.

### Video-player and codecs

**GNOME Videos (Totem)**

```sh
$ sudo yum install totem gstreamer1-libav gstreamer1-plugins-bad-free gstreamer1-plugins-bad-freeworld gstreamer1-plugins-good gstreamer1-plugins-ugly gstreamer1-vaapi
```

**VLC**

```sh
$ sudo yum install vlc
```

### Flash

If you need Flash-support in Firefox.

```sh
sudo rpm -ivh http://linuxdownload.adobe.com/adobe-release/adobe-release-x86_64-1.0-1.noarch.rpm
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-adobe-linux
sudo yum install flash-plugin nspluginwrapper alsa-plugins-pulseaudio
```

### MTP support

If you, for example, need to copy photos from your Android-device via USB.

```sh
sudo yum install gvfs-mtp
```

## Compare packages with standard Fedora Workstation

If you need help finding a package you know is installed in Fedora Workstation check the output of this command.

```sh
$ sudo yum groupinstall 'Fedora Workstation' --skip-broken --assumeno
```
