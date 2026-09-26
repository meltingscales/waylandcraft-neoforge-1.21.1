![waylandcraft banner](/assets/title_scaled.png)

Wayland Compositor in Minecraft

> **This is an unofficial fork** of [EVV1E/waylandcraft](https://github.com/EVV1E/waylandcraft), ported to
> **NeoForge 1.21.1**, and with **read-only window sharing**: other players can watch and hear a window
> you share, e.g. to watch YouTube videos together or show a terminal.
> Most of this fork's work was done with major help from an LLM (Claude Code).
> Upstream targets Fabric on Minecraft 26.1.2 and is available on [Modrinth](https://modrinth.com/mod/waylandcraft);
> please report issues with this fork here, not upstream.
>
> **Download:** [releases](https://github.com/meltingscales/waylandcraft-neoforge-1.21.1/releases)
> (latest: [v2.1.0-neoforge-1.21.1-sharing-alpha](https://github.com/meltingscales/waylandcraft-neoforge-1.21.1/releases/tag/v2.1.0-neoforge-1.21.1-sharing-alpha)).
> Branches: `window-sharing` (default, the port plus sharing) and `neoforge-1.21.1` (the port only).

[Demo video](https://youtu.be/cTkEM7b0IQw) (upstream)

## System dependencies
- OS: Linux (x86_64, glibc 2.34+ for the release builds)
- Minecraft 1.21.1
- NeoForge 21.1.251 or newer
- xkbcommon library 1.11.0
- xkbcommon tools (xkbcli)
- xwayland-satellite (for Xwayland support)
- For sharing window audio: PipeWire (`pw-record`, `pw-dump`), and `xprop` for X11 apps

Only watching other players' shared windows works on any OS; everything else needs Linux.

Additionally recommended:
- Prism Launcher
- Sodium

## Important notes for installing / using!!!
1. Do not use a Minecraft launcher packaged as a flatpak! You won't be able to use your apps.
2. For nvidia: Set the `__GL_THREADED_OPTIMIZATIONS` environment variable to `0` in your launcher.
3. The Zink OpenGL driver has been known to cause issues. Use native OpenGL instead.
4. For GPU-accelerated (dmabuf) windows, set `earlyWindowControl = false` in `config/fml.toml`. NeoForge's early
   loading window otherwise creates a non-EGL OpenGL context, and windows fall back to slower shared-memory buffers
   (the log says so when this happens).

## Frequently Asked Questions
### How do I use this thing?
Download the mod from the [releases](https://github.com/meltingscales/waylandcraft-neoforge-1.21.1/releases), install NeoForge for Minecraft 1.21.1 and drag the jar file in your mods folder.
Look at your keybind settings. By default `V` opens the app launcher, `G` enables keyboard capture allowing you to type in
the windows, `B` opens the window manager screen.

### How can I press Escape in the windows?
Instead of using `G` to capture the keyboard, use `ALT+Q` instead. The only way to turn it off is to press `ALT-Q` again,
so the `ESC` key is forwarded to the application.

### How do I run X11 apps?
Since v2.0.0 waylandcraft has integrated support for [xwayland-satellite](https://github.com/Supreeeme/xwayland-satellite).
If you have the binary installed on your system, it should automatically be started.

### How to do the relative mouse movement thing for 3D games?
Move your mouse over the window, then activate the hard keyboard capture mode. (`ALT-Q`)
Exiting the hard keyboard capture mode releases the mouse.

### Can other players see my windows? (window sharing)
In this fork, yes, if you choose to share them. Sharing is read-only: others can watch and hear, but can't interact.
1. Open the window manager (`B`), select the window's tab and click the **share** (broadcast) button. Click it again to stop.
   `N` toggles sharing of the most recently focused window as a shortcut.
2. Put the window's item in an item frame, or place the window in the world as usual. Other players see and hear it there.

Shared windows get a red ● in the window manager's tabs, and the HUD lists everything you share.
Video is only sent while someone can actually see the window. Audio (the shared app's own PipeWire stream, never
your microphone) plays in stereo from the window to players within 24 blocks. The server needs this mod for sharing.

To try it alone, run `/waylandcraft testpattern` (needs cheats): it spawns a test window with color bars and a beep
every second (the border flashes with each beep, and beeps alternate left/right). `/waylandcraft testpattern stop`
removes it. Design notes and known limitations are in [TODO-SHARING.md](TODO-SHARING.md).

### But can I use it on a server though?
You can. Unless you share a window, other players won't see your windows, and nobody else can interact with them.
Servers can opt to install the mod, which will allow players to use the window items for themselves, and to share windows.
If the server doesn't support it, you can spawn a window in the world by going into the wm screen (default bind `B`)
and then pressing and holding the "Grab" button.

### Does this work in VR?
Depending on your VR mod, you can probably get the windows to display fine but you probably won't be able to interact with
the windows using your controller. Soooo, kinda.

### Does this work with shaders?
Since v2.0.2 Iris shaders are supported (in this fork: Iris 1.8.14 for NeoForge 1.21.1, tested with Sodium 0.8.13).

There are a couple of downsides though: The windows might have large borders and text in windows will be harder to read from a distance (because window anti-aliasing doesn't work)

This is because for the shader support windows are rendered with the same pipeline as entities because otherwise the shaders would ignore them.

For some shaders you might need to disable features like Temporal Anti Aliasing (TAA).

## Building and Running
You need a Rust development environment, a Java 21 SDK, and for the native library: `libxkbcommon`, `x264`
(library and headers), `clang`/`libclang` (for bindgen) and `pkg-config`.
```sh
./build.sh #all arguments are passed to cargo build
```

The final jar file will be in `build/libs`, or run `./gradlew runClient` for a development environment
(`./gradlew runClient2` starts a second client as player `Viewer`, and `./gradlew runServer` a dedicated server,
for testing sharing).

Local builds link the system `libx264` dynamically. Release builds link a static, position-independent x264
instead, so players don't need a matching `libx264` (see `.github/workflows/build.yml`: build x264 with
`--enable-static --enable-pic`, then build the native library with `SYSTEM_DEPS_X264_LINK=static` and
`PKG_CONFIG_PATH` pointing at it). The release jars are built on Ubuntu 22.04 for glibc compatibility.

The port's history and decisions are in [TODO-PORT-NEOFORGE-1.21.1.md](TODO-PORT-NEOFORGE-1.21.1.md).


## Images
![screenshot](/assets/screenshot.png)

## Disclaimer
This compositor still has lots of issues and bugs. Use it at your own risk or whatever.
Window sharing is an alpha: only share windows you're comfortable showing to everyone near them.

## License
GPLv3 (see `LICENSE`). Release builds bundle x264 (GPLv2+), JCodec (BSD) and Concentus (BSD-style, an Opus port).

## Contribution Policy
All contributions have to be made an accordance with the GPLv3 license (see `LICENSE`).
Waylandcraft has some important policy around LLMs and generative AI, mostly because of code and contribution quality as well as some ethical and copyright concerns.
Mergeable contributions made to the repository in the form of pull requests need to be made **without major usage** of LLMs.

If you feel as though you have something worthwhile to contribute which was made using LLMs **please disclose it** and file it as a **draft** pull request instead.
It will probably have to be more closely examined or even entirely rewritten by a human programmer, which can then be (re-)submitted as a normal pull request.
