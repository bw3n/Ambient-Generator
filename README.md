# Ambient Generator

A generative ambient music player that runs entirely in the browser — no
backend, no build step, just one HTML file. Eight evolving pieces, each
built from its own set of rules (probabilistic note choices, layered
drones, swells, and slowly shifting harmony) rather than a fixed
recording, so no two plays sound quite the same.

Built on [Tone.js](https://tonejs.github.io/) for audio scheduling and
sample playback.

## Running it

Because the player loads local audio samples, it needs to be served over
HTTP rather than opened directly as a `file://` URL (the browser blocks
that for CORS reasons). From the project root:

```bash
python3 -m http.server 8000
```

Then open `http://localhost:8000/generative-player.html`.

## Samples

The `samples/` folder ships a small local sample pack so pieces load
instantly and work offline. It's converted from Alex Bainter's
`samples-alex-bainter` repo (MIT licence) — the same source
[generative.fm](https://generative.fm) uses — sourced from **Versilian
Studios Chamber Orchestra 2: Community Edition (VSCO-2 CE)**, released
CC0 / public domain. See `samples/README.md` for details.

If the local samples aren't reachable, the player falls back to loading
from a CDN automatically.

## The pieces

Eight pieces, each with its own instrument voice, harmonic rules, and
tweakable parameters (swell timing, note density, brightness, reverb,
etc.) exposed via sliders in the drawer — drag any of them while a piece
is playing to hear it change live.

## Status

Actively developed. See `REDESIGN_NOTES.md` in the project history for
background on recent UI and audio-engine work (caching, mix fixes,
visual cleanup).
