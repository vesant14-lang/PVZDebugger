# Changelog

All notable user-facing changes to PvZ Debugger are documented here. Detailed
implementation notes and research data live in the documents under `docs/`.

## [0.0.2-hotfix.2] - 2026-08-02

### Added

- **Functional ZomBotany heads.** Plant-head choices 26–31 now keep their real
  behavior ID, so their corresponding attacks are not reduced to decoration.
  The beta 0.1.1 dog/bee IDs are rejected instead of being mislabeled.
- **Current modifier display.** The selected zombie now reports its measured
  helmet, shield, ZomBotany head and active effects. Every `Keep current`
  option includes the current value, making repeated zombies easier to
  identify before applying a change.
- **Editor error translation.** Advanced editor and inspector dialogs now
  translate validation, unsupported-field, reconstruction, accessory,
  speed, address and rollback errors when English is selected. Legacy
  mojibake in older backend messages is repaired before translation.
  - **Live entity search.** The plant, zombie and projectile inspector now has a
  local search field that filters as you type. Partial names, translated or
  source names, accents, numeric IDs and hexadecimal addresses are accepted;
  pressing `Esc` clears the filter. Searching uses the last refreshed snapshot
  and does not write to the game.
  - **Small quality-of-life tools.** The Advanced log can now be copied or
  saved as a text report, cleared without touching the game, and refreshed
  quickly with `F5`; `Ctrl+Shift+L` copies the current report.

### Fixed

- **Safer disconnects after spawns.** The trainer no longer closes its process
  handle while a timed-out remote call may still be running. It keeps the
  connection available and asks the user to retry cleanup instead of leaving
  PvZ paused or executing freed temporary code.
- **Remote-memory cleanup retry.** If Windows temporarily refuses to release a
  completed remote allocation, the trainer records it and retries before the
  next remote operation or disconnect. This prevents repeated spawning from
  accumulating unreleased temporary memory.
- **Transactional live writes.** Adventure level, seed decks, endless rounds,
  hidden-mode changes, wave skips, and projectile physics now capture their
  previous values, verify every write, and restore the previous state when a
  later write fails.
- **Projectile editor stability.** Active projectile slots are rechecked for
  liveness, row and age immediately before modification. Rapid, repeated, and
  multi-shot attacks are less likely to receive a stale write, and an already
  frozen projectile is still reported as handled instead of appearing skipped.
- **Process watchdog.** When the Windows wait query fails, the trainer now
  validates the process through its PID and a safe module-memory probe. A
  genuinely closed game is automatically disconnected and removed from the
  selection instead of producing a later write error.
- **Advanced entity rollback reporting.** If a reconstructed plant or zombie
  cannot be retired during rollback, the trainer reports the affected address
  and recommends restarting PvZ instead of silently hiding the partial state.
- **Complete zombie donor crash.** Football, digger and ladder selections no
  longer copy an ordinary zombie's logical ID or animation phase into a
  different Reanimation skeleton. They now use the donor's real constructor
  type, while standard cone, bucket, screen-door and newspaper accessories
  keep their separate live path.
- **Unsafe overlay rejection.** Direct calls that request a complete donor
  while preserving another zombie type are rejected before any game memory is
  changed. This prevents the `00479F38` / null `mTrackInstances` crash found in
  the Steam GOTY report.
- **Remote-call cleanup.** Timed-out constructor and music calls no longer
  free their temporary code while the game thread may still be executing;
  completed calls are reclaimed safely on the next operation or disconnect.
- **Connection fallback across PC builds.** Startup now tries the next native
  candidate when a DRM launcher is detected, while standalone
  `PlantsVsZombies.exe` installations can still connect automatically.
- **Adventure-level safety.** Preparing a level while a match is active is
  rejected instead of writing the live board and sending the game to an
  unrelated mode or special screen.
- **HUD state and live-loop feedback.** HUD checkboxes are cleared when a
  board transition removes their restoration record, and projectile physics,
  fire-rate, mushroom, Squirrel and HUD errors are reported instead of being
  silently ignored.
- **Flash connection status.** A detected SWF whose ActionScript bridge
  failed now shows as identified rather than falsely reporting a connected
  bridge.
- **Flash language refresh.** Recreated cheat controls now use the active
  Spanish/English selection.
- **Native process selection.** When a launcher and `popcapgame1.exe` are both
  visible, automatic discovery now selects the playable PopCap process first.
  The launcher remains available only as a fallback when no game process has
  appeared yet.
