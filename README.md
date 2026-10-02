# Jiggle or Physics — Windows Static Recompilation

**An unofficial Windows x64 static recompilation of the US PlayStation 1 release of _Dead or Alive_, created by 3DGE.** Version **0.1.0** · Experimental.

> Jiggle or Physics translates the game's MIPS executable into generated C, then compiles it into a native Windows program linked to the PSXRecomp runtime. This is a recompilation project: it is not a conventional emulator front-end or a traditional hand-written decompilation.

## GitHub description

> 3DGE's unofficial Windows x64 static recompilation of Dead or Alive (US, SLUS-00606), with 16:9 presentation, configurable controls, portable memory-card saves, and optional PS1 runtime enhancements.

Suggested repository topics: `psx`, `playstation-1`, `static-recompilation`, `windows`, `fighting-game`, `psxrecomp`.

## Project overview

Jiggle or Physics is a title-specific Windows static recompilation of the US _Dead or Alive_ release. The translated game executable is compiled for Windows; PSXRecomp supplies runtime services for graphics, sound, disc I/O, timing, controllers, and memory cards. The bundled launcher puts display, controller, save, and mod settings in one place.

The current build targets one specific game release: the US NTSC-U disc with serial **SLUS-00606**. Other regions, revisions, and track layouts have not been validated by this project.

OpenBIOS is included to provide console firmware services; game data still comes from the matching disc image selected by the player. The most precise technical label is static recompilation. The presence of OpenBIOS alone does not determine whether the broader phrase “PC port” applies, and this is not a clean-room source-code port.

## Highlights

### Display and rendering

- **16:9 at 1920×1080 by default**, with selectable 4:3, 16:9, and 21:9 presentation modes and adjustable window size.
- **2× supersampling** and bilinear texture filtering are the packaged defaults.
- The OpenGL renderer is the default. A software renderer is included as a compatibility option.
- Widescreen presentation includes UI and sprite correction settings to reduce stretching.
- Frame interpolation can present at the display's refresh rate. It does **not** unlock or speed up the game's original simulation timing.

### Controls and saves

- Keyboard and SDL game-controller input, with per-player device and binding configuration in the launcher.
- Two local player slots are exposed by this release. _Dead or Alive_ uses digital directional input; the release maps the controller's left stick to those directions while retaining configurable D-pad and button inputs.
- Two virtual memory-card slots use `.mcd` files. The portable layout stores saves below `saves/` next to the executable.
- Settings, controller bindings, memory cards, mods, and runtime assets live beside the executable. Put the disc image in `disc/` as well when you want to move the whole setup as one portable folder.

### Optional runtime enhancements

The Mods page includes framework packages that are **off by default**:

| Package | What it does |
| --- | --- |
| PGXP Precision | Optional subpixel geometry and perspective-correct texture support; visible coverage depends on the compiled hooks and game paths. |
| Fast Loading | Changes host pacing during detected loads. It can make loading periods pass faster, but changes wall-clock pacing. |
| CD Speed | Delivers emulated CD sectors sooner. Large multipliers can expose timing issues; FMV and CD audio keep their normal timing. |
| Bezel Artwork | Places a user-selected image in the margins around the game image. |
| 8 MB Main RAM | Hidden advanced mode for patches that specifically require expanded memory; stock settings remain unchanged. |

These are PSXRecomp features, not a set of Dead or Alive-specific cheats. The current catalog contains no 3DGE-authored costume, character-physics, or gameplay-mod package.

### Startup and tribute

The Windows release can play `assets/ps1_boot.mp4` before game runtime startup. After the clip, a black memorial card fades in and stays visible for 12 seconds. **Esc**, **Space**, **Enter**, or a mouse click skips the screen currently playing.

The card reads:

> In honor and loving memory of the gigachad Tomonobu Itagaki. Jiggle or Physics is an unofficial Windows static recompilation of Dead or Alive for PS1. Created with and runs off of LEGAL physically owned game copies.

That on-screen wording is a tribute, not a license or a determination of anyone's rights.

## What this build does not include

- No ray tracing, DLSS, or new physically based lighting pass.
- No HD texture pack or new character models.
- No change to the game's character animation, jiggle/physics simulation, or gameplay rules.
- No unlocked gameplay simulation frame rate; interpolation only affects presentation.
- No online play in this Windows build.
- No verified support for non-US discs or other revisions.

The widescreen and filtering options improve how the original assets are displayed; they do not replace those assets with newly authored high-resolution art.

## Quick start

