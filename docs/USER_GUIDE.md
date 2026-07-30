# PvZ Debugger User Guide

This guide describes the packaged release of PvZ Debugger. It covers
the native Windows trainer, the experimental Flash integration, Nintendo DS
identification, research tools, safety rules, known limitations, and planned
work.

> [!WARNING]
> Back up the active PvZ user profile before changing progress, unlocks,
> inventory-related values, or experimental game modes. Memory edits can crash
> the game, and a game can save an unusual value after the trainer disconnects.

## 1. What PvZ Debugger is

PvZ Debugger is an offline trainer and research interface for selected builds
of the original *Plants vs. Zombies*. The Windows trainer identifies the
executable and enables only the tools declared by the matching build profile.

The release also includes:

- Experimental support for one identified PopCap web-demo SWF.
- Ruffle and Flashpoint process discovery.
- Nintendo DS ROM and emulator identification scaffolding.
- Compatibility planning for future Nintendo DS, Xenia, and RPCS3 support.

It is not an online cheat, a game launcher, an emulator, or a replacement for
the game. It does not download or distribute PvZ files.

## 2. Support levels

The words used in this project have specific meanings:

| Level | Meaning |
| --- | --- |
| Extended native | Exact Windows profile with the general structures plus validated internal calls, patch groups, generator, and advanced live tools. |
| Basic native | Recognized Windows executable with the common data structures and only the build-specific calls or patches actually present in its profile. |
| Partial beta | Pre-release build with useful support, but incomplete game content and additional uncertainty around discarded features. |
| Experimental | The implementation exists, but runtime behavior has not been tested broadly enough to make a compatibility guarantee. |
| Identity only | The artifact can be identified, but no live gameplay writes are enabled. |
| Planned | Architecture or documentation exists, but there is no working backend. |

No support label means that every feature is guaranteed. The Compatibility tab
is authoritative for the executable currently connected.

The exact 0.2.0 boundary for Quick Play, advanced editors, projectiles, live
combat, betas, Flash, and future platforms is tracked in
[PvZ Debugger 0.2.0 Feature Test Status](FEATURE_TEST_STATUS_0.2.0.md).

## 3. Requirements and launch

### Packaged executable

The release artifact is intended to be:

```text
PvZDebugger.exe
```

Start PvZ first, wait until the playable game process exists, and then open the
trainer. The application prevents a second trainer instance from opening.

Administrator rights are normally unnecessary. If the game was explicitly
started as administrator, Windows may require the trainer to run at the same
integrity level before memory access is allowed.

## 4. Connecting to a game

1. Start a supported Windows build and wait for its actual gameplay process.
2. Open PvZ Debugger.
3. Press **Search** if the process list is empty or stale.
4. Select the PvZ process.
5. Press **Connect**.
6. Check the detected version and capability status before changing values.

Most supported releases run the game in `popcapgame1.exe`. In the Steam GOTY
release, `PlantsVsZombies.exe` may only be a DRM launcher; the trainer must
attach to the later `popcapgame1.exe` process.

The trainer verifies the PE timestamp and a known code fingerprint. An unknown,
modified, packed, or unofficial executable is rejected instead of receiving
guessed offsets.

## 5. Interface and navigation

The UI is available in English and Spanish. The language button changes the
interface without restarting and stores the preference in:

```text
%LOCALAPPDATA%\PvZDebugger\settings.json
```

The window starts maximized. Use **F11** for fullscreen and **Escape** to leave
fullscreen. The main page scrolls when the window is smaller than its content.
When a combo box is open, the mouse wheel is reserved for that list and should
not move the page behind it.

### Home

The Home tab contains progress and setup tools:

- Read or prepare an Adventure level.
- Enter integer test levels up to 100, including zero and negative values.
- Complete the active level when the build has a validated completion call.
- Edit profile money.
- Unlock Adventure progress, modes, shop-related progress, and GOTY
  achievements where those profile fields exist.
- Show or hide the Limbo page using a reversible build-specific patch.
- Change or remove Crazy Dave's forced seed choices.
- Replace active seed packets.
- Capture, save, apply, and delete seed-deck presets.

Preparing a level from the main menu may require entering Adventure afterward.
Changing a level while a board is active depends on the exact game routine
available in that build.

