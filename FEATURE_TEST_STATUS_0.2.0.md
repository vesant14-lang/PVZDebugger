# PvZ Debugger 0.2.0 Feature Test Status

This document records features that exist in the 0.2.0 alpha but are not
equally researched, enabled, or live-tested on every supported build.

Recognition of an executable means that PvZ Debugger can select its profile.
It does **not** mean that every feature has full runtime coverage on that
executable. A feature is enabled only when the exact profile declares the
required fields, calls, or original patch bytes.

## Status labels

| Label | Meaning |
| --- | --- |
| Profiled | The executable identity and required data are declared. |
| Extended | The profile contains additional internal calls or measured object layouts. |
| Experimental | The implementation exists, but live coverage is not broad enough for a compatibility guarantee. |
| Partial | Only part of the intended workflow has been reconstructed. |
| Identity only | The game or artifact can be recognized, but no live modification is available. |
| Planned | No user-facing implementation is claimed in 0.2.0. |

Automated tests use synthetic process memory and verify offsets, validation,
transactions, rollback, and generated x86 call stubs. They reduce regression
risk but do not replace a live test on every regional executable, game mode,
Windows release, and hardware configuration.

## Native Windows coverage groups

### Extended research profiles

- Bloom and Doom Beta 0.1.1.1014 EN.
- Plants vs. Zombies Beta 0.9.9.1029 EN.
- Original 1.0.0.1051 EN.
- GOTY Steam 1.2.0.1096 EN.

These builds expose more laboratory features than the other ten native
profiles. They are not feature-equal: beta-only content, Quick Play, advanced
entity layouts, Tree of Wisdom data, and some internal calls remain
build-specific.

### Basic final-PC profiles

- Original 1.2.0.1065 EN.
- Original 1.0.4.7924 ES.
- Original 1.0.7.3556 ES.
- Original 1.0.7.3467 RU.
- GOTY 1.2.0.1073 EN.
- GOTY 1.2.0.1093 DE/ES/FR/IT.
- GOTY 1.1.0.1056 JA.
- GOTY 1.1.0.1056 ZH, 2010 release.
- GOTY 1.1.0.1056 ZH, 2012-06 release.
- GOTY 1.1.0.1056 ZH, 2012-07 release.

These profiles retain common profile, level, resource, progress, inspector,
basic projectile, and other declared controls. They must not borrow internal
calls or complete entity layouts from an extended profile merely because the
game looks similar.

## Features with incomplete cross-build coverage

