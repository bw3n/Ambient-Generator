# Sample pack

Converted from Alex Bainter's `samples-alex-bainter` repository (MIT licence),
which is what generative.fm itself uses.

- `vsco2-piano-mf/` and `vsco2-glock/` come from
  **Versilian Studios Chamber Orchestra 2: Community Edition (VSCO-2 CE)**,
  released under **CC0 / public domain**.
- Source files are 24-bit stereo WAV (1.2 GB for the full library).
  Only the notes these pieces need are included here, transcoded to
  OGG Vorbis q5 at 44.1 kHz stereo — 3.7 MB total.
- Durations and channel counts are preserved exactly; verified with ffprobe.

Keep this folder next to `generative-player.html`. The player probes for
it at startup and falls back to a CDN if it isn't reachable.