### Gameplay

The Gameplay tab contains values and rules used during a board:

- Set sun.
- Toggle free planting.
- Toggle plant or zombie immunity on profiles with verified patch bytes.
- Complete a level, skip a wave, remove current zombies, or stop new spawns.
- Enable automatic coin and item collection.
- Keep the rake available.
- prevent new Doom-shroom craters.
- Start, remove, or restore lawnmowers.
- Apply other reversible build-specific patches, such as instant recharge,
  awake mushrooms, frozen zombies, or visible vases.
- Place a plant directly at a chosen row and column. This bypasses Lily Pad,
  Flower Pot, and terrain checks, so a Peashooter can be created in a pool row
  without a support plant. Unusual combinations can have visual or behavioral
  side effects because the game was not designed to place every plant on every
  terrain.
- Run one-shot board actions to heal plants/zombies, clear projectiles, remove
  current plants, hypnotize current zombies, clear temporary zombie effects,
  or ready compatible projectile plants. These actions do not remain active
  for entities created later.

On the validated beta profile, the projectile source list includes the beta
plant IDs for Iceberg Lettuce, Stinger, and Cycler. Selecting them applies the
same multi-source row filter as the final plants; the IDs are never offered on
final builds. Enabling **Awake mushrooms** also performs one pass over the
current board and wakes existing mushroom objects. The patch remains active
for newly created mushrooms according to that profile.

The same tab also contains **Combat and reuse** for extended Windows profiles:

- **One-hit projectiles**: each newly observed live projectile marks one active
  zombie in the same row as dying.
- **One-hit bites**: only zombies whose native `mIsEating` flag is active can
  instantly remove one plant in their row.
- **Reusable instant plants**: Cherry Bomb, Potato Mine, Doom-shroom, Squash,
  and Jalapeño are restored after being seen alive and completing their special
  action. The mode does not resurrect an unrelated dead slot.

These modes run live while the board is active and are intentionally reversible.
They are gameplay approximations rather than a replacement for the game's
damage routine: a projectile is associated by row, not by a hidden owner
pointer. Disable them before changing level or mode if a board is transitioning.

Patch names may vary by build. A disabled control means that the connected
profile does not contain the required original bytes and patch signature.

### Laboratory

The Laboratory groups the live board editors.

#### Speed and scene

- Change simulation speed from 0.25x through 10x.
- Change day, night, pool, fog, and roof scene variants on supported builds.
- Switch among the stock music tracks or stop music. Music calls are currently
  validated only for GOTY Steam 1.2.0.1096 and are signature-checked before
  execution.
- Remove and restore fog through the build-specific reversible patch.
- Hide and restore the seed bank/sun display, shovel, menu button, and wave
  progress independently. These are live-board presentation controls, not
  permanent profile settings.

Live scene changes can leave temporary visual artifacts. Use them on an empty
board when possible. HUD controls are restored when the trainer disconnects
only if the same board and trainer-owned hidden value still exist; changing
levels first can naturally destroy that old board.

#### Waves

- Read the current wave list.
- Change zombie entries in each wave.
- Clear or regenerate entries.
- Choose a normal, rush, immediate, or huge-wave launch style.
- Apply changes to the active board.
- Read and set the current Endless Survival round/flag where supported.

Live changes affect the board state and upcoming spawn list. Zombies that have
already spawned are not retroactively converted. Some operations therefore
offer a separate clear-current-zombies action.

#### Generator

On the four profiles with validated internal calls, the generator can create:

- Plants.
- Supported zombies.
- Graves and other declared grid objects.

The requested row and column are validated before the internal game function
is called. Some special zombies require game-owned auxiliary objects or
initialization paths; unsupported IDs are blocked because calling the generic
constructor for them can cause an access violation.

#### Projectiles

The projectile editor can:

- Change existing projectile types.
- Keep converting newly created projectiles.
- Target several selected plant types or use a general rule.
- Map native catapult projectiles to Cabbage-pult, Kernel-pult, Melon-pult, and
  Winter Melon sources where the build exposes the required plant/projectile
  fields.
- Edit validated position, velocity, vertical acceleration, motion mode, age,
  and row fields.
