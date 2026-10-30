# AMD Azimuth Patch — Older Versions

All older patch versions found and verified on Seth's PC (Downloads / F:\beam), as of 2026-10-30.
This file lists versions only — it does not re-host the DLLs. The zips themselves stay where they were found (see "Where to find them" below). Do not upload a DLL to this repo without checking its licence / origin first.

**Important:** these are OLD versions. They were built for Azimuth 0.9.x-era builds. They have NOT been re-tested on Azimuth 1.0.3. V3 (see `AMD_Patch_V3_Roadmap.md`) is the 1.0.3 work. Do not install an old version on 1.0.3 and expect the V3 results.

Install for any version below (all versions): drop `d3d12.dll` next to `azimuth.exe`. Uninstall: delete that `d3d12.dll`. AMD GPUs only — this patch does not work on NVIDIA.

---

## v1 — stable line

### v1.1 — stable
- Zip: `Azimuth_AMD_Fix_v1.1.zip` — 101,161 bytes, 2026-10-12 18:10
- Contents (verified from the zip): `d3d12.dll` 201,216 bytes, `README_AMD_FIX.txt` 1,045 bytes
- Also found loose: `d3d12_v1.1.dll` — 201,216 bytes, 2026-10-12 07:26 (same size as the DLL inside the v1.1 zip)
- What it does: D3D12 proxy, PSO caching (the v1.1 caching that V2's prompt says to extend, not replace). This is the base V2 was built on.
- Where: PC Downloads (`F:\FromC\Users\seths\Downloads\Azimuth_AMD_Fix_v1.1.zip`)
- Status: v1 stable line. Use this if you need the last v1 build, not V3.

### Unlabelled early build (same README as v1.1, different DLL)
- Zip: `F:\beam\Azimuth_AMD_Fix.zip` — 100,977 bytes, 2026-10-12 05:10
- Contents (verified): `d3d12.dll` 196,608 bytes, `README_AMD_FIX.txt` 1,045 bytes
- Note: README size matches v1.1 exactly, but the DLL is smaller (196,608 vs 201,216 bytes), so this is NOT the v1.1 zip — it is an earlier, unlabelled build from the same day. Do not label it v1.1.

## v2 — stable line

### v2.0 — stable
- Zip: `AMD-D3D12-Proxy-v2.0.zip` — 112,350 bytes, 2026-10-13 09:55
- Contents (verified): `AMD-D3D12-Proxy-v2.0\d3d12.dll` 223,232 bytes, `AMD-D3D12-Proxy-v2.0\README.txt` 3,680 bytes
- What it does (per the V2 build prompt, 2026-10-13): D3D12 optimizations for AMD RDNA (descriptor heap, command allocator pooling, pipeline state caching, barrier batching), plus the XAudio2 work was planned for v2.0 with audio as v2.1 if it was too big — so do NOT assume the v2.0 zip contains the full XAudio2 implementation without checking its README.
- Where: PC Downloads (`F:\FromC\Users\seths\Downloads\AMD-D3D12-Proxy-v2.0.zip`)
- Status: v2 stable line. V2 is the version that "carried us so far" — this is the last stable before V3.

## Azimuth 0.9.9 patch

### AMD Azimuth 0.9.9 Patch
- Zip: `AMD-Az-Patch-0.9.9.zip` — 112,248 bytes, 2026-10-15 11:33
- Contents (verified): `d3d12.dll` 225,280 bytes, `README.txt` 537 bytes
- What it does (per its README): drop `d3d12.dll` into the Azimuth folder next to `azimuth.exe`. AMD GPU optimizations (async compute, PSO caching tuned for RDNA). Tested on RX 5700 XT, should work on any AMD card. FSR upscaling / frame-gen toggles exist in the config but are NOT implemented — they won't do anything.
- Where: PC Downloads (`F:\FromC\Users\seths\Downloads\AMD-Az-Patch-0.9.9.zip`)
- Note on naming: "0.9.9" here is the Azimuth version this patch was made for, not a patch version number. Do not confuse it with patch v1/v2.

---

## Where to find them

| Version | Zip file | Size | Date |
|---|---|---|---|
| v1.1 (stable) | `Azimuth_AMD_Fix_v1.1.zip` (Downloads) | 101,161 B | 2026-10-12 |
| v2.0 (stable) | `AMD-D3D12-Proxy-v2.0.zip` (Downloads) | 112,350 B | 2026-10-13 |
| Azimuth 0.9.9 patch | `AMD-Az-Patch-0.9.9.zip` (Downloads) | 112,248 B | 2026-10-15 |
| Unlabelled early build | `Azimuth_AMD_Fix.zip` (F:\beam) | 100,977 B | 2026-10-12 |

## Missing / unverified

- **No v0.5 patch file was found** in PC Downloads or F:\beam in the 2026-10-30 check. If "V.5" meant a different version (e.g. a 0.9.5 build, or a v1.5 build), send its filename and it will be added here with its verified size/date/contents — it will not be invented.
- DLL contents beyond file names/sizes were NOT re-audited for this list. The READMEs inside the zips are the source for what each version claims to do.
- V3 is not listed here — V3 is the current work, not an older version. See `AMD_Patch_V3_Roadmap.md`.
