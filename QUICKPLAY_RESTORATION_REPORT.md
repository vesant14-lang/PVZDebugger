# PvZ Quick Play Restoration

Detailed technical report for the native, reversible implementation in PvZ Debugger.

| | |
| --- | --- |
| Validated build | Plants vs. Zombies GOTY Steam 1.2.0.1096 |
| PE TimeDateStamp | `0x4D02B058` |
| Architecture | 32-bit x86 Windows process |
| Implementation status | Experimental native integration |
| Report edition | 28 July 2026 |

> **Research warning.** This feature modifies a live process and temporarily changes
> profile-related runtime fields. Restoration is best effort. Back up the PvZ profile
> before activation and do not treat this report as a guarantee that every save or
> session can be returned to an identical prior state.

## Contents

1. [Executive summary](#1-executive-summary)
2. [Scope and support boundary](#2-scope-and-support-boundary)
3. [Discovery and evidence chain](#3-discovery-and-evidence-chain)
   - 3.1 [What was preserved and what was missing](#31-what-was-preserved-and-what-was-missing)
4. [Original runtime architecture](#4-original-runtime-architecture)
   - 4.1 [Preserved resources](#41-preserved-resources)
   - 4.2 [Original composition and command paths](#42-original-composition-and-command-paths)
5. [Native payload design](#5-native-payload-design)
   - 5.1 [Hook manifest](#51-hook-manifest)
   - 5.2 [Clip wrapper](#52-clip-wrapper)
   - 5.3 [Mouse and cloud router](#53-mouse-and-cloud-router)
   - 5.4 [Rendering and hover strategy](#54-rendering-and-hover-strategy)
6. [Adventure selector and launch sequence](#6-adventure-selector-and-launch-sequence)
   - 6.1 [Launch fields](#61-launch-fields)
   - 6.2 [Special-level identity](#62-special-level-identity)
7. [Controller lifecycle](#7-controller-lifecycle)
   - 7.1 [Activation transaction](#71-activation-transaction)
   - 7.2 [Stable page entry](#72-stable-page-entry)
   - 7.3 [Return to Quick Play](#73-return-to-quick-play)
   - 7.4 [Maintenance loop](#74-maintenance-loop)
   - 7.5 [Restore and disable](#75-restore-and-disable)
8. [Safety model and failure handling](#8-safety-model-and-failure-handling)
9. [Validation and test evidence](#9-validation-and-test-evidence)
10. [Known limitations](#10-known-limitations)
11. [Reproduction and developer workflow](#11-reproduction-and-developer-workflow)
    - 11.1 [Payload regeneration](#111-payload-regeneration)
12. [Address and structure reference](#12-address-and-structure-reference)
13. [Recommended future work](#13-recommended-future-work)
14. [Conclusion](#14-conclusion)

## 1. Executive summary

Quick Play is not implemented as a conventional GameMode that can be enabled with one
value. The GOTY executable preserves a hidden left-hand page inside the three-page
`DelayLoad_Zombatar` container. Its original background, cloud art, Back widget,
drawing calls and destination commands survived, but the entry widget, three cloud
widgets, part of the visible button layer and correct clipping were removed or left
disconnected.

The restoration therefore combines preserved game behavior with a small native x86
payload. The payload repairs clipping, draws the missing labels, rebuilds hover
feedback, routes cloud clicks to original command IDs, provides a 1-50 Adventure
selector behind the wooden Quick Play sign, and preserves the two-step Back behavior.
Python owns installation, validation, maintenance, rollback and temporary profile
restoration.

| Layer | Responsibility | Trust boundary |
| --- | --- | --- |
| Original game | Loads images, draws the page, animates the 2400 px container, and opens Mini-Games, Puzzle and Survival through preserved command handlers. | Used only after exact build and object validation. |
| Native payload | Repairs missing routing and rendering behavior inside the live 32-bit process. | Build-specific machine code with four validated entry points. |
| Trainer controller | Validates state, links the payload, installs hooks transactionally, maintains temporary level identity, restores hooks and frees memory. | Rejects unknown builds, altered signatures and unsafe menu state. |
| User interface | Requires explicit activation, exposes Return to Quick Play, and provides a separate Restore and Disable action. | Warns that profile restoration is best effort. |

*(Trainer UI ↔ QuickPlayController ↔ GOTY process: Research tab activation/return/restore drives controller validation, allocation, hooks, maintenance and rollback, which operate on the GOTY process's preserved page, resources, original transition and destinations.)*

## 2. Scope and support boundary

The native integration is intentionally narrower than the rest of PvZ Debugger. It is
enabled only for the profiled GOTY Steam 1.2.0.1096 process whose PE TimeDateStamp is
`0x4D02B058`. Original releases, beta releases, regional GOTY revisions, Flash,
Nintendo DS and console editions are not enabled by inference.

| Condition | Required value | Reason |
| --- | --- | --- |
| Executable identity | TimeDateStamp `0x4D02B058` | Prevents address reuse across unrelated builds. |
| Module base | `0x00400000` | The restoration was validated without ASLR; a relocated image is rejected. |
| Container vtable | `0x006EEC88` | Confirms the expected `DelayLoad_Zombatar` object. |
| Container width | 2400 at `root+0x48` | Confirms the three-page horizontal layout. |
| Widget manager vtable | `0x006FC114` | Confirms the expected menu manager object. |
| Board state | `LawnApp+0x868 == null` | Activation and return are allowed only from the main menu. |
| Menu position | `root+0x40 == -800` | Confirms that the central page is currently visible. |
| Image objects | vtable `0x006FDCC4` and sane dimensions | Rejects missing, stale or malformed resource pointers. |

**Important distinction:** recognizing the executable is necessary but not sufficient.
The controller also validates live object identity, menu state, image metadata and
unmodified hook bytes before writing anything.

## 3. Discovery and evidence chain

The work progressed from offline resource analysis to controlled dynamic tracing.
Earlier hypotheses were retained in the research log, including a discarded idea that
Quick Play belonged to the Achievements screen. Cross-references proved that the
relevant composition routine belongs to `DelayLoad_Zombatar` instead.

| Phase | Evidence | Result |
| --- | --- | --- |
| Resource inventory | `IMAGE_QUICKPLAY_*` and `selector_morewaystoplay` assets | Confirmed that GOTY contains complete original art. |
| Cross-reference analysis | Single caller of `0x451A00` at `0x44F05B` | Placed the composition in `DelayLoad_Zombatar`. |
| Object graph inspection | root width 2400; pages at x=0, 800 and 1600 | Identified the hidden left page and preserved Back widget. |
| Dynamic tracing | original command IDs `0x65`, `0x66`, `0x6E` and `0x72` | Recovered category destinations and Back dispatch. |
| Render probe | empty Graphics clip while `0x451A00` still executes | Explained why the original page was invisible. |
| Visual reconstruction | normal and highlight image objects inspected live | Reused valid art and rebuilt invalid highlights safely. |
| Adventure launch trace | `GameSelector+0x144`, `+0xE4` and `+0xE8` | Recovered a stable transition without calling an unverified internal function. |
| Native integration | generated relocatable x86 payload plus transactional installer | Removed the runtime dependency on Cheat Engine. |

### 3.1 What was preserved and what was missing

| Component | State in GOTY | Restoration treatment |
| --- | --- | --- |
| 800x600 page background | Preserved and loaded | Drawn through original `0x451A00` path after clip repair. |
| Three cloud images | Preserved and loaded | Drawn by the original composition routine. |
| Visible category labels | Normal art preserved; some highlight art malformed | Normal art centered; Mini-Games uses original highlight; others use rebuilt glow. |
| Back control | Widget and command ID `0x72` preserved | Original dispatch retained; intercepted only while the level grid is open. |
| Entry command | Case `0x70` preserved but entry widget absent | Trainer starts the original transition directly. |
| Cloud widgets | Not present in the live graph | Mouse regions route to preserved destination commands. |
| Adventure selector | No preserved functional selector on this page | Reconstructed as a 10x5 grid using the original number sprite sheet. |

## 4. Original runtime architecture

The key object is a `DelayLoad_Zombatar`-derived menu container referenced through
`LawnApp`. The central Zombatar page is visible when the 2400 px container sits at
x=-800. Moving it to x=0 reveals the preserved Quick Play page on the left.

```
Preferred global: 0x00731C50 -> LawnApp*
LawnApp + 0x868 -> Board* (null at the main menu)
LawnApp + 0x870 -> DelayLoad_Zombatar* (root container)
LawnApp + 0x94C -> PlayerInfo*
root + 0x20 -> WidgetManager*
root + 0x40 -> current container x
root + 0x44 -> current container y
root + 0x48 -> width = 2400
root + 0x144 -> requested Adventure level
root + 0x154..0x164 -> transition duration, source and destination
```

Page coordinates:

```
x = 0     Quick Play (left page)
x = 800   Zombatar page (central page)
x = 1600  third preserved page

Visible central state: root x = -800
```

### 4.1 Preserved resources

| Resource | Global | Observed size / use |
| --- | --- | --- |
| `selector_morewaystoplay_background` | `0x0072F37C` | 800x600 at (0,0) |
| `quickplay_minigames_cloud` | `0x0072F04C` | 360x269 at (20,40) |
| `quickplay_puzzles_cloud` | `0x0072F4B8` | 381x177 at (350,285) |
| `quickplay_survival_cloud` | `0x0072F47C` | 488x253 at (130,135) |
| `quickplay_minigames_button` | `0x0072F6B0` | Centered over Mini-Games cloud |
| `quickplay_minigames_button_highlight` | `0x0072F554` | Valid original highlighted art |
| `quickplay_puzzles_button` | `0x0072ECD4` | Centered over Puzzles cloud |
| `quickplay_survival_button` | `0x0072EF10` | Centered over Survival cloud |
| `SelectorScreen_LevelNumbers` | `0x0072F84C` | Sprite strip used for the 1-50 grid |

Every image is validated as a live Image object with vtable `0x006FDCC4` and positive
dimensions no larger than 800x600. The installer does not assume that resource loading
has completed merely because the process exists.

### 4.2 Original composition and command paths

Function `0x451A00` composes the hidden page through the generic image draw helper at
`0x59F180`. The sole call site is `0x44F05B`. The preserved destination commands are
dispatched through the embedded `ButtonListener` at `root+0xA0`.

| Target | Command ID | Observed result |
| --- | --- | --- |
| Mini-Games cloud | `0x65` | Opens the original Mini-Games selector. |
| Puzzles cloud | `0x66` | Opens the original Puzzle selector. |
| Survival cloud | `0x6E` | Opens the original Survival selector. |
| Back | `0x72` | Returns from the left page to the central/main-menu path. |
| Removed entry | `0x70` | Original case moves the container to x=0; widget is absent. |

## 5. Native payload design

The payload is authored as 32-bit MASM, compiled to COFF, then converted into a Python
module. The build helper resolves internal symbols and records absolute relocations.
At runtime the controller rebases both payload-internal references and preferred game
pointers before copying 2,104 bytes into remote executable memory.

### 5.1 Hook manifest

| Entry point | Expected original bytes | Branch | Payload symbol | Purpose |
| --- | --- | --- | --- | --- |
| `0x0044F05B` | `E8 A0 29 00 00` | CALL | `quickplay_clip_wrapper` | Repair the Graphics clip around the preserved composition call. |
| `0x00450960` | `55 8B EC 83 E4 F8` | JMP | `quickplay_cloud_router` | Route cloud, sign and level-grid clicks; otherwise resume original MouseDown. |
| `0x00450EE0` | `55 8B E9 83 BD B4 00 00 00 00` | JMP | `quickplay_back_dispatch` | Close the reconstructed grid first; otherwise preserve original Back behavior. |
| `0x00451A52` | `C3 CC CC CC CC` | JMP | `quickplay_button_art` | Draw labels, highlights, sign feedback and the level selector after original composition. |

Each replacement is a near `E8` or `E9` branch with a signed rel32 displacement.
Remaining bytes in the validated patch window are padded with NOP. If any original
byte differs, activation stops before allocation or modification. This catches
leftover Cheat Engine patches, incompatible executables and unexpected third-party
modifications.

### 5.2 Clip wrapper

The preserved draw routine was executing with a clip equivalent to `[800,0,0,0]`, so
its resources existed but could not contribute visible pixels. The wrapper saves
Graphics fields `+0x20..+0x2C`, installs a temporary 800x600 clip at the page origin,
calls the original tail at `0x451A00`, restores every field and returns.

```
save Graphics clip fields
clip = { x: 0, y: 0, width: 800, height: 600 }
call original composition routine
restore previous clip fields
return
```

### 5.3 Mouse and cloud router

The router activates only when the object vtable is `0x006EEC88` and the container is
on the Quick Play page. All other calls reconstruct the original prologue and continue
at `0x450966`. The hit regions intentionally do not overlap.

| Region | Inclusive logical bounds | Action |
| --- | --- | --- |
| Quick Play sign | x 20..210, y 395..552 | Open the Adventure level selector. |
| Mini-Games | x 35..300, y 50..150 | Dispatch command `0x65`. |
| Survival | x 200..540, y 175..280 | Dispatch command `0x6E`. |
| Puzzles | x 420..720, y 320..440 | Dispatch command `0x66`. |
| Grid Back | x 630..799, y 430..571 | Close the grid and return to clouds. |

### 5.4 Rendering and hover strategy

The payload centers category art from live Image width and height rather than
hard-coding text positions. Mini-Games uses its valid original highlighted image. The
nominal Survival highlight has invalid metadata and the nominal Puzzles highlight
points to unrelated Zombatar frame content, so both are excluded. Their hover state is
rebuilt by drawing the normal art with a reversible tint/alpha pulse after saving and
restoring Graphics state.

The wooden sign has no preserved widget. A clipped, tinted redraw of the original
background provides click feedback without introducing replacement artwork. The
effect is generated only while the cursor is within the sign region.

## 6. Adventure selector and launch sequence

Clicking the wooden sign replaces the cloud layer with a 10-column by 5-row selector.
Levels 1 through 50 are drawn with the original `SelectorScreen_LevelNumbers` image
strip via function `0x520960`. The payload stores both the last selected level and the
current hover level, and uses existing Graphics color fields for feedback.

```
Grid origin: x = 150, y = 90
Cell step: x = 56, y = 65
Columns: 10
Rows: 5

column = floor((mouse_x - 150) / 56)
row = floor((mouse_y - 90) / 65)
level = row * 10 + column + 1
valid level range = 1..50
```

### 6.1 Launch fields

| Field | Write | Role |
| --- | --- | --- |
| `GameSelector+0x144` | selected level | Provides the requested Adventure level to the preserved update path. |
| `PlayerInfo+0x50` | selected level (temporary) | Keeps special-level identity visible to code that reads profile progress after Board creation. |
| `GameSelector+0xE8` | 450 | Places the original start counter at its transition threshold. |
| `GameSelector+0xE4` | 1 | Sets `mStartingGame`; the next original Update performs the launch. |
| payload `original_profile_level` | previous `PlayerInfo+0x50` | Stores the value required for best-effort restoration. |
| payload `restore_pending` | 1 | Signals the controller that temporary state must be maintained and later restored. |

**Why no direct internal function call?** An early prototype attempted to dispatch
Adventure through an unrelated button command and crashed after returning to
temporary code. The integrated design writes only confirmed state fields and lets the
game's next normal Update perform the transition.

### 6.2 Special-level identity

Some levels continue reading `PlayerInfo+0x50` after the Board has been constructed.
Restoring that field immediately can turn special stages into generic plant selection
or otherwise change their setup. The controller therefore keeps the temporary level
until it observes a Board whose `Board+0x5568` matches the selected level, and
restores only after that Board disappears.

This is still a best-effort mechanism. The 1-50 grid reflects Adventure numbering, not
a guarantee that every profile state, special encounter, Dr. Zomboss transition or
modified executable will interpret every number identically.

## 7. Controller lifecycle

### 7.1 Activation transaction

- Validate exact executable timestamp, fixed module base, main-menu state, container
  vtable and width, manager vtable, image objects and dimensions.
- Read every hook window and compare it byte-for-byte with the expected original
  signature.
- Reserve one remote block with `VirtualAllocEx` using `MEM_COMMIT | MEM_RESERVE` and
  `PAGE_EXECUTE_READWRITE`.
- Link payload-local relocations and rebase named game pointers.
- Write the payload, then install four relative branches with instruction-cache-safe
  code writes.
- If any write fails, restore already changed entries in reverse order, free the
  remote block and leave the controller inactive.
- Enter the page through the preserved transition fields rather than forcing root x
  directly.

### 7.2 Stable page entry

Directly forcing root x=0 can yield a black page with only Back visible because
normal invalidation and movement state are bypassed. Activation and Return to Quick
Play use the preserved 75-unit transition:

```
root+0x154 = 75
root+0x158 = current x
root+0x15C = current y
root+0x160 = 0
root+0x164 = 0
root+0x03C = 1 ; request update/invalidation
```

### 7.3 Return to Quick Play

The trainer's Return to Quick Play button is intentionally not another install. It
requires an active controller, re-runs full live menu validation and submits the same
original transition to x=0. The remote payload address and hook set are reused, so
`VirtualAllocEx` is called only once per active session.

### 7.4 Maintenance loop

While Quick Play is active, the UI schedules `maintain()` every 100 ms. The loop is
small and state-driven: it does nothing unless `restore_pending` equals 1. It captures
`PlayerInfo+0x58` (playthrough) on demand, watches `Board+0x5568` for the selected
level, and restores `PlayerInfo+0x50` and `+0x58` after the observed Board is
destroyed.

### 7.5 Restore and disable

- Attempt any pending profile restoration.
- Restore all four original code windows in reverse order.
- Reset the menu transition fields and central position to x=-800 when the validated
  root still exists.
- Mark the controller inactive before releasing remote memory.
- Release the payload with `VirtualFreeEx(..., MEM_RELEASE)`.
- Report an error if one or more hook windows could not be restored.

## 8. Safety model and failure handling

| Risk | Control | Residual limitation |
| --- | --- | --- |
| Wrong executable | Exact TimeDateStamp and fixed base gate | A modified binary could share metadata; hook signatures and live object checks add further defense. |
| Conflicting Cheat Engine patch | Every hook byte must match the pristine signature | The user must restore old CE scripts before activation. |
| Wrong screen | Board must be null and root x must be -800 | Activation is deliberately unavailable from submenus or gameplay. |
| Resources still loading | All required Image pointers and dimensions are checked | The user may need to wait for the main menu to finish loading. |
| Partial installation | Transactional reverse-order rollback | A process termination during a system-level write cannot be recovered by the trainer. |
| Temporary profile state | Pending flag, Board observation and best-effort restoration | Killing PvZ or the trainer during a pending level can leave temporary values active. |
| Malformed highlight art | Known-invalid resources are never drawn | Puzzles and Survival use reconstructed hover effects rather than their nominal assets. |
| Trainer closed first | Backend detach attempts `stop()` when active | Forced termination or power loss bypasses cleanup. |

**Operational rule:** activate only from a fully loaded main menu, test one action at
a time, return to the main menu, then use Restore and Disable before closing the
trainer or the game.

## 9. Validation and test evidence

The implementation has two different evidence classes. Dynamic research confirmed
visual composition, category navigation, hover behavior, Back routing and selected
Adventure launches in the live GOTY process. Automated tests validate controller logic
with a deterministic fake process memory model; they do not replace live testing of
every level and machine.

| Test area | Automated assertion |
| --- | --- |
| Installation | Payload relocations resolve inside the remote block and all four hooks change from their expected originals. |
| Rollback | `stop()` restores every hook and releases the remote allocation. |
| Temporary level | The selected level is held while the matching Board exists, then the original profile level is restored. |
| Re-entry | Return to Quick Play reuses the same remote address and does not allocate a second block. |
| Build gating | Other timestamps are rejected. |
| Menu gating | A non-main-menu container position is rejected. |
| Project regression | 411 repository tests passed after adding native re-entry. |

Live validation confirmed the three original destinations and a successful Adventure
1-1 to 1-2 temporary launch with the prior displayed progress restored. The full set of
50 choices, every profile progression edge case and every Steam runtime environment
have not been exhaustively verified.

## 10. Known limitations

- **Single build.** Native Quick Play is enabled only for GOTY Steam 1.2.0.1096. Other
  GOTY revisions, Original and Beta require independent research.
- **No ASLR support.** A module base other than `0x00400000` is blocked even though
  pointer cells can be rebased.
- **Best-effort save restoration.** The trainer cannot guarantee that a save or active
  session returns to exactly its prior state.
- **Interrupted lifecycle.** Killing the game, trainer or operating system while
  `restore_pending` is set can bypass cleanup.
- **Adventure semantics.** The 1-50 selector drives confirmed progression fields but is
  not an independent scripted recreation of every special stage.
- **Highlight fidelity.** Survival and Puzzles use a safe reconstructed glow because
  their nominal highlight resources are malformed.
- **Loading latency.** The page can take time to become available after game startup;
  resource validation intentionally rejects premature activation.
- **Third-party modifications.** Any changed entry signature causes a safe refusal.
  Compatibility with mods is not claimed.

## 12. Address and structure reference

| Address / offset | Meaning | Access |
| --- | --- | --- |
| `0x00731C50` | Preferred LawnApp global pointer | Read |
| `LawnApp+0x868` | Board pointer | Read |
| `LawnApp+0x870` | DelayLoad_Zombatar root pointer | Read |
| `LawnApp+0x94C` | PlayerInfo pointer | Read |
| `PlayerInfo+0x50` | Adventure/profile level | Temporary read/write |
| `PlayerInfo+0x58` | Playthrough value | Temporary read/write |
| `root+0x20` | WidgetManager pointer | Read |
| `root+0x3C` | Update/invalidation request byte | Write |
| `root+0x40`/`+0x44` | Container x/y | Read; reset on disable |
| `root+0x48` | Container width | Read validation |
| `root+0x144` | Requested Adventure level | Write |
| `root+0x154..0x164` | Transition timing and endpoints | Write |
| `root+0xE4` | mStartingGame | Write from native payload |
| `root+0xE8` | mStartingGameCounter | Write from native payload |
| `Board+0x5568` | Observed active level identifier | Read |
| `WidgetManager+0x108`/`+0x10C` | Logical mouse x/y | Read from payload |
| `0x00451A00` | Original hidden-page composition routine | Called |
| `0x0059F180` | Generic image draw helper | Called |
| `0x00520960` | Image-cell draw helper for level numbers | Called |

Offsets are observations for the validated executable only. They are not a portable
PvZ memory specification and must never be copied into another build solely because
the visible game version appears similar.



## 14. Conclusion

The restored page is a hybrid preservation result: original PopCap assets, layout,
transition machinery, Back behavior and destination commands are combined with a
small build-specific compatibility layer that reconnects what the final executable no
longer exposes. The design avoids modifying the executable on disk and favors strict
refusal over guessed compatibility.

The most important engineering property is reversibility under normal operation.
Installation is transactional, re-entry reuses the existing payload, disable restores
original code bytes and frees remote memory, and temporary level state is maintained
only until the matching Board closes. Those controls reduce risk, but they do not
eliminate the need for profile backups and exact-build testing.

**Final status:** native Quick Play is experimentally functional on the exact GOTY
Steam 1.2.0.1096 build, with original category navigation, reconstructed Adventure
selection, two-step Back handling and a trainer-side Return to Quick Play command.
Compatibility outside that boundary is intentionally not claimed.