- Change plant attack cadence with a safe minimum of 30 ticks.
- Maintain a cadence override and restore the original values later.

The game does not store an owner pointer on every projectile in a way currently
validated by this project. For ordinary shooters, selected-plant filtering may
use the projectile row and nearby live plants as an approximation. Catapult
mapping is more exact because the native projectile type identifies its source
family.

Conversion waits until the game has completed the projectile constructor,
retries a newly allocated slot, and resets validated visual rotation fields
before committing an ordinary new type. The persistent override checks often
enough to cover every projectile in multi-shot animations such as Repeater and
Gatling Pea.

Fire Pea uses the game's validated `ConvertToFireball` routine so its looping
reanimation is created, attached, and mirrored for backward shots. Cob receives
a complete lob state and an automatic target farther ahead in the same row.
Those two conversions are available only in exact builds whose profile declares
the internal call or complete projectile fields. Other converted projectiles
keep their original trajectory and collision context. This area remains
experimental.

#### Editor and inspector

The inspector lists active plants, zombies, and projectiles using the
build-specific structure size and live/dead flag. Selecting a plant or zombie
opens an advanced property panel and an Almanac-style preview. The preview uses
a real PNG layer from the connected installation when one is available; it is
not a reconstructed animation and falls back to an ID/name card.

Only checked properties are written. **Selected entity only** changes one live
object; **All of the same type** applies the same checked values to every live
object that had the selected entity's original type. The backend revalidates
every address, snapshots all bytes, verifies every write, and rolls back the
whole operation on failure.

Original 1.0.0.1051 EN and GOTY Steam 1.2.0.1096 currently declare the complete
plant/zombie fields used by this editor. Other native Windows builds retain the
safe list and basic type editor. They never inherit a layout merely because
their structures look similar. Flash does not use this editor.

### Editors

The Editors tab reports the real implementation state of larger editors.
Currently usable pieces include deck presets, advanced build-gated entity
editing, the Tree of Wisdom tools described below, and selected Endless fields.

#### Tree of Wisdom

The height editor is enabled only for GOTY Steam 1.2.0.1096, whose exact
PlayerInfo record has been measured. Reading or applying a height validates a
range from 0 through 1,000,000; applying it snapshots the old dword and restores
it if verification fails. If the Zen Garden was already open, leave and reopen
it so PvZ rebuilds the screen.

**Export all dialogue** is independent from height editing. It reads
`properties/LawnStrings.txt` or the same entry from the installation's
validated, read-only `main.pak`, then exports the numbered Tree of Wisdom
messages as JSON. It includes the build identity, source path, discovered cheat
words, and heights explicitly stated in the text. Availability means the
installation has an extractable resource; it does not claim that every regional
translation has been manually reviewed.

The following larger editors are incomplete or blocked:

- Zen Garden plants, growth, position, and supplies.
- Shop and inventory quantities.
- Per-seed sun cost, recharge, and dynamic slot count.
- Full Endless formation serialization.
- Vasebreaker, I, Zombie, Wall-nut Bowling, conveyor, and lane controllers.

Rows marked **Blocked**, **Partial**, or **Pending profile** are status reports,
not buttons that silently use guessed offsets.

### Research

Research tools include:

- **Restored Quick Play (experimental):** available only for the profiled GOTY
  Steam 1.2.0.1096 build. Activate it from the PvZ main menu to open the
  preserved Quick Play page, its three category clouds, and the reconstructed
  Adventure selector. The trainer attempts to restore temporary profile values,
  but it cannot guarantee that the save or session will remain exactly as it
  was before activation. Back up the profile first. After using the in-game
  Back button, **Return to Quick Play** reopens the page from the main menu
  without reinstalling the native payload.

- A reversible `GameMode` explorer for IDs from -1 through 100, with an
  assisted sweep ("◀ ID -1" / "ID +1 ▶") for screens the game never links to
  from any menu, such as the Mini-games/Survival/Puzzles selector some
  releases keep in their assets without exposing a button for it. Watch the
  game after each step; a confirmation dialog gates every change unless
  "Confirmar cada ID" is turned off for a long sweep, which does not make a
  hidden ID any less likely to return to the menu or crash, only faster to
  try.
