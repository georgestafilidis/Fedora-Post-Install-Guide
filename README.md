# Fedora Post Install Guide
An unofficial, beginner-friendly guide I created to help new Fedora Workstation users get comfortable with the basics.

## Table of Contents

- [Update the System First (Recommended)](#update-the-system-first-recommended)
- [Multimedia Codecs](#multimedia-codecs)
- [Optional Cleanup](#optional-cleanup)
  - [Input Methods](#input-methods)
  - [Unnecessary Apps](#unnecessary-apps)
  - [System Services](#system-services)
  - [Small Boot Speed Tweak](#small-boot-speed-tweak)
- [Recommended Apps](#recommended-apps)
- [Recommended GNOME Extensions](#recommended-gnome-extensions)

## Update the System First (Recommended)
Fedora keeps your system secure and stable with regular updates. Always start here after a fresh install or before big changes. The easiest way:  
Open the **Software** app $\rightarrow$ go to the **Updates** tab $\rightarrow$ click **Download**.

You can also do it in the terminal:
```bash
sudo dnf upgrade --refresh
```
- `sudo` $\rightarrow$ runs as admin (you'll have to type your password)
- `dnf` $\rightarrow$ Fedora's package manager (think of it like an app store in the terminal)

If it installs a new kernel (you’ll see “kernel” in the output), reboot:
```bash
sudo reboot
```

> [!TIP]
> GNOME Software uses offline updates by default (downloads first, applies on reboot, safest). The terminal command applies changes immediately (live update, still fine for most cases)

## Multimedia Codecs
Fedora doesn't include some common video / audio codecs. The easiest and most reliable fix is to enable the trusted **RPM Fusion** repositories and install full codec support.

Run these commands one after another:

```bash
# Enable RPM Fusion Free + Nonfree (one-time setup)
sudo dnf install \
  https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
  https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```
```bash
# Install full codecs (this is the recommended way)
sudo dnf swap ffmpeg-free ffmpeg --allowerasing
sudo dnf group install multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
```

After running these, test playback in Firefox (open a YouTube video or any MP4 file).
It should be smooth now.

## Optional Cleanup
These steps remove some pre-installed items that many users do not need.  
Everything here is safe and reversible. You can always reinstall anything later with `sudo dnf install <package-name>`. You can also find most of these items in the **Software** app (explained later). 

### Input Methods
Fedora includes input methods for typing in many languages.  
If you only use one language (such as English) and do not need these tools, you can remove them safely.

Run this command:
```
sudo dnf remove ibus-m17n ibus-typing-booster ibus-hangul ibus-libpinyin ibus-anthy
```

> [!NOTE]
> You can find these Input Sources in the **Explore** tab of the **Software** app. Open **Software** and scroll down until you see Input Sources (if you don't see the category, make sure "Only show verified developers" is disabled in the app preferences).

<img src="https://github.com/georgestafilidis/Fedora-Post-Install-Guide/blob/main/assets/input_sources.gif?raw=true">

### Unnecessary Apps
I remove these apps because I do not use them. Better alternatives exist for most of them, or they feel outdated. Your mileage may vary. Keep whatever you like.

- **gnome-tour** - Welcome tour
- **gnome-color-manager** - Color profile manager
- **gnome-connections** - Remote desktop client
- **gnome-contacts** - Contact manager
- **gnome-weather** - Weather app
- **gnome-abrt** - Graphical crash reporter frontend
- **gnome-boxes** - Virtual machine manager
- **simple-scan** - Document scanner
- **snapshot** - Camera app

Run this to remove them all:

```
sudo dnf remove gnome-tour gnome-color-manager gnome-connections gnome-contacts gnome-weather gnome-abrt gnome-boxes simple-scan snapshot
```

### System Services
`gnome-abrt` is the graphical crash reporter (we already removed it in the apps section if you did that step).  
`abrt` is the actual background service (Automatic Bug Reporting Tool) that collects crash data.

If you do not plan to report crashes to Fedora developers, you can remove it safely. It saves a little space and removes one unnecessary background process.

```
sudo dnf remove abrt
```

> [!NOTE]
> Removing `abrt` hides the entire **Diagnostics** entry in **Settings** $\rightarrow$ **Privacy & Security** (inside the **System** section). If you reinstall `abrt` later and the entry does not appear again, start the service manually:

```bash
sudo systemctl start abrtd
```

<table>
  <tr>
    <td align="center">
      <img src="https://github.com/georgestafilidis/Fedora-Post-Install-Guide/blob/main/assets/diagnostics_before.png?raw=true">
      <br>
      <strong>Before</strong>
    </td>
    <td align="center">
      <img src="https://github.com/georgestafilidis/Fedora-Post-Install-Guide/blob/main/assets/diagnostics_after.png?raw=true">
      <br>
      <strong>After</strong>
    </td>
  </tr>
</table>

## Small Boot Speed Tweak

If your laptop or PC takes a long time to boot, you can disable this service.  
It makes NetworkManager wait for full internet connection before finishing boot.

Note: this reduces some privacy (especially if you use a VPN), because the system might start sending packets before the VPN is active.

Run this command:

```bash
sudo systemctl disable NetworkManager-wait-online.service
```

## Recommended Apps

After cleaning up, here are some apps I install right away on a fresh Fedora Workstation.  
All of these are available on **Flathub**. They are sandboxed, update easily, and do not mess with your system packages.

First, enable Flathub if you have not done it yet (one-time step):

```bash
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

Now open the **Software** app and search for these.

- **Extension Manager**  
The easiest way to browse, install and manage GNOME extensions. Must-have if you want to customize the desktop.

- **Flatseal**  
Lets you see and change permissions for every Flatpak app. Great for privacy. You can block access to files, camera, microphone, etc.

- **Gradia**  
Lets you see and change permissions for every Flatpak app. Great for privacy. You can block access to files, camera, microphone, etc.

- **Bottles**  
Run Windows software/games easily.

- **Resources**  
Shows CPU, RAM, disk, network usage and running processes/apps in a clean, modern interface. It uses GNOME's libadwaita design so it feels right at home on Fedora Workstation. Much nicer than the default GNOME System Monitor for quick checks.

- **Packet**  
Send and receive files wirelessly with Android devices (using Quick Share) or with another device that has Packet installed. It needs Bluetooth enabled and both devices on the same Wi-Fi network with mDNS support. Super simple for sharing photos, PDFs or videos without USB or email.

- **PeaZip**
Free, open-source archive manager. Handles 200+ formats (7Z, RAR, ZIP, TAR, Brotli, Zstd, etc.).

## Recommended GNOME Extensions

GNOME is clean and beautiful by default, but extensions make it much more powerful and comfortable.  
I only recommend a few that are stable, actively maintained, and do not cause issues on updates.

Install **Extension Manager** first (from the Recommended Apps section).

- **AppIndicator and KStatusNotifierItem Support**
- **Alphabetical App Grid**
- **Night Theme Switcher**
- **Tinted Shell**
- **Just Perfection**
- **Vitals**
- **Hot Edge**

> [!IMPORTANT]
> If something suddenly breaks on your system **always disable all extensions first** as the culprit.   
> If the problem goes away, re-enable extensions one by one to find which one is causing it. Most issues are extension-related and get fixed quickly by the developers.


You can always turn it back on later with:
```
sudo systemctl enable NetworkManager-wait-online.service
```

## Got Ideas?

This is my personal take on a clean and useful Fedora Workstation setup.  
If you notice other pre-installed things that feel unnecessary, better apps/extensions I missed, or tweaks that work great for you let me know!
