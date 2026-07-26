# Safety model

PvZ Debugger modifies the memory of a running game and can crash it. Use it
only for offline testing with a backup save.

- Unknown builds are rejected instead of receiving guessed offsets.
- Controls are enabled by detected capabilities, not by edition name alone.
- Managed patches verify original bytes and restore patches owned by the current
  trainer instance.
- The trainer disconnects when the game process exits and clears the selection.
- The manual memory inspector is read-only until writing is explicitly enabled.
- Flash and future emulator integrations require exact artifact identity.
- Stop using a feature if the game begins transitioning or showing corruption.
  Close the game, reconnect, and restore the original state where possible.