- Menu bookmarks for the Mini-games, Puzzles, and Survival selector screens:
  no profile validates a call or field that opens them directly, so the
  trainer cannot jump there on its own. Once the sweep above (or ordinary
  navigation, for a screen that does have a menu button) lands on the right
  ID, press "Guardar aquí" to record it, and "Ir" reapplies it afterward
  through the same explorer. Bookmarks are stored per exact build; one
  captured on Original is never offered on GOTY. The Puzzles tab has a
  matching shortcut so it is not only a placeholder.
- An evidence-limited Beta-versus-Final ID report exported as Markdown.
- Status tracking for discarded plants, experimental zombies, Crazy Dave
  scripts, and Squirrel reconstruction.

Hidden game modes can return to the menu or crash. Restore the previous mode
before normal play; the same warning applies to a bookmarked menu the first
time it is reapplied.

### Beta

Beta-only controls are gated by exact executable identity:

- The dog and bee/propeller zombies are only offered for Bloom and Doom
  0.1.1.1014.
- Squirrel mode is available for the two beta profiles that retain relevant
  evidence.
- Final builds never receive beta IDs because their numeric positions happen
  to overlap.

In Beta 0.9.9.1029, Squirrel uses `GameMode` 49. Bloom and Doom retains
Squirrel artwork, but its complete mode behavior is less certain. The current
implementation forces the mode; counters, nut contents, rewards, and an
in-place restart are not reconstructed.

### Compatibility

The Compatibility tab shows:

- Platform and detected edition.
- Executable or artifact identity.
- Available capabilities.
- Missing capabilities and their reason.
- Profile and hash information when available.

Use this tab as the source of truth. Edition names such as “GOTY” are never
enough to enable a patch.

### Technical

Technical tools include:

- The managed patch list.
- Enable, disable, and restore actions.
- A manual memory inspector.

Managed patches check the expected original bytes before writing and restore
only changes owned by the current trainer instance. The manual inspector is a
research tool; arbitrary addresses can corrupt the process and are not covered
by the profile safety model.

## 6. Native Windows build coverage

The complete recognized-build table, distribution information, and feature
depth are maintained in the [compatibility matrix](COMPATIBILITY.md).

Extended native tools are currently declared for:

- Bloom and Doom Beta 0.1.1.1014 EN.
- Plants vs. Zombies Beta 0.9.9.1029 EN.
- Original 1.0.0.1051 EN.
- GOTY 1.2.0.1096 Steam EN.

The other recognized native profiles contain the common structures and their
declared per-build calls/patches, but should not be described as feature-equal
to those four.

## 7. Experimental Flash support

Flash discovery is intentionally limited to desktop Ruffle and Flashpoint.
Generic web browsers are not listed.

The packaged release recognizes one exact profile for the PopCap AS3 web demo:

```text
plants-vs-zombies.swf
resources.swf
data.xml
```

The files and their hashes must match the profile. The demo is not the native
PC GOTY game and does not contain all final plants, modes, Almanac, Zen Garden,
or internal Windows functions.

The demo declares 21 internal keyboard cheats. They are confirmed to exist in
the SWF, but in-game coverage remains insufficiently tested. Depending on the
player and exact files, the available
actions include level stepping, next wave, free planting, mode unlock, seed
refresh, awards, eight zombie spawns, pause/step/resume, speed changes, and a
debug display.

Use the Flash tab in the packaged application to detect the supported
Flashpoint or Ruffle player and apply the available demo actions. Restart the
player after changing its configuration. Flash edits create a backup before
the first change and can be restored from the same tab.

### ActionScript bridge

An experimental local ActionScript bridge may be available for an instrumented,
profile-verified movie. An original SWF has no client for this bridge.
Exact value reads and writes require an instrumented SWF or a compatible
player modification, followed by a new profile for the resulting SHA-256.

The existing `ExternalInterface` callbacks belong to the advertisement wrapper
and do not expose level, sun, or waves. Flash support is therefore not 100%
compatible and must not be presented as equivalent to the native backend.

## 8. Nintendo DS and future emulators

Current Nintendo DS support can discover melonDS, DeSmuME, and BizHawk, inspect
a local ROM header, validate its header CRC, identify known regional product
codes, and calculate SHA-256.

