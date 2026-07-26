# Flash support

Flash support is experimental and build-dependent. The application currently
focuses on desktop Ruffle and Flashpoint detection; ordinary browsers are not
a reliable live-memory target.

A known local SWF may be identified by its artifact and profile. Some PopCap
demo cheats can then be exposed, including level stepping, next wave, free
planting, unlocks, seed refresh, awards, supported zombie spawns, pause/step,
and speed controls. The exact actions depend on the movie.

The Flash tab can prepare an experimental local ActionScript bridge for an
instrumented, profile-verified movie. An unmodified movie does not contain that
client and remains limited to its own supported cheats.

Known limitations:

- Flashpoint and Ruffle versions may be detected incorrectly when several
  players are open.
- Browser-hosted SWFs, modified movies, and unknown SWF hashes may be rejected.
- Flash does not provide guaranteed GOTY-equivalent features.
- A demo must be opened with its companion files available.
- Flash support is not claimed to be 100% compatible with every historical web
  release.
