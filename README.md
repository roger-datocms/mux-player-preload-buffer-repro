# Mux Player: preload reverts buffer settings on first play

Minimal reproduction for [muxinc/elements#1382](https://github.com/muxinc/elements/issues/1382).

**Live page:** https://roger-datocms.github.io/mux-player-preload-buffer-repro/

## What it shows

With `preload="metadata"`, `setupPreload` in [`playback-core/src/preload.ts`](https://github.com/muxinc/elements/blob/ef41d8beebc55644e63b5279feca173b164c764e/packages/playback-core/src/preload.ts#L26-L63) saves `maxBufferLength` and `maxBufferSize` when hls.js is created, and writes them back on the first `play`. Values set through `player._hls.config` before that, as the [cost optimization guide](https://www.mux.com/docs/pricing/optimizing-video-costs#player-buffer-length) suggests, are silently reverted.

The page runs two players with `@mux/mux-player@3.14.1` (latest stable) and Mux's public demo video:

- **A:** sets `maxBufferLength = 10` and `maxBufferSize = 0` before play. After play, they read `30` and `60000000`, and the player buffers far beyond 10 s.
- **B:** also sets `maxMaxBufferLength = 10`, which `preload.ts` never touches. The buffer stays at about 10–15 s.

## Run locally

Open `index.html` in a browser. No build step.