It is identity only. There is no live memory adapter and no hash-specific
write profile, so level, sun, entities, waves, and save editing remain disabled.

Xenia/Xbox 360 and RPCS3/PlayStation 3 are planned only. Future support requires
guest addresses, PowerPC endianness, title/update identity, executable hashes,
and emulator-specific memory adapters. Native PC addresses are not reusable.

## 9. Known issues and insufficiently tested areas

- The ten basic native profiles have substantially less runtime coverage than
  the four extended profiles.
- Combat one-hit and reusable-instant-plant modes use the common PC entity
  layout and have not been stress-tested with every special zombie, boss, or
  minigame.
- One-hit projectile association is row-based; a projectile can affect the
  first eligible zombie in its row before the normal collision animation.
- One-hit bites depend on the game's `mIsEating` flag and do not cover
  drive-over, vault, ladder, bungee, or other non-chew attacks.
- Flash keyboard cheats are statically verified but not broadly tested across
  Ruffle and Flashpoint versions.
- Windows 7, 8, and 8.1 are packaging targets, not continuously tested hosts.
- Modified executables, cracks, repacks, fan patches, and localization patches
  may fail identity checks or have incompatible code despite a familiar
  version label.
- Live scene switching can cause visual artifacts.
- Projectile conversion can preserve initialization state from the original
  projectile.
- Multi-plant projectile ownership is approximate for projectile families that
  do not identify their source plant.
- A 30-tick attack interval is the enforced safety minimum. Faster values are
  intentionally blocked because very low counters can stop firing or
  destabilize game behavior.
- Generic spawning of special zombies is blocked when their constructor needs
  unverified auxiliary state.
- Wave edits do not transform entities that are already alive.
- Endless live regeneration is profile-gated and does not reproduce every
  possible internal setup path of every mode.
- Squirrel is a partial mode loader, not a finished reconstruction.
- Advanced Zen Garden, shop, seed-stat, entity-state, and minigame editors are
  not implemented.
- Nintendo DS has no live writes.
- Xenia and RPCS3 backends are not implemented.

## 10. Troubleshooting

### The game is open but the trainer says it is closed

- Press **Search** again after the main game process appears.
- Select `popcapgame1.exe`, not a Steam DRM launcher.
- Reconnect after the game changes process during startup.
- Run the trainer at the same Windows integrity level as the game.
- Check whether antivirus or endpoint security denied process access.

### The version is not detected

The executable did not match an exact known profile. Do not copy offsets from a
different version. Record the file SHA-256, PE timestamp, language,
distribution, and whether the executable is unmodified before researching a
new profile.

### A control is disabled

Open Compatibility and read the missing-capability reason. The profile may
support basic values but lack the internal function or original patch bytes
required by that control.

### A tool says to enter a match

Board values such as sun, waves, entities, and projectiles do not exist at the
main menu. Enter a level and retry.

### The game crashed

- Close the trainer.
- Restart the game before reconnecting.
- Restore the save backup if the game saved unwanted progress.
- Record the exact build signature, selected tool, value, game mode, and
  whether entities were already active.
- Do not retry an unsupported entity ID through the manual inspector.

## 11. Reporting a compatibility result

Useful reports include:

- PvZ edition, language, store/distribution, and displayed version.
- Executable filename and SHA-256.
- PE `TimeDateStamp`.
- Windows version and the release executable used.
- Exact feature, entered values, game mode, scene, and current wave.
- Whether the result is reproducible after a clean game restart.
- A screenshot and the trainer log, with personal paths removed if desired.

Do not attach proprietary game executables, ROMs, SWFs, or game resources to a
bug report.

## 12. Safety and restoration

- Keep a save backup.
- Prefer managed controls over the manual inspector.
- Disable persistent projectile, cadence, immunity, and patch toggles before
  disconnecting.
- Use **Restore all applied by the trainer** for managed patches.
- For Flash configuration, use the backup and restore controls in the Flash
  tab.
- Restart the game after a crash; do not assume partially written process state
  is safe.

The trainer is intended for offline research, testing, and video production.
It is not affiliated with or endorsed by PopCap Games or Electronic Arts.