- **HUD hiding on profiled PC builds.** Seed-bank and menu widgets now hide
  their complete rectangle instead of moving only their X coordinate. This
  prevents the GOTY redraw flicker and keeps the original size and position
  available for restoration when the trainer disconnects.
- **Connection error language.** The process-opening and Steam DRM launcher
  messages now follow the Spanish/English toggle, including the WinError code.
- **Safe Quick Play teardown.** If a hook cannot be restored, the trainer no
  longer frees the payload or reports the feature as detached. The state is
  kept for a retry, preventing a jump into freed memory.
- **External patch ownership.** A patch already enabled by another tool is
  now rejected instead of being silently claimed or restored by the trainer.
- **Live-operation rollback.** Scene, wave, Survival flag, global entity and
  zombie-speed writes now report rollback failures. Clearing existing zombies
  also snapshots their status first.
- **Fire-rate restoration.** Plants whose attack counter was clamped for a
  shorter cadence now restore that counter along with the original launch rate.
- **Connection watchdog and close.** A transient process/bridge read error no
  longer cancels the next watchdog poll, and one backend cleanup failure no
  longer prevents the other backends or the window from closing.
- **Guarded remote timeout recovery.** A timed-out generator keeps the
  measured pause instruction in place until its remote thread finishes, then
  restores it automatically. A second generator is refused while that
  cleanup is pending.
- **Atomic editor writes.** Freeze cleanup, wave composition, fire-rate,
  maintained zombie speed, HUD restoration and Zen/Unlock transactions now
  retain their rollback record when a read or write fails.
- **Partial Quick Play startup recovery.** If only some entry points were
  installed before an error, the temporary payload is retained and a later
  restore can remove the changed hooks safely.

## [0.0.2-hotfix.1] - 2026-08-01

This hotfix is published after the 0.0.2 final release and contains only the
post-release changes from this work session.

### Added



- **Direct Tree of Wisdom cheats.** The Tree panel can now read the exact
  keyboard codes declared by the connected installation and activate a
  selected code with one button. No manual typing and no Tree-height change is
  required. The feature remains build-validated and only sends the normal game
  input path; it does not guess internal cheat flags.
- **Zen Garden live refresh.** After a supported Zen edit, the trainer now
  attempts to synchronize already-created Zen plants in the active garden.
  Plant identity and position can update without leaving the screen. Growth
  stage, care timers, needs and supply counters remain save-backed and may
  still require rebuilding the Zen Garden screen.



### Fixed

- **Remote-call cleanup.** Timed-out constructor and music calls no longer
  free their temporary code while the game thread may still be executing;
  completed calls are reclaimed safely on the next operation or disconnect.
- **Native process selection.** When a launcher and `popcapgame1.exe` are both
  visible, automatic discovery now selects the playable PopCap process first.
  The launcher remains available only as a fallback when no game process has
  appeared yet.
- **Connection error language.** The process-opening and Steam DRM launcher
  messages now follow the Spanish/English toggle, including the WinError code.
- **External patch ownership.** A patch already enabled by another tool is
  now rejected instead of being silently claimed or restored by the trainer.
- **Live-operation rollback.** Scene, wave, Survival flag, global entity and
  zombie-speed writes now report rollback failures. Clearing existing zombies
  also snapshots their status first.




## [0.0.2] - Final release

This is the released 0.0.2 feature set. The older `0.0.2-alpha` entry below
is retained only as the historical pre-release record.

### Added

- **Never lose guard.** Replaced the old position guard with a reversible
  defeat-transition guard. Zombies can pass the lawnmower/house boundary and
  the normal loss cinematic is skipped only when the build's code signature is
  validated; the original bytes are restored on disable, disconnect, or close.
- **One-hit projectile tracking.** Normal shots in Original can now credit a
  target that spawned between trainer ticks when the shot disappeared inside
  the lawn, while shots leaving through the right edge remain ignored.
- **Zen Garden category.** A dedicated tab with five panels: garden care,
  supplies, plants, Tree of Wisdom and Tree dialogues. Available on GOTY Steam
  1.2.0.1096, the only build where the structures behind it were measured.
- **Zen Garden care.** Read every saved Zen plant with its garden, pot, growth
  stage, care progress and what it is currently asking for, then water,
  fertilise, bug-spray, play music, make happy, force full growth or set up
  coin production, for the whole garden or a selection, in one transaction that
  rolls back completely if any record fails to write.
