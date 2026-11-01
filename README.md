# AMD-Overhaul-Azimuth-

A drop-in D3D12 proxy for Forza Horizon 2 on Azimuth, tuned for AMD RDNA GPUs.

V2 carried the patch on async compute and PSO caching. V3 is the full overhaul, worked in phases with a measurement gate on every idea — if an idea fails its measurement, it gets cut, not forced in.

## Status

- **Target: Azimuth 1.0.4** (retargeted from 1.0.3; the proxy itself is unaffected by the 1.0.4 changes, only a version guard is needed)
- **Primary test GPU: RX 5700 XT (RDNA1)**
- **AMD only** — this proxy does not work on NVIDIA or Intel GPUs
- **Phase 0 (measurement harness): overlay proven in the Azimuth launcher menu** on 2026-10-30 — Perf v3.0 panel, F9 hide, proxy v3.0.0, dxgi passive, VRAM readout. Menu numbers only, not in-game performance yet.
- **Phase 1: built.** Remaining before a tester pack: a driven in-game CSV benchmark gate, the 1.0.4 version guard, and the tester pack itself.
- **No release yet.** No DLL is shipped in this repository. Install instructions below apply once a release exists.

## What it does

- Stats overlay (FPS, 1% lows, frame time, PSO misses, VRAM, barrier count, affinity mask, proxy version) — off by default
- CSV benchmark export and a built-in benchmark mode (fixed route, fixed duration, fixed settings)
- Hitch detection (frames over 33 ms, logged with cause: PSO miss, barrier, streaming, audio)
- Version and GPU stamp in every log (Azimuth version, proxy version, GPU, driver, settings)
- PSO miss logging and barrier count measurement

Phase 1 adds the core patch update: mirror and reflection profiles, a PSO cache that survives Azimuth updates, safe pass-through fallback (proxy failure falls back to the real D3D12, never a crash), a version guard, and per-GPU profiles.

## Docs

- [V3 Roadmap — everything, in order](docs/AMD_Patch_V3_Roadmap.md) — phases, gates, and the explicit do-not-build list
- [Full ideas list (65+ ideas)](docs/AMD_Patch_Ideas_Full_List.md) — the backlog V3 is drawn from
- [Old versions](docs/OLD_VERSIONS.md) — v1.1, v2.0, the Azimuth 0.9.9 patch, and an unlabelled early build

## Third-party code

- `third-party/FH2-FSR4` is a git submodule pointing at aranhoso's FH2-FSR4 (FSR 4.1.1 Native AA for FH2 on Azimuth, MIT licensed). It is NOT bundled code in this repo — it is referenced as a submodule so its own LICENSE stays intact. V3's temporal work may reuse that MIT code with attribution, or link to it, once Phase 3 prototyping starts. It is not part of Phase 0/1.

## Testers

- FSR4-hardware testing: Refil (volunteered 2026-10-30) — needed because the main test rig is RDNA1 and cannot verify FSR 4 as intended on its own.
- RDNA2/3/4 testing: WireShark, LucksterYo (first in line for the tester pack).

## Install (when a release exists)

Drop-in `d3d12.dll` next to `azimuth.exe`. Uninstall = delete that file. No registry changes, no overwriting Extra Options, drive maps, or saves.

## Compatibility notes

- Extra Options 0.3.2 and FH2 Drive Maps are compatible with the proxy.
- FH3 must run in its own separate Azimuth install — Extra Options makes FH3 crash when shared with FH2. That is an Extra Options issue, not a proxy fix.