1. Download the latest **Jiggle or Physics Windows** ZIP from Releases and extract the entire archive to a writable folder.
2. Run `Jiggle or Physics.exe`.
3. Select a compatible image of your own US disc when prompted. A CUE sheet is recommended; keep every BIN track named by the CUE beside it.
4. Configure display and controller options in the launcher, then choose **Play**.

For a fully portable disc setup, put the CUE and all of its referenced BIN tracks in `disc/`. The default relative path is `disc/Dead or Alive.cue`. If the image is stored elsewhere, the launcher remembers that path; reselect it if you move the image to another location.

The game disc is not included. This build uses the bundled OpenBIOS backend by default, so a retail BIOS dump is not normally required.

## Supported disc

| Field | Supported target |
| --- | --- |
| Region | USA / NTSC-U |
| Serial | `SLUS-00606` |
| CUE track count | 29 |
| Data track size | 153,251,616 bytes |
| Data track SHA-1 | `8f76598e9c35bd725a08dc6c750221c19d518f3b` |

The runtime checks disc identity. A differently mastered disc, a different region, or a revision with a different executable may be rejected or may need its own recompiler configuration and build.

## Configuration and files

| File or folder | Purpose |
| --- | --- |
| `settings.toml` | Display, audio, launcher, controller, BIOS, and selected-disc preferences. Written by the launcher. |
| `game.toml` | Title identity and default runtime/display configuration. |
| `input.ini` | SDL controller button and axis mappings. |
| `keybinds.ini` | Keyboard-to-controller bindings. |
| `disc/` | Optional portable location for the CUE and its referenced tracks. |
| `saves/` | Virtual memory-card files. |
| `mods/bundled/` | Framework packages shipped with the build. |
| `mods/installed/` | Player-installed `.psxmod` packages managed by the launcher. |
| `assets/ps1_boot.mp4` | Startup clip used by the Windows release. At runtime, omit this file beside the executable to skip the clip and show the memorial card directly. The current source packaging step expects a clip asset when rebuilding. |

Open `Open Launcher.cmd` if the launcher is skipped at startup and you need to change settings.

## Building from source

The repository uses `psxrecomp` and `recomp-ui` as Git submodules. Clone with submodules enabled, then configure a CMake build with a Windows C/C++ toolchain, Ninja, SDL3 development files, and OpenGL available:

```powershell
git clone --recurse-submodules <repository-url>
cd <repository-folder>
cmake -S . -B build-release -G Ninja -DCMAKE_BUILD_TYPE=Release
cmake --build build-release --target psx-runtime
```

The project targets CMake 3.20 or newer. A Windows resource compiler is needed for the executable icon and version information. The Windows startup video uses Media Foundation, which is part of Windows.

The checked-in `generated/` sources are the title-specific recompiler output used by the normal build. Regenerating game code is a separate reverse-engineering workflow using the matching US release and PSXRecomp's code-generation tools; do not attach disc images, BIOS images, or copyrighted game files to issues or pull requests.

## Project status and reporting issues

This is an experimental **0.1.0** build for one Windows x64 target and one US disc revision. Report issues with the port version, Windows version, display mode, selected renderer, controller type, and steps to reproduce. Do not attach game images, BIOS dumps, memory-card saves, or other copyrighted game material.

## Credits

- **Static recompilation, launcher, package integration:** 3DGE.
- **Tribute:** Tomonobu Itagaki.
- **Recompilation framework and launcher UI:** [PSXRecomp](https://github.com/RetroPortingToolKit/psxrecomp) and [recomp-ui](https://github.com/RetroPortingToolKit/recomp-ui); see their respective licenses and notices.
- _Dead or Alive_, its characters, audio, artwork, and other original game content belong to their respective rights holders.

## Licensing and asset notice

The PSXRecomp runtime is provided under the included **PolyForm Noncommercial License 1.0.0**. `recomp-ui`, Dear ImGui, OpenBIOS, and other components carry their own licenses and notices; review each before redistributing. This repository does not currently declare one blanket license for every project file.

The commercial game disc is not included, but the repository/build contains game-specific recompiled code. The supplied startup movie and cover-art executable icon are separate assets; the PSXRecomp license does not grant rights to those materials or to the original game. Owning a physical copy does not by itself grant permission to redistribute the executable, generated game code, cover art, or video. Confirm the rights for each item before making a public source or binary release, and remove or replace any asset you are not authorized to distribute.

Jiggle or Physics is an unofficial fan project and is not affiliated with or endorsed by the original game's publishers or platform makers. All trademarks and copyrights remain with their respective owners.

