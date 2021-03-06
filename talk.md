# Understanding Desktop Environments

## Intro
- A fresh install of most distros will result in a graphical interface
- All the pieces that make up this experience are part of the desktop environment (DE)
- Also known as a desktop, shell or session
- Popular DEs include GNOME, KDE, Cinnamon, MATE, XFCE, etc.
- Today we'll discuss
  - What a DE is
  - How the pieces fit together
  - The old X11 windowing system
  - The present and future of Wayland (its replacement)
- Raise your hand at any point--there are not stupid questions

## About me
- William Wold
- Often found in Fredericksburg VA
- Works for Canonical, the company behind Ubuntu
  - Works on Mir, an up-and-coming Wayland compositor library
- Get in touch:
  - Email: wm@wmww.sh
  - Twitter: [@__wmww](https://twitter.com/__wmww)
  - Mastodon: [@wmww@cybre.space](https://cybre.space/@wmww)
- __TODO: Link to talk notes and include QR code__

## Desktop Environment (DE)
- The visual components that make up the core desktop experience
- Not a single piece of software, rather a collection of programs that work well together
- Generally includes:
  - Core applications (file manager, system monitor, etc)
  - Settings manager
  - Panels (contains clock, open windows, Wifi menu, etc)
  - App launcher/Menu
  - Notifications
  - Desktop background (including icons)
  - Window manager (more on that below)
  - Lock screen
- A DE does __not__ include:
  - Command line programs (ls, vim)
  - More specialized apps (Firefox, Blender)
  - The kernel

## Window Manager
- A window manager handles the logic for positioning windows
- It may or may not provide the "window decorations" (bar at the top including the title and the close button)
- Floating window manager
  - The most common type (all major Linux desktops as well as Windows and Mac)
  - Allows the user to drag window wherever they like
  - Often includes features like "snapping" a window to half the screen
- Tiling window manager
  - Places windows in non-overlapping tiles
  - Generally considered more difficult to learn how to use, but some find them more productive
  - Often largely keyboard driven
  - Examples include i3, Sway, Awesome, bspwm
- Can you use a window manager without its associated desktop environment?
  - Yes
  - Many window manger projects are not event part of a DE (Openbox, i3, etc)
  - In this case, the user is expected to assemble whatever desktop environment they want out of existing pieces
  - You can also switch out a DEs window manger
  - These sort of setups are more customizable, but generally less polished

## Display Manager
- What you see after booting up or logging out
- Allows you to select a user to log in as
- Allows you to select which installed DE to run
- Can be turned off, in which case you'll boot directly to a TTY terminal
- There are several, and their names usually end with DM (GDM, LXDM, etc)

## TTY
- A TTY is like a full screen terminal
- There are usually about 7 TTYs available
- You can switch between them with Ctrl+Alt+F1/F2/F3/etc
- It can be useful to jump into a TTY when your graphical system is mucked up or frozen
- If your desktop environment is running, it's on one of the TTY slots
- If you switch away and want to get it back, try all of them until you find it (it's often on 1, 2 or 7)
- If there is no display manager enabled, Linux will boot directly to TTY 1
- You can run a DE from a TTY without a display manager, though it may take some configuration

## Display Server
- The Linux kernel allows a program to draw to the display, but only one program at a time
- If you want to run multiple apps side-by-side, you need a single display server to sit between all your apps and the kernel
- The display server is a "server" in the sense that it waits for apps to start up and serves their requests relating to opening windows
- The display server also receives keyboard and mouse input from the kernel, and relays those input events on to apps
- If you're using X11 (as most desktops currently do), the display server you're running is Xorg
- There are multiple Wayland display servers

## Display Server Protocol
- A __protocol__ is a simple, well defined "language" two pieces of software use to communicate
- A display server protocol allows apps to open windows and get input events
- For example, the following "conversation" might take place via a such a protocol:
  - App: `open a new window`
  - App: `make the window 640 pixels by 480 pixels`
  - App: `set the window's title to "My App"`
  - App: `fill the window with these pixels: XXX…`
  - Display Server: `the user moved the mouse pointer to (67, 180)`
  - Display Server: `the user clicked the left mouse button`
  - App: `fill the window with these pixels: YYY…`

## X11
- The standard display server protocol since 1987
- Some of the design decisions have aged well. Others… not so much
- Not great in terms of security (nothing preventing key loggers or screen grabbers)
- As mentioned, Xorg is the main display server that implements X11

## Wayland
- A more modern, efficient and secure protocol
- In Wayland, the window manager is built into the display server
- Wayland does not have a standard display server like Xorg is for X11
- Instead, desktop environments are expected to provide a server to meet their unique needs
- The core Wayland protocol is very basic, but servers can support extensions

## Wayland Compositor
- Display servers that speak Wayland are known as __Wayland compositors__
- GNOME and KDE have turned their window managers into Wayland compositors
- Projects that don't want to maintain an entire display server can use libraries such as wlroots and __Mir__ (← that's the thing I work on)

## Apps and Toolkits
- Apps that work on X11 must be adapted to also work on Wayland
- Instead of using X11 directly, most apps are built with "toolkits" such as GTK or Qt
- In these cases, only the toolkits need to be ported to Wayland (and the major ones already have been)
- Most Wayland compositors support XWayland, which is used by legacy apps that only speak X
- XWayland apps are a bit more buggy than native Wayland apps, but good enough

## DEs on Wayland
- Some Wayland compositors incorporate many components of the DE into the compositor
- In GNOME, for example, panels, notifications and the wallpaper are all built into the compositor
- Other projects have opted to keep the architecture similar to how it was in X, with each component being a separate program
- The Mir team believes keeping components separated is more reliable and flexible
  - A single buggy component can't crash the whole compositor
  - Components can be used in a desktop environment they weren't built for
- Protocol extensions are needed to make this possible, so such desktop components won't work on compositors that don't support the extensions they use
