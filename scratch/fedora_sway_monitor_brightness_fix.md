## Background
>I needed a way to control my external desktop monitor's brightness directly from my Sway Fedora Spin , using Fn keys (F1/F2). Because I am on a desktop rather than a laptop `brightnessctl` or `light` won't work because they rely on laptop-specific kernel backlight drivers, this is the default that comes with the Fedora Sway spin so I can't rely on it. Instead, desktop monitors require the DDC/CI protocol to change physical hardware settings over display cables (HDMI/DisplayPort) (urgh).

>Here is the complete walkthrough of how I solved this, including the specific adjustments needed for Fedora's permission model.

### 1. Install ddcutil
First, I installed `ddcutil`, which handles the DDC/CI protocol communication with external displays on Linux:
```bash
sudo dnf install ddcutil
```

### 2. Load the Kernel Module & Configure Permissions
Fedora does not use an `i2c` group like Arch or Debian; instead, it relies on udev rules and the `uaccess` tag to grant the active user session rights to the display buses. 

I loaded the `i2c-dev` module immediately and set it up to load automatically on every boot:
```bash
sudo modprobe i2c-dev #this activates it temporarily but does not persist on reboots.
echo "i2c-dev" | sudo tee /etc/modules-load.d/i2c.conf
```

If running `ddcutil detect` as a normal user throws a permission error, It can be fixed by creating a custom udev rule that explicitly triggers `uaccess` for my graphics layer I2C buses:
```bash
echo 'SUBSYSTEM=="i2c-dev", KERNEL=="i2c-[0-9]*", ATTRS{class}=="0x03*", TAG+="uaccess"' | sudo tee /etc/udev/rules.d/60-ddcutil-i2c.rules
sudo udevadm control --reload-rules && sudo udevadm trigger
```

### 3. Detect and Test the Monitor
I ran a hardware detection test to make sure the computer could communicate with the display:
```bash
ddcutil detect
```
Once detected, I verified I could manually manipulate the panel's physical brightness. Feature code `10` is the universal hex standard for monitor brightness controls:
```bash
ddcutil setvcp 10 + 10
```

### 4. Set Up the Keybindings in Sway
Because the `Fn` key is a hardware-level modifier handled by my keyboard's internal controller, it doesn't send a standalone "Fn key" signal to Wayland. I checked what it outputs using the `wev` tool in a terminal. 

If pressing `Fn + F1` outputs a standard `F1` keystroke, I can bind it directly. However, to prevent overriding hardwired multimedia keys or creating layout conflicts, pairing it with the system modifier key (`$mod`, usually the Windows key) is the cleanest route.

I added these lines to my `~/.config/sway/config` file to handle the adjustments:
```text
# Use Mod + F1 and Mod + F2 to lower and raise external monitor brightness
bindsym --locked \$mod+F1 exec ddcutil setvcp 10 - 10
bindsym --locked \$mod+F2 exec ddcutil setvcp 10 + 10
```
*Note: The `--locked` flag ensures Sway passes these commands through even if the screen locker is active.*

Finally, I updated the environment to register the new hotkeys without starting a new session:
```bash
swaymsg reload
# alternatively just use your reload hotkey which is probably just mod-shift-c
```

