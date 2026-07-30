# Known issues

The broader per-feature compatibility boundary for this release is documented
in [PvZ Debugger 0.2.0 Feature Test Status](FEATURE_TEST_STATUS_0.2.0.md).

## Restored Quick Play

- Quick Play is experimental and currently enabled only for the profiled GOTY
  Steam 1.2.0.1096 executable.
- It temporarily changes profile and runtime state to load Adventure levels.
  The trainer attempts to restore the previous level and playthrough value when
  the board closes or the feature is disabled, but it cannot guarantee that a
  save or active session will return to exactly its previous state.
- Back up the PvZ profile before activation. Avoid closing the trainer, killing
  PvZ, or losing power while a temporary Quick Play level is loading.
- Original, Beta, regional GOTY builds, and ASLR-relocated executables are not
  enabled for Quick Play without separate validation.

This is a research build. Some behavior is intentionally incomplete:

- Some changes are applied internally but do not appear until the affected
  menu or board is closed and reopened.
- Projectile conversion and physics can fail, behave incorrectly, or work only
  partially. Catapults, Cob Cannon, fire, multi-shot, and custom projectiles
  are especially sensitive.
- All listed PC versions remain under test; recognition does not prove that
  every feature works on every version or computer.
- Windows 7, 8, and 8.1 may have startup, runtime, permissions, or packaging
  issues.
- Flashpoint/Ruffle detection may select the wrong player when several copies
  are open.
- Squirrel in beta 0.9.9.1029 is partial; full restoration is planned.
- Live scene, wave, flag, and entity changes may create visual artifacts.
- One-hit, immunity, instant recharge, beta entities, and hidden modes are
  experimental and can vary by build.
- Modified or unofficial executables may be rejected or behave differently.
- English localization is incomplete in places.

Report bugs with the edition/version, language, executable identity, Windows
version, backend, exact steps, menu or board state, relevant log, and
screenshots. Do not attach game binaries or personal save files.
