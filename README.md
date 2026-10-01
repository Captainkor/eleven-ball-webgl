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

## Two-player preview (`multiplayer/`, 2026-10-01)

`multiplayer/` is an early two-player test of Eleven Ball, built from
eleven-ball `feature/multiplayer` 1ed3517 (portable DLLs from egdl_Core
868c999, EGDL facades from egdl2 `feature/multiplayer` bb8647bd). A second
fruit spawner drops balls for player two, and the pairing lobby takes one to
two players. The site root stays the single-player game.

Multiplayer UI (2026-10-01): the Players tab lists each player (joined, lost
or waiting, their tracker and LAN or relay, their movement, Kick or Release);
the Tracking tab has a P1 / P2 switch that picks each player's movement (P2's
can be picked before P2 joins, or left as "Same as Player 1") and shows that
player's live signals; the header reads "Players n/2"; in game the top bar has
a chip per player, toasts say when a player joins, is lost, comes back or
leaves, and the pause panel lists the players with their movements.

In the browser every player comes in through the relay, and the relay
deployed at `https://www.enablegames.xyz/` pairs one phone per game, so this
preview takes one player for now. Once enableportal `feature/multiplayer-relay`
is deployed, the same build takes a second phone. Two players today need the
desktop build with the EnAble Launcher.
