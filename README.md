# hyprpaper

Hyprpaper is a blazing fast wallpaper utility for Hyprland with the ability to dynamically change wallpapers through sockets. It will work on all wlroots-based compositors, though.

# Features
 - Per-output wallpapers
 - fill or contain modes
 - fractional scaling support
 - IPC for blazing fast wallpaper switches
 - preloading targets into memory

# Installation

[AUR](https://aur.archlinux.org/packages/hyprpaper-git): `yay -S hyprpaper-git`

### Manual:
```
git clone https://github.com/hyprwm/hyprpaper
cd hyprpaper
make all
```
*the output binary will be in `./build/`, copy it to your PATH, e.g. `/usr/bin`*

# Usage

Hyprpaper is controlled by the config, like this:

*~/.config/hypr/hyprpaper.conf*
```
preload = /path/to/image.png

# .. more preloads

wallpaper = monitor,/path/to/image.png

# .. more monitors
```

Preload will tell Hyprland to load a particular image (supported formats: png, jpg, jpeg). Wallpaper will apply the wallpaper to the selected output (`monitor` is the monitor's name, easily can be retrieved with `hyprctl monitors`. You can leave it empty for a wildcard (aka fallback))

You may add `contain:` before the file path in `wallpaper=` to set the mode to contain instead of cover:

```
wallpaper = monitor,contain:/path/to/image.jpg
```

A Wallpaper ***cannot*** be applied without preloading. The config is ***not*** reloaded dynamically.

## Important note to the inner workings
Preload does exactly what it says. It loads the entire wallpaper into memory. This can result in around 8 - 20MB of mem usage. It is not recommended to preload every wallpaper you have, as it will be a) taking a couple seconds at the beginning to load and b) take 100s of MBs of disk and RAM usage.

Preload is meant only for situations in which you want a wallpaper to switch INSTANTLY when you issue a wallpaper keyword (e.g. wallpaper per workspace)

In any and all cases when you don't mind waiting 300ms for the wallpaper to change, consider making a script that:
 - preloads the new wallpaper
 - sets the new wallpaper
 - unloads the old wallpaper (to free memory)

# IPC
You can use `hyprctl hyprpaper` (if on Hyprland) to issue a keyword, for example
```
hyprctl hyprpaper wallpaper DP-1,~/Pictures/myepicpng.png
```

# Battery life
Since the IPC has to tick every now and then, and poll in the background, battery life might be a tiny bit worse with IPC on. If you want to fully disable it, use
```
ipc = off
```
in the config.

## Unloading
If you use a lot of wallpapers, consider unloading those that you no longer need. This will mean you need to load them again if you wish to use them for a second time, but will free the memory used by the preloaded bitmap. (Usually 8 - 20MB, depending on the resolution)

You can issue a `hyprctl hyprpaper unload [PATH]` to do that.

You can also issue a `hyprctl hyprpaper unload all` to unload all inactive wallpapers.

<br/>

For other compositors, the socket works like socket1 of Hyprland, and is located in `/tmp/hypr/.hyprpaper.sock` (this path only when Hyprland is not running!)
