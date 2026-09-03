# eleven-ball — WebGL build

Published build only; no source. Built from the `eleven-ball` Unity project
(Azure DevOps) against the EGDL3 split libraries.

- Unity 2022.3.62f3, IL2CPP, release build with managed stripping
- Gzip compression with Unity's JS decompression fallback, so it loads from a
  static host that cannot set `Content-Encoding` headers (GitHub Pages)
- `.nojekyll` disables Jekyll processing

Motion input streams from the enAble tracker over Socket.IO via the relay at
`https://www.enablegames.xyz/`. The relay authenticates at the handshake and
restricts origins, so this site's origin must be on its allowlist.
