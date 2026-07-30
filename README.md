# PvZ Debugger

PvZ Debugger is a closed, local trainer for selected original, GOTY, regional,
and beta Windows releases of *Plants vs. Zombies*. It detects a profiled build
and exposes video-friendly controls without requiring manual offset searches.
The main focus is the Laboratory, where speed, waves, entities, and projectiles
can be inspected or changed for supported builds.

It is intended for offline testing, research, and video production. It is not
affiliated with PopCap Games or Electronic Arts.

![Release](https://img.shields.io/github/v/release/vesant14-lang/PVZDebugger?include_prereleases)
![Platform](https://img.shields.io/badge/platform-Windows-blue)
![Status](https://img.shields.io/badge/status-alpha-orange)
![Source](https://img.shields.io/badge/source-closed-red)

## Download

Download the latest packaged Windows build from the
[GitHub Releases page](../../releases/latest). The release ZIP contains the
trainer executable and user documentation; source code is not included.

Current distribution: pre-release / first Windows build.

Requirements: Windows 7 or newer, a supported 32-bit PvZ Windows executable,
and a saved-game backup. Windows 7, Windows 8, and Windows 8.1 may require
additional troubleshooting.

The application supports English and Spanish. Some experimental diagnostics may
remain untranslated. A few antivirus products may flag a trainer because it
opens a game process and reads or writes its memory. Verify the download hash
from the release page and use only a release obtained from this project.

## Quick start

1. Back up your PvZ save.
2. Start a recognized version of the game.
3. Open `PvZDebugger.exe`.
4. Confirm the detected build in the Compatibility tab.
5. Enter a board before using live gameplay tools.
6. Test experimental actions one at a time and reconnect after a crash or
   version change.

Some cheats update the saved or internal game state immediately but do not
refresh the currently visible menu. Return to the previous screen, reopen the
affected menu, or restart the current level when a change does not appear at
once. Some edited values may also be applied internally without being reflected
immediately by the visible entity, animation, counter, or menu state. This does
not necessarily mean that the action failed.

The trainer disconnects automatically when the attached process closes. Window
size, position, maximized/fullscreen state, active tab, and language are saved
in `%LOCALAPPDATA%\PvZDebugger\settings.json`.

## Compatibility principles

> A recognized executable is not a guarantee that every feature works on every
> computer, distribution, language, or game state.

- Only currently profiled builds receive native Windows memory features.
- Capabilities are enabled independently; unsupported controls stay disabled.
- Unknown or modified builds may be rejected rather than assigned guessed
  offsets.
- Beta-only IDs are never enabled in final releases by name matching alone.
- Flash/SWF support is experimental and is not guaranteed for every Ruffle,
  Flashpoint, browser-hosted movie, or historical web release.
- Nintendo DS currently provides identification only. Xbox 360/Xenia and
  PlayStation 3/RPCS3 support is future work.

## Main features

| Area | Highlights | Status |
| --- | --- | --- |
| **Laboratory** | Speed, scenes, live waves, wave editor, wave generator, plant/zombie generator, projectile editor, and entity inspector | **Featured; build-dependent** |
| Home | Adventure level, complete level, unlocks, Limbo, Crazy Dave, seed packets, money, and deck presets | Available where verified |
| Gameplay | Sun, free planting, immunities, instant recharge, mushrooms, fog, craters, collection, lawnmowers, and zombie controls | Available where verified |
| Beta | Beta plants, zombies, projectiles, Limbo, Squirrel, and discarded content | Partial and version-gated |
| Editors | Decks, entity types, Endless formations, Zen Garden, shop, and minigame editors | Mixed: available, partial, or planned |
| Flash | Flashpoint/Ruffle detection, demo cheats, SWF identity, and bridge status | Experimental |
| Nintendo DS | ROM and emulator detection | Identification only |

### Laboratory focus

The Laboratory is the advanced workspace for videos and controlled testing:

- Change speed and scene while observing the current game state.
- Read, clear, edit, launch, and configure waves.
- Generate plants, zombies, graves, rakes, brains, and other supported items.
- Convert current or newly created projectiles, including multi-plant filters,
  cadence, motion, physics, and deletion tools.
- Inspect active plants, zombies, and projectiles and change validated IDs.

Projectile, combat, beta, and live-wave tools are experimental and can behave
differently between builds.

## Platform status

| Platform | Status |
| --- | --- |
| Native Windows PC | Main supported platform for currently profiled builds |
| Flash/SWF | Experimental and build-dependent |
| Nintendo DS | ROM/emulator identification only; live support planned |
| Xbox 360 / Xenia | Planned |
| PlayStation 3 / RPCS3 | Planned |

This project supports known profiled official Windows releases, including
Original, GOTY, regional editions, and selected betas. It does not claim that
every historical PC revision is supported. See the compatibility document for
the exact list.

## Documentation

- [User guide](docs/USER_GUIDE.md) — tabs, controls, workflows, and practical
  examples.
- [Compatibility matrix](docs/COMPATIBILITY.md) — profiled PC builds, Windows
  targets, and feature depth.
- [Flash support](docs/FLASH_SUPPORT.md) — Flashpoint, Ruffle, demos, SWF
  detection, and bridge limitations.
- [Beta research](docs/BETA_RESEARCH.md) — discarded entities, Squirrel,
  Limbo, and version-gated content.
- [Known issues](docs/KNOWN_ISSUES.md) — current bugs, limitations, and report
  checklist.
- [Safety model](docs/SAFETY_MODEL.md) — backups, patches, process handling,
  and recovery behavior.
- [Roadmap](docs/ROADMAP.md) — planned improvements and future platforms.
- [Playable title-screen concept](docs/PLAYABLE_TITLE_SCREEN_CONCEPT.md) — a
  standalone presentation-screen idea, not a memory patch.

## Safety

Back up the save before every experiment. Memory writes can crash the game or
leave a test state until the game is restarted. Use one action at a time, avoid
writing during transitions, and disconnect before changing the executable or
switching versions.

The trainer is for offline use. Do not use it with online services, competitive
play, or files you do not own or have permission to test.

## Distribution and credits

PvZ Debugger is currently distributed as a closed, non-open-source build. The
executable and implementation are not licensed for copying, modification,
redistribution, reverse engineering, or incorporation into another project
without permission from the project owner.

*Plants vs. Zombies*, its names, artwork, audio, code, and game assets belong to
PopCap Games and Electronic Arts. This project does not include or license
those assets and is not endorsed by either company.
