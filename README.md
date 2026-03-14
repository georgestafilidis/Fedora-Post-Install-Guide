# Fedora Workstation Post Install Guide

A simple, step-by-step post-installation guide tailored for beginners on Fedora Workstation. It walks you through essential updates, codec setup, optional cleanups to streamline your system, performance tweaks, and recommendations for must-have apps and GNOME extensions to make your desktop experience smooth, efficient, and personalized.

## Update the System First (Recommended)

Fedora keeps your system secure and stable with frequent updates. While you don't need to update every single day, it’s a good idea to update regularly.

Since this is a fresh installation, we’ll start by updating the system.

### Using the Software App (Easiest Method)

1. Open **Software**
2. Go to the **Updates** tab
3. Click **Download**
4. Click **Restart & Install**

### Alternatively, use the Terminal

1. Open **Terminal**
2. Run the following command:

```bash
sudo dnf upgrade --refresh
```

3. Enter your password when prompted

> [!NOTE]
> GNOME Software uses offline updates by default. Updates are downloaded first and then applied during the next reboot, which is the safest method. When updating from the terminal, changes are applied immediately (a live update), which is also fine for most cases.

## Multimedia Codecs

Fedora does not include some common video/audio codecs by default. The easiest and most reliable fix is to enable the trusted **RPM Fusion** repositories and install full codec support.

### Enable RPM Fusion Repositories

```bash
sudo dnf install \
  https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
  https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

```bash
sudo dnf swap ffmpeg-free ffmpeg --allowerasing
sudo dnf group install multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
```

After running these, test playback in Firefox (open a YouTube video or any MP4 file).

## Optional Cleanup

These steps remove some pre-installed items that many users do not need. You can find most of these items in **Software**.

### Input Methods

Fedora includes input methods for typing in many languages.
If you only use one language (e.g., English) and do not need extra input tools, you can safely remove them.

Run this command:

```bash
sudo dnf remove ibus-m17n ibus-typing-booster ibus-hangul ibus-libpinyin ibus-anthy
```

> [!NOTE]
> You can find these Input Sources in the Explore tab of the Software app.
> Open Software and scroll down until you see Input Sources. If you don't see the category, make sure "Only show verified developers" is disabled in the app preferences.

<img src="https://github.com/georgestafilidis/Fedora-Post-Install-Guide/blob/main/assets/input_sources.gif?raw=true" alt="Input Sources in GNOME Software">

### Unnecessary Apps

I remove these apps because I find them rarely used, outdated, or there are better alternatives available. Additionally, some of them feel like they don't belong on a PC or laptop.

As a beginner, review the list below and decide which ones you might want to keep (for example, if you need a weather app or a virtual machine manager). If you want to keep some, you can either:

Remove them individually by running sudo dnf remove <app-name> (replacing <app-name> with the specific app, like sudo dnf remove gnome-weather).
Or, copy the full command below and edit it to exclude the apps you want to keep (just delete their names from the list).

| App                     | Description                       |
| ----------------------- | --------------------------------- |
| **gnome-tour**          | Welcome tour                      |
| **gnome-color-manager** | Color profile manager             |
| **gnome-connections**   | Remote desktop client             |
| **gnome-contacts**      | Contact manager                   |
| **gnome-weather**       | Weather app                       |
| **gnome-abrt**          | Graphical crash reporter frontend |
| **gnome-boxes**         | Virtual machine manager           |
| **simple-scan**         | Document scanner                  |
| **snapshot**            | Camera app                        |

Run the following command to remove them all at once (or edit it as mentioned above):

```bash
sudo dnf remove gnome-tour gnome-color-manager gnome-connections gnome-contacts gnome-weather gnome-abrt gnome-boxes simple-scan snapshot
```

### System Services

`gnome-abrt` is the graphical crash reporter (already removed in the **Unnecessary Apps** section if you followed that step).  
`abrt` is the background service (Automatic Bug Reporting Tool) that collects crash data.

If you do not plan to report crashes to Fedora developers, you can safely remove it. This saves a little space and removes one unnecessary background process:

```bash
sudo dnf remove abrt
```

> [!NOTE]
> Removing abrt also hides the Diagnostics entry in Settings -> Privacy & Security -> System.
If you reinstall abrt later and the entry does not appear, start the service manually:

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

> [!NOTE]
> This reduces some privacy (especially if you use a VPN), because the system might start sending packets before the VPN is active.

Run this command:

```bash
sudo systemctl disable NetworkManager-wait-online.service
```

You can always turn it back on later with:

```
sudo systemctl enable NetworkManager-wait-online.service
```

### Recommended Apps

After cleaning up, here are some apps I install right away on a fresh Fedora Workstation.

All of these are available on Flathub. They are sandboxed, update easily, and do not mess with your system packages.

> [!NOTE]
> Fedora includes default Flatpak remotes (fedora, fedora-testing, and a filtered flathub) that provide a limited selection of apps. If you want a cleaner setup focused on the full, unfiltered Flathub (with more apps available), you can optionally remove them first:

```bash
flatpak remote-delete fedora
flatpak remote-delete fedora-testing
flatpak remote-delete flathub
```

Then, re-add the unfiltered Flathub with the command above.

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
