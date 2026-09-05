# eleven-ball — WebGL build

Published build only; no source. Built from the `eleven-ball` Unity project
(Azure DevOps) against the EGDL3 split libraries.

- Unity 2022.3.62f3, IL2CPP, release build with managed stripping
- Gzip compression with Unity's JS decompression fallback, so it loads from a
  static host that cannot set `Content-Encoding` headers (GitHub Pages)
- `.nojekyll` disables Jekyll processing

## UI (2026-09-04)

The launcher and the in-game UI are UI Toolkit, following the EGDL launcher
design system ("Direction B · minimal"):

- **Launcher** (`MainMenu_UITK`): Home, Session settings, Tracking (movement
  picker with live signal meters), Pairing, Settings.
- **In game**: score / eleven-ball chart / best-three top bar, pause panel
  (Escape or the Pause action), game-over panel with the screenshot and top
  scores.
- **Highscores**: today / this month / all time from `games.json`.

Scene order: `eag_Login` → `MainMenu_UITK` → `Game` ↔ `Highscores`.

Motion input streams from the enAble tracker over Socket.IO via the relay at
`https://www.enablegames.xyz/`. The relay authenticates at the handshake and
restricts origins, so this site's origin must be on its allowlist. Without a
portal sign-in the game listens for OSC on the local port, which the browser
cannot receive.
