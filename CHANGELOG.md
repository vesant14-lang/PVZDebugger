# Changelog

All notable user-facing changes to PvZ Debugger are documented here. Detailed
implementation notes and research data live in the documents under `docs/`.

## [0.2.0-alpha] - 2026-07-29

The first major pre-alpha update focuses on restoration research, safer live
editing, a more useful projectile laboratory, advanced entity inspection, and
new Zen Garden and Tree of Wisdom tooling.

### Added

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
- **Direct plant placement.** Place plants on a chosen row and column,
  including experimental terrain combinations such as plants in pool rows
  without a Lily Pad.
- **New gameplay actions.** Remove or heal plants, heal or hypnotize zombies,
  clear status effects, retire projectiles, and ready compatible attacking
  plants.
- **Scene controls.** Change or stop music, control fog, hide individual HUD
  layers, read or restore the active scene, pause updates, and issue an
  experimental update pulse.
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

- Visual zombie accessories now use validated game routines on supported
  builds, reducing unwanted type changes and preserving the selected entity.
- Movement presets remain active while the edited zombie is alive, with an
  option to return control to the game.
- Mouse-wheel input over dropdowns and numeric fields no longer changes values
  accidentally.
- Removed the unused **Save current I, Zombie formation** option because the
  exported data could not be loaded by the game or trainer.
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

- The Zen Garden tools are still experimental. Some actions may not work
  correctly, and parts of their English interface may remain untranslated.
- The advanced inspector remains experimental. Some values may be overwritten
  by the game or may change internally without immediately updating the model.
- Full advanced entity editing is currently limited to Original 1.0.0.1051 EN
  and GOTY Steam 1.2.0.1096. Other native builds keep the safer basic tools;
  Flash is excluded.
- Some model-specific accessories and animations remain disabled until their
  game routines are validated.
- Some advanced features are available only on specifically validated builds
  and may require returning to a menu or restarting the current level.
- Cross-build test coverage is tracked in
  [PvZ Debugger 0.2.0 Feature Test Status](docs/FEATURE_TEST_STATUS_0.2.0.md).
- Flash support remains experimental and is not guaranteed for every SWF or
  player.
- Nintendo DS, Xbox 360, and PlayStation 3 support is reserved for future work.
