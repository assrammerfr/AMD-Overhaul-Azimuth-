# AMD-Overhaul-Azimuth-
Hello there. i am SSTG, i have a passion for games and tech and i noticed AMD was a bit out of touch for azimuth since ver 9.7 and i just havent got to making a github for this. well ENJOY !!!

## What this is

AMD Azimuth patch, V3. A drop-in D3D12 proxy for Forza Horizon 2 on Azimuth, tuned for AMD RDNA GPUs.

V2 carried us on async compute + PSO caching. V3 is the full overhaul, worked in phases with a measurement gate on every idea — if an idea fails its measurement, it gets cut, not forced in.

- Target: Azimuth 1.0.3
- Primary test GPU: RX 5700 XT (RDNA1)
- AMD only — this proxy does not work on NVIDIA or Intel GPUs
- Status: Phase 0 (measurement harness) in progress

## Docs

- [V3 Roadmap — everything, in order](docs/AMD_Patch_V3_Roadmap.md) — phases, gates, and the explicit do-not-build list
- [Full ideas list (65+ ideas)](docs/AMD_Patch_Ideas_Full_List.md) — the backlog V3 is drawn from

## Third-party code

- `third-party/FH2-FSR4` is a git submodule pointing at aranhoso's FH2-FSR4 (FSR 4.1.1 Native AA for FH2 on Azimuth, MIT licensed). It is NOT bundled code in this repo — it is referenced as a submodule so its own LICENSE stays intact. V3's temporal work may reuse that MIT code with attribution, or link to it, once Phase 3 prototyping starts. It is not part of Phase 0/1.

## Testers

- FSR4-hardware testing: Refil (volunteered 2026-10-30) — needed because the main test rig is RDNA1 and cannot verify FSR 4 as intended on its own.

## Install (when a release exists)

Drop-in `d3d12.dll` next to `azimuth.exe`. Uninstall = delete that file. No registry changes, no overwriting Extra Options, drive maps, or saves.
