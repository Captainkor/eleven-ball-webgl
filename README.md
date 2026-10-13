# eleven-ball — WebGL build

Published build only; no source. Built from the `eleven-ball` Unity project
(Azure DevOps) against the EGDL3 split libraries.

- Unity 2022.3.62f3, IL2CPP, release build with managed stripping
- Gzip compression with Unity's JS decompression fallback, so it loads from a
  static host that cannot set `Content-Encoding` headers (GitHub Pages)
- `.nojekyll` disables Jekyll processing
- `multiplayer/Build/` names its files after the build's eleven-ball commit
  (`WebGL-Multiplayer-<commit>.*`, and `index.html` points at them). GitHub
  Pages lets browsers cache files for ten minutes, and with fixed names a
  browser that had the previous build could run new code against old data,
  which crashes at start-up ("indirect call to null"). A new build is new
  URLs, so nothing mixes; keep the previous build's files for one more
  publish so a browser still holding the old `index.html` finds them.

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

`multiplayer/` is the two-player preview of Eleven Ball, built from
eleven-ball `feature/multiplayer` 68ea948 (portable DLLs from egdl_Core
13aabd9, EGDL facades from egdl2 `feature/multiplayer` e090dbdb, multiplayer
through the optional multiplayer module's MultiplayerManager; since 2026-10-08
the game's host takes the relay socket whenever the portal sign-in makes it,
so the browser build announces two players to a launcher or phone paired
through the relay and reads their frames). The pairing lobby takes one to
two players. The site root stays the single-player game.

Modes (2026-10-12, MULTIPLAYER-ELEVENBALL-SPEC.md in egdl_Core): with two
players in, Home's Mode row picks **Co-op** (both drop into one shared jar
for a team score; a merge of balls dropped by different players earns a
teamwork bonus of half its points, rounded up) or **Versus** (a jar each,
side by side, the same seeded balls for everyone, a timed round, 180 s by
default, then ranked results). One player plays Solo, today's game. Each
player's card on the Players tab sets their assist (0 Off, 1 Guide: a ghost
of where the ball lands, 2 Small balls, 3 Slow fall: half gravity and an aim
magnet). High scores keep a list per mode. Built for four players, shipped
with two.

Multiplayer UI (2026-10-01): the Players tab lists each player (joined, lost
or waiting, their tracker and LAN or relay, their movement, Kick or Release);
the Tracking tab has a P1 / P2 switch that picks each player's movement (P2's
can be picked before P2 joins, or left as "Same as Player 1") and shows that
player's live signals; the header reads "Players n/2"; in game the top bar has
a chip per player, toasts say when a player joins, is lost, comes back or
leaves, and the pause panel lists the players with their movements.

In the browser every player comes in through the relay at
`https://www.enablegames.xyz/`, which pairs several phones on one code since
2026-10-02 (enableportal `feature/multiplayer-relay` 1bf9da8 deployed). Each
player scans the code with their own phone, or one phone that sees several
people (the tracker's group camera, from its build of egdl_Core step 1d)
tracks them all: while it is connected, an empty slot reads "Raise a hand in
view of <phone>", and the second person joins by raising a hand for a second.
