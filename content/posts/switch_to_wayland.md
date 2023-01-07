+++
title = "Switch to Wayland"
author = ["Zarkli Leonardo"]
description = "Switch to Wayland"
date = 2022-12-22T00:00:00+08:00
tags = ["wayland", "rice"]
categories = ["rice"]
draft = false
+++

## Installation {#installation}

I choose [hyprland](https://github.com/hyprwm/Hyprland) as my Wayland window manager(and compositor) which is based on `wlroots`, it is a really new compositor with a lot of great features, and also it is pretty stable(according to the office). Here is the _stars over time_ charts for it:

{{< figure src="https://starchart.cc/hyprwm/Hyprland.svg" caption="<span class=\"figure-number\">Figure 1: </span>Hyprland Stars Over Time" >}}

```bash
# glfw-wayland is conflict with glfw-x11
paru -S hyprland-git xorg-xwayland xorg-xlsclients qt5-wayland glfw-wayland qt6-wayland
paru -S dunst wireplumber pipewire polkit-kde-agent xdg-desktop-portal-hyprland-git # must-have (from hyprland-wiki)

paru -S wev # xev
paru -S wl-clipboard # xclip
paru -S wtype # xdotool
```


### applications {#applications}

```bash
paru -S hyprpaper # wallpaper manager under hyprland
paru -S rofi-lbonn-wayland-git # rofi under Wayland
paru -S waybar-hyprland-git # waybar hyprland (enhanced?)
paru -S hyprpick # color picker
paru -S grim slurp swappy # screenshot (swappy is a GUI annotation client for grim+slurp combination, which is enough for screenshot)
paru -S gammastep # screen gamma, alternative for redshift
```


## Emacs Under Wayland {#emacs-under-wayland}

Please use Emacs with `pgtk` (pure GTK) build to support Wayland. The 29 version of Emacs officially support build with `pgtk` flag=. You can simply get it from AUR:

```Bash
paru -S emacs29-git
```


## Customization {#customization}

1.  cursor:
    1.  <https://wiki.hyprland.org/FAQ/index.html#how-do-i-change-me-mouse-cursor>
    2.  <https://github.com/swaywm/sway/wiki/GTK-3-settings-on-Wayland>


## Problems {#problems}

1.  `nm-applet` not appears on Waybar tray (at least for me), so I use `nmtui` in terminal to manage my network connection instead.
2.  `megasync` not working properly
3.  `jetbrains-toolbox` not appears on the Waybar tray.


## Links: {#links}


### Applications {#applications}

1.  <https://arewewaylandyet.com/>
2.  <https://wiki.hyprland.org/Useful-Utilities/>
3.  <https://github.com/swaywm/sway/wiki/Useful-add-ons-for-sway>


### Guides {#guides}

1.  [Full Wayland Setup on Arch Linux](https://www.fosskers.ca/en/blog/wayland)