| Feature | 0.2.0 status | Enabled or investigated builds | Current boundary |
| --- | --- | --- | --- |
| Restored Quick Play | Experimental, single build | GOTY Steam 1.2.0.1096 only | Other GOTY, Original, and beta executables have not been investigated deeply enough to identify equivalent objects, vtables, hooks, navigation, and rollback. Absence of support does not prove that their binaries lack the preserved page. |
| Quick Play save/session restoration | Best effort | GOTY Steam 1.2.0.1096 | Temporary Adventure progress and runtime state are restored when possible, but an interrupted process, save, or transition cannot be guaranteed to return to its exact previous state. |
| Live Unlock Everything refresh | Build-specific | Saved profile updates: all 14 native profiles. Immediate open-menu refresh: GOTY Steam 1.2.0.1096 only. | Other builds need the main menu to be rebuilt normally because their selector cache layout is not measured. |
| Advanced plant and zombie editor | Partial, experimental | Original 1.0.0.1051 and GOTY Steam 1.2.0.1096 | The current editor can inspect entities and apply a validated subset of changes, but it is not a complete editor yet. Several properties still require research into the game's state machines, constructors, attachments, animations, and update routines before they can be exposed safely or reflected visibly. Other builds retain only the safe inspector and their gated subset. |
| Visual plant/zombie reconstruction | Experimental | Original 1.0.0.1051 and GOTY Steam 1.2.0.1096 | Type, stock helmet, stock shield, and size changes use the game's constructors so the model is visible. Bosses and special constructors remain blocked where auxiliary state is unknown. Stock PvZ has no constructor that combines a helmet and shield on one zombie. |
| Basic projectile replacement | Experimental broad coverage | All 14 profiled native builds | The type field is profile-gated, but every regional executable has not received equivalent live stress testing. |
| Advanced projectile ownership, physics, Fire Pea/Cob initialization, and plant-specific cadence | Experimental | Original 1.0.0.1051 and GOTY Steam 1.2.0.1096 | Other profiles lack one or more measured structure fields or internal calls. Multi-shot, multi-row, catapult, Cob Cannon, and fire behavior should still be treated as sensitive. |
| One-hit projectiles, one-hit bites, and reusable instant plants | Experimental | Twelve final-PC profiles; disabled for both betas and Flash | The final profiles declare the common combat fields, but only representative builds have synthetic and live coverage. Bosses, special attacks, and unusual minigames are not exhaustively tested. |
| Live wave composition and launch | Experimental | The two betas, Original 1.0.0.1051, and GOTY Steam 1.2.0.1096 | Transactions and rollback are verified, but every special mode and transition path is not. Existing zombies are not transformed by a future-wave edit. |
| Live Endless Survival flag regeneration | Experimental | The two betas, Original 1.0.0.1051, and GOTY Steam 1.2.0.1096 | Regeneration validates the generated table, but it does not reproduce every internal setup path used by every Survival variant. |
| Entity generator and live scene change | Experimental | The two betas, Original 1.0.0.1051, and GOTY Steam 1.2.0.1096 | Internal calling conventions are not copied to the ten basic profiles. Special zombies remain blocked unless their constructor case is explicitly validated. Scene changes can leave visual artifacts. |
| Direct plant placement without terrain supports | Experimental | Builds with a validated plant constructor: the two betas, Original 1.0.0.1051, and GOTY Steam 1.2.0.1096 | Bypasses placement validation for a single constructor call. Unsupported terrain combinations may animate or target incorrectly; it does not install a global `CanPlantAt` patch. |
| Music switching | Experimental, single build | GOTY Steam 1.2.0.1096 | Both Music routine entry signatures are checked immediately before use. Other builds remain disabled until their calling convention and signatures are measured independently. |
| Fog and HUD layers | Experimental, profile-gated | Fog uses each profile's declared patch. Independent seed bank, shovel, menu button, and progress controls are currently measured for GOTY Steam 1.2.0.1096. | HUD values belong to the active Board and may disappear naturally on a level transition. Restoration refuses to overwrite a value changed by the game or another tool. |
| Advanced one-shot board actions | Experimental | Original 1.0.0.1051 and GOTY Steam 1.2.0.1096 where the complete Plant/Zombie fields are declared | Removal, hypnosis, temporary-state clearing, and shot readiness affect only current entities. Special plants may ignore a generic launch counter, and no continuous rule is installed. |
| Grid-item generator | Partial | Original 1.0.0.1051 and GOTY Steam 1.2.0.1096 | Gravestones use a validated call. Safe constructors for brains, rakes, and other board objects are not yet declared. |
| Squirrel minigame | Partial research | Beta 0.1.1.1014 and Beta 0.9.9.1029 | The mode can be forced and beta resources are gated correctly. Counters, nut contents, rewards, restart, and complete original rules are not reconstructed. The 0.9.9 build currently has the strongest evidence. |
| Dog and bee/propeller zombies | Experimental beta content | Bloom and Doom Beta 0.1.1.1014 only | Their IDs are not reused on other builds. Behavior, animation state, and interaction coverage remain incomplete. |
| GameMode explorer and menu bookmarks | Experimental research tool | Profile-gated native builds | IDs are not portable between builds. A discovered screen may return to the menu, load an incomplete mode, or crash. Bookmarks only reapply a value already captured on the same exact build. |
| Crazy Dave controls | Partial | Seed-choice controls are profile-gated across native builds | Full dialogue replacement, reward control, scripted scene skipping, and complete forced-packet reconstruction still require additional internal functions. |
| Tree of Wisdom height | Experimental, single build | GOTY Steam 1.2.0.1096 | Other builds do not declare the measured record. The dialogue exporter is separate and depends on a readable, validated `main.pak` or `LawnStrings.txt`. |
| Tree of Wisdom dialogue export | Read-only, resource-dependent | Installations with a validated extractable text resource | Export availability does not mean every regional translation or every inferred height has been manually reviewed. |
| Managed code patches, immunity, mowers, rake, auto-collect, and no-craters | Per-profile experimental | Only builds declaring exact original and replacement bytes | A feature present on one executable is not assumed safe on another. Runtime interaction with every minigame has not been exhaustively tested. |
| Flash integration | Experimental, separate backend | One exact PopCap AS3 web-demo profile through supported Ruffle/Flashpoint workflows | The SWF does not contain the complete native-PC feature set. Keyboard cheats are statically confirmed but not broadly tested across player versions. Exact value editing requires an instrumented SWF and ActionScript bridge. |
| Nintendo DS | Identity only | Recognized regional ROM headers in supported emulator discovery | No live memory adapter, hash-specific write profile, or gameplay editor is enabled in 0.2.0. |
| Xbox 360/Xenia and PlayStation 3/RPCS3 | Planned | None | Guest-memory mapping, title/update identity, endianness handling, and safe emulator adapters remain future work. |

## Incomplete editors in 0.2.0

The following areas may have design notes, placeholders, basic controls, or
read-only research, but are not complete cross-build editors:

- Zen Garden plants, growth stages, positions, supplies, and chocolate.
- Shop purchases and inventory quantities.
- Per-seed sun cost, recharge, Imitater state, and dynamic slot count.
- Full Endless Survival formation serialization and restoration.
- Vasebreaker contents and controller state.
- I, Zombie level authoring and reusable custom formations.
- Wall-nut Bowling, conveyor, brain, and lane-controller editors.
- Full Crazy Dave script, dialogue, reward, and cutscene editing.
- Complete Squirrel reconstruction.
- Complete plant and zombie state editing. The current advanced editor is
  partial: some internal values, visual attachments, animation states, and
  special entity behaviors still need build-specific game research.
- Plant behavior inheritance: let one plant keep its visible identity and
  position while using another plant's attack, projectile, timing, or selected
  gameplay attributes. This requires validated behavior and state changes, not
  only replacing the plant ID.
- Functional ZomBotany heads for existing zombies. Selecting a plant head
  should create its real attack and gameplay effect; a decorative head or
  reanimation by itself does not count as complete support.
- Never Lose mode: prevent the normal defeat transition when a zombie reaches
  the house and keep the current board playable. It must restore the original
  loss behavior when disabled and remain gated until the relevant board state
  and transition routine are validated per build.
- Per-plant value preservation in editors: applied settings such as firing
  cadence should remain active independently for every plant type. Selecting a
  different plant should reset the input control to that plant's own default
  or saved value instead of copying the previous selection, without removing
  overrides already applied to other plant types.
- Random Projectile mode: assign a newly chosen compatible projectile type to
  every shot as it is created. Each projectile in a repeated, multi-shot, or
  multi-row attack should be randomized independently, while projectiles
  already in flight keep the type they received.
- Fully composited animated previews of plants, zombies, projectiles, and
  attached accessories. Current previews use available read-only resource
  layers and a textual fallback.