- **Repeatable needs.** Make one plant ask for water, bug spray or music again
  immediately and keep asking, for studying the garden animations.
- **Zen Garden supplies.** Read and set fertiliser, bug spray, chocolate, Tree
  Food and the rake as amounts, unlock the phonograph, gardening glove,
  wheelbarrow and the extra gardens, or fill everything at once. The counters
  are stored with an offset the game requires, so the interface shows both the
  usable amount and the stored value.
- **Zen plant editor.** Add a plant to a chosen garden and pot, edit the
  selected plant's type, garden, position, growth stage, pending need and care
  counters, duplicate it, or remove it. Removal compacts the saved array and
  renumbers the plants currently on screen so none of them follows the wrong
  record. The aquarium refuses non-aquatic plants and the Zen Garden refuses a
  33rd plant, exactly as the game does.
- **Tree of Wisdom height steps.** `+1`, `+10`, `+100`, `+1000`, a maximum, a
  custom value, and restoring the height held before the last change.
- **Tree Food controls.** Read, set or fill the Tree Food counter.
- **Codes the Tree reveals.** Lists which cheat codes the current height has
  revealed and which are still pending, taken from the connected build's own
  text rather than from a remembered table.
- **Tree dialogue browser.** Browse every message in the build, search by text,
  code, ID or LawnStrings key, step to the previous or next message, filter to
  entries that would never show a distinct message, export to JSON, CSV or TXT,
  and compare against another build's export. Heights the game's text states are
  labelled separately from heights this program interpolates.

- **Advanced plant and zombie editor.** Inspect a live entity, change supported
  properties, apply changes to one entity or every entity of the same type,
  and spawn zombies without leaving the editor.
- **Entity previews.** Plants and zombies can be previewed with artwork read
  from the connected game's resources, with a text fallback when no compatible
  image is available.
- **Expanded zombie editing.** Named controls cover movement, size, status,
  environment, visible accessories, shields, helmets, and experimental
  ZomBotany heads without exposing raw memory fields.
- **Tree of Wisdom tools.** Read or change the tree height on supported builds
  and export the available Tree of Wisdom dialogue to JSON.
- **Direct plant placement.** Place plants on a chosen row and column,
  including experimental terrain combinations such as plants in pool rows
  without a Lily Pad.
- **New gameplay actions.** Remove or heal plants, heal or hypnotize zombies,
  clear status effects, retire projectiles, and ready compatible attacking
  plants.
- **Scene controls.** Change or stop music, control fog, hide individual HUD
  layers, read or restore the active scene, pause updates, and issue an
  experimental update pulse.

### Changed

- **Classic scene compatibility.** Original and other profiled classic PC
  builds now expose their measured HUD layout. Music remains limited to the
  GOTY profile until the older builds have separately validated call
  conventions and object layout.
- **Persistent HUD hiding.** Seed bank, menu button, shovel, and wave progress
  hiding is reapplied when PvZ refreshes or recreates the active board, instead
  of only working reliably for the shovel.
- **Beta organization.** The Squirrel control and its compatibility note now
  live in the Beta tab instead of Home.
- **Safety fix for Original music.** Removed the unverified runtime music
  fallback after it could match a routine without proving the Original
  calling convention. Original music is now blocked instead of risking a
  process crash.
- **Original custom projectiles.** Fire Pea conversion now uses the Original
  build's own initialization convention, and the trainer rechecks projectile
  slots immediately before changing them. Recycled or retired slots are
  skipped instead of receiving a stale write, reducing crashes when the game
  is firing several projectiles at once.
- Visual zombie accessories now use validated game routines on supported
  builds, reducing unwanted type changes and preserving the selected entity.
- Movement presets remain active while the edited zombie is alive, with an
  option to return control to the game.
- Mouse-wheel input over dropdowns and numeric fields no longer changes values
  accidentally.
- Removed the unused **Save current I, Zombie formation** option because the
  exported data could not be loaded by the game or trainer.

### Known limitations

- The Zen Garden tools are still experimental. Some actions may not work
  correctly, and parts of their English interface may remain untranslated.
- The advanced inspector remains experimental. Some values may be overwritten
  by the game or may change internally without immediately updating the model.
- Full advanced entity editing is currently limited to Original 1.0.0.1051 EN
  and GOTY Steam 1.2.0.1096. Other native builds keep the safer basic tools;
  Flash is excluded.
