# Install guide for a minimal Fedora 21 desktop

These are my notes for setting up a Fedora 21-installation with a small set of packages. Mainly for my own use but it's there in case anyone else needs it.

## Resons for using this guide

This document guides you through the processes of setting up a non-standard install. Why in the world would anyone do that?

- Small footprint means fewer updates and less security-holes (the standard desktop would install another 700 packages, 500 MB download, 1,4 GB installed).
- You are free to use the webbrowser and terminal emulator of your choice.
- You will not be bothered with apps you never use (cheese, gnome-maps, gnome-weather, gnome-documents, brasero).
- Most people nowadays use web-based applications for e-mail, word-processing, chatting and music listening (evolution, libreoffice, empathy, rhythmbox).
- A lot of applications aren't used in virtual machines or in front of the TV (scanning, printing, vpn, wifi, input-support, language-support, hardware-support, localized fonts).
- Why not use respins (Korora) or installers (Fedy)? Official packages and scripts tend to be better maintained and of higher quality. Security/trust. Keeping it vanilla makes it easier to use upstream bug-tracking, mailing-lists, chats and forums. Longer support. Safer upgrade-path.

## Installing the base system

Download netinst-iso (for Fedora 21 it's found under the download-page for Fedora Server). Install with package-set: "Minimal" (add any language-pack you'll use).

To use `sudo` instead of the root-user: once the install starts chose the **User Creation** dialog (with *Administrator* checked), don't use the *Root Password* dialog.

### Graphical environment

Once the install finishes you'll need an ethernet connection after the reboot to install the graphical environment. Run these commands to install X and GNOME.

```sh
$ sudo yum update
$ sudo yum install @base-x gnome-shell
$ sudo yum install gnome-terminal dejavu-sans-mono-fonts # terminal
$ sudo yum install NetworkManager-wifi # if you're on a laptop
$ sudo systemctl set-default graphical.target # enable on boot
$ sudo systemctl isolate graphical.target # start
```

## Great looking fonts

Fedora doesn't come prepackaged with some (non-free) patches and settings you'll need for your fonts to look good.

For additional hardware- and software-support you'll need to add a third-party repo called RPM Fusion containing packages that are not available in the standard Fedora repos.

```sh
$ sudo yum localinstall --nogpgcheck http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm http://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

Next install RPM Fusion's version of Freetype.

```sh
$ sudo yum install freetype-freeworld
```

And create `/etc/fonts/local.conf` from the contents in  [Fedy-plugin](https://github.com/satya164/fedy/blob/master/plugins/util/font_rendering.sh).

## Browser

A lot of webpages will have missing or bad-looking fonts. One solution is to install the Microsoft fonts.

```sh
$ yum install cabextract
$ sudo rpm -i https://downloads.sourceforge.net/project/mscorefonts2/rpms/msttcore-fonts-installer-2.6-1.noarch.rpm
```

Next choose between (or install both) **Firefox**

```sh
$ sudo yum install firefox dejavu-sans-fonts mozilla-adblockplus
```

**Chrome**

```sh
$ sudo cat << EOF > /etc/yum.repos.d/google-chrome.repo
[google-chrome]
name=google-chrome - \$basearch
baseurl=http://dl.google.com/linux/chrome/rpm/stable/\$basearch
enabled=1
gpgcheck=1
gpgkey=https://dl-ssl.google.com/linux/linux_signing_key.pub
EOF
$ yum install google-chrome-stable
```

I recommend installing the extensions *AdBlock* and *Adwaita (GNOME 3)*.

## Other utilities

You might need some more apps. These are the GNOME defaults.

```sh
$ sudo yum install gedit # Editor
$ sudo yum install nautilus # File manager
$ sudo yum install gnome-system-monitor # System monitor
$ sudo yum install eog # Image viewer
```

### Video-player and codecs

GNOME Videos (Totem).

```sh
$ sudo yum install totem gstreamer1-libav gstreamer1-plugins-bad-free gstreamer1-plugins-bad-freeworld gstreamer1-plugins-good gstreamer1-plugins-ugly gstreamer1-vaapi
```

VLC (RPM Fusion needs to be enabled).

```sh
$ sudo yum install vlc
```

## Differences against standard Fedora Workstation

If you need to find a package that you miss, run this command (check the output and cancel).

```sh
$ sudo yum groupinstall 'Fedora Workstation' --skip-broken
```
