# Razer Laptop Control - Revived

A Linux userspace application to control Razer Blade laptops. No kernel modules (DKMS) required!

**Now with Blade 2025 support!**

> **DISCLAIMER:** This is a highly experimental build. No support is provided. I may try to help if I can, but my knowledge is limited. Use at your own risk!
>
> Fun fact: This tool actually gives you MORE control over your Razer laptop than Synapse 4 does. Ironic, isn't it?

## Supported Features

- Fan speed control (auto/manual RPM)
- Power mode control (Balanced/Gaming/Creator/Silent/Custom)
- CPU/GPU boost settings
- Logo LED control
- Keyboard brightness
- Battery Health Optimizer (BHO) - limit charge to extend battery lifespan
- RGB keyboard effects (experimental)

## Supported Devices

| Model | Year | USB PID | Status |
|-------|------|---------|--------|
| Blade 15 | 2016-2022 | Various | Tested |
| Blade 14 | 2021-2024 | Various | Tested |
| Blade 16 | 2023 | 029F | Tested |
| Blade 17 | 2022 | 028B | Tested |
| Blade Pro | 2017-2021 | Various | Tested |
| Blade Stealth | 2017-2020 | Various | Tested |
| Razer Book 13 | 2020 | 026A | Tested |
| **Blade 2025** | 2025 | **02c6** | **NEW!** |

To check if your laptop is supported, run:
```bash
lsusb | grep -i razer
# Look for: Bus XXX Device XXX: ID 1532:XXXX Razer USA, Ltd
# The XXXX after 1532: is your device's PID
```

## Dependencies

### Fedora/RHEL
```bash
sudo dnf install -y rust cargo dbus-devel libusb1-devel hidapi-devel pkgconf systemd-devel gtk3-devel git
```

### Ubuntu/Debian
```bash
sudo apt install -y rustc cargo libdbus-1-dev libusb-1.0-0-dev libhidapi-dev pkg-config libsystemd-dev libgtk-3-dev git
```

### Arch Linux
```bash
sudo pacman -S rust cargo dbus libusb hidapi pkgconf systemd gtk3 git
```

## Installation

```bash
# Clone the repository
git clone https://github.com/encomjp/razercontrol-revived.git
cd razercontrol-revived/razer_control_gui

# Install
./install.sh install
```

After installation, **log out and back in** (or reboot) for udev rules to take effect.

## Usage

### Command Line Interface

```bash
# Read current settings (use 'ac' for plugged in, 'bat' for battery)
razer-cli read fan ac
razer-cli read power ac
razer-cli read brightness ac
razer-cli read logo ac
razer-cli read bho

# Set fan speed (0 = auto, or specify RPM like 3500)
razer-cli write fan ac 0        # Auto
razer-cli write fan ac 4000     # 4000 RPM

# Set power mode (0=Balanced, 1=Gaming, 2=Creator, 3=Silent, 4=Custom)
razer-cli write power ac 1 0 0  # Gaming mode

# Set brightness (0-100)
razer-cli write brightness ac 75

# Set logo LED (0=Off, 1=On, 2=Breathing)
razer-cli write logo ac 1

# Battery Health Optimizer
razer-cli write bho on 80       # Limit charge to 80%
razer-cli write bho off
```

### GUI Application

```bash
razer-settings
```

### Service Management

```bash
# Check service status
systemctl --user status razercontrol

# Restart service
systemctl --user restart razercontrol

# View logs
journalctl --user -u razercontrol -f
```

## Troubleshooting

### "no supported device found"

Your laptop's USB PID might not be in the device list. Check your PID:
```bash
lsusb | grep -i razer
```

Then add it to `/usr/share/razercontrol/laptops.json` and restart the service.

### "Permission denied" on hidraw

The udev rules might not have been applied. Try:
```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

Then log out and back in.

### Socket doesn't exist

If you have another razer service installed (like from a previous installation), it might conflict:
```bash
# Check for conflicting services
systemctl list-units | grep -i razer

# Disable any system-level razer service
sudo systemctl stop razer-service
sudo systemctl disable razer-service
```

## Uninstallation

```bash
cd razer_control_gui
./install.sh uninstall
```

## Adding Support for New Devices

1. Find your device's USB PID: `lsusb | grep -i razer`
2. Edit `data/devices/laptops.json` and add your device
3. Edit `data/udev/99-hidraw-permissions.rules` and add your PID
4. Reinstall: `./install.sh install`
5. Submit a PR!

Example device entry:
```json
{
    "name": "Blade XX 20XX",
    "vid": "1532",
    "pid": "XXXX",
    "features": ["logo", "boost", "bho"],
    "fan": [2200, 5000]
}
```

## Warning

This software is provided AS-IS with NO WARRANTY. This is an experimental community project.

- I am NOT affiliated with Razer
- I am NOT responsible for any damage to your hardware
- No official support is provided - I'll try to help but no guarantees
- If something breaks, you get to keep both pieces

That said, it works great on my Blade 2025 and gives me more control than Synapse 4 ever did on Windows.

## Credits

- Original project: [Razer-Linux/razer-laptop-control-no-dkms](https://github.com/Razer-Linux/razer-laptop-control-no-dkms)
- Blade 2025 support added by [@encomjp](https://github.com/encomjp)

## License

GPL-2.0 - See LICENSE file