- Projectile replacement still cannot observe a projectile that is created
  and retired entirely between trainer polling intervals. Catapults, Cob
  Cannon, Fire Pea, and unusual constructor paths remain sensitive.
- Scene field-only mode changes the measured board values but does not promise
  an immediate visual background rebuild until the next board is created.
- Some model-specific accessories and animations remain disabled until their
  game routines are validated.

## [0.0.2-alpha] - 2026-07-29

The first major pre-alpha update focuses on restoration research, safer live
editing, and a more useful projectile laboratory.

### Added

- **Quick Play restoration.** Restores the hidden GOTY Steam Quick Play screen,
  its Mini-Games, Survival and Puzzles navigation, level selection, hover
  feedback, and return controls. It is experimental and warns that the trainer
  cannot guarantee restoration of the previous game or save state.
- **Advanced projectile editor.** Replace projectiles globally or for selected
  plants, keep native trajectories or force forward travel, edit firing rate,
  and adjust supported projectile behavior without manually finding offsets.
- **Live wave tools.** Edit upcoming zombie compositions and change Endless
  Survival flags from the trainer on validated builds.
- **GameMode research tools.** Explore hidden mode IDs carefully and save
  build-specific menu bookmarks for research.
- **Expanded combat tools.** Added one-hit projectiles, one-hit zombie bites,
  and reusable instant plants where the connected build provides a validated
  layout.
- Added a dedicated Puzzles tab and clearer controls for live wave and mode
  research.

### Changed

- Projectile controls are grouped into replacement, affected plants, firing
  rate, trajectory, and advanced behavior sections.
- Unlocking reports saved-progress changes separately from an immediate menu
  refresh. **Unlock everything** remains the safe public action; builds without
  a validated live refresh update when the game rebuilds its menu.
- Native memory features are enabled per exact executable profile. Unsupported
  betas and Flash do not inherit fields from similar final builds.
- Live controls now use clearer progress messages and complete English and
  Spanish labels.

### Fixed

- **Global freeze cleanup.** Disabling the zombie-freeze option now restores
  its code patch and clears the stale freeze state from existing zombies,
  preventing them from stopping again when they reach the old freeze point.
- **Deterministic new projectile tracking.** Projectile replacement now
  tolerates the game's rapid constructor retyping and validates slot age and
  liveness, improving repeated, multi-shot, and fast attacks instead of
  randomly leaving new shots with the plant's default type.
- **Broader scene controls.** Original and other profiled PC builds now expose
  their measured scene fields. Builds without a validated visual rebuild call
  use a safe field-only mode and apply the background when the next board is
  created.
- Custom projectile replacement now covers repeated and multi-shot attacks
  more reliably, including Repeater, Gatling Pea, Threepeater, Starfruit, and
  Split Pea.
- Multi-row and directional plants preserve the visual direction of each shot
  instead of concentrating every projectile in one lane.
- Converted Cob Cannon projectiles can travel forward from straight shooters,
  while genuine catapult sources keep their arc.
- Changing the selected projectile affects active and newly created shots more
  consistently, including after board transitions or reused projectile slots.
- Lower firing rates no longer leave incompatible attack counters that could
  crash the game. The applied rate also respects the plant's animation.
- One-hit projectiles and bites now choose their targets more accurately.
- Fixed mixed-language wave status text, repeated transition errors, stale
  process results, leaked process handles, and windows restored off-screen.
- Editing the destination level is no longer overwritten while the field or a
  dropdown has keyboard focus.

### Known limitations

- Some advanced features are available only on specifically validated builds
  and may require returning to a menu or restarting the current level.
- Cross-build test coverage is tracked in
  [PvZ Debugger 0.0.2 Feature Test Status](docs/FEATURE_TEST_STATUS_0.2.0.md).
- Flash support remains experimental and is not guaranteed for every SWF or
  player.
- Nintendo DS, Xbox 360, and PlayStation 3 support is reserved for future work.

## [0.0.1-alpha]

Initial public pre-alpha foundation.

### Added

- Support profiles for selected PvZ Original, GOTY, and beta PC builds.
- Automatic game process detection and exact-build validation.
- Adventure level controls, gameplay helpers, and basic live editors.
- Laboratory tools for waves, spawning, projectiles, and inspection.
- English and Spanish interface.
- Flashpoint and Ruffle discovery with experimental Flash support.
- Compatibility center, safety warnings, restoration tools, and user guide.
