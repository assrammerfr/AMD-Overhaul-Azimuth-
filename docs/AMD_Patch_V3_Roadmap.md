# AMD Azimuth Patch V3 — Roadmap: Everything, In Order

Project decision, 2026-10-30: do everything brainstormed, as V3.
Interpretation, stated plainly so it doesn't become a trap: "everything" = the full 65-idea backlog, worked in phases, with a measurement gate on each idea. An idea that fails its measurement gets cut, not forced in. Forcing 65 features into one build = bloat, crashes, and no way to tell what helped. V3 ships as the survivors.

Source list: `AMD_Patch_Ideas_Full_List.md` (65 ideas, numbers referenced below).
Builder: Freebuff (prompts are sent by the project owner, Muse writes them and verifies).
Test rig: RX 5700 XT, Azimuth 1.0.3, separate test install — never the live 1.0.2 folder.
Other-GPU testers: WireShark, LucksterYo (RDNA2/3/4).

---

## Phase 0 — Measurement harness (build FIRST, no perf features yet)

Without this, every later claim is vibes. This phase is non-negotiable.

- #39 Stats overlay (FPS, 1% lows, frame time, PSO misses, VRAM, barrier count, affinity mask, proxy version) — off by default
- #40 CSV benchmark export
- #41 Built-in benchmark mode (fixed route, fixed duration, fixed settings)
- #37 Hitch detector (log frames >33ms with cause: PSO miss / barrier / streaming / audio)
- #45 Version + GPU stamp in every log (Azimuth version, proxy version, GPU, driver, settings)
- #18 PSO miss logging
- #3 Barrier count measurement (measure only in Phase 0 — batching itself is Phase 1, gated on this count)

Gate to leave Phase 0: a timed benchmark run produces a CSV + log that two different people can reproduce. No gate, no Phase 1.

## Phase 1 — Core 1.0.3 (the actual patch update people asked for)

- #1 Mirror / reflection profile (full / half-rate / mirror resolution scale)
- #2 Reflection quality split (car mirrors vs world vs water)
- #15 PSO cache that survives Azimuth updates (GPU + Azimuth build versioning, recompile only changed)
- #47 Safe fallback / pass-through (proxy failure = real d3d12, never a crash)
- #48 Version guard (warn on untested Azimuth versions)
- #46 Per-GPU profiles (5700 XT, 6700 XT, 7900 XT, 9070 XT — auto-detect, no one-size-fits-all)
- #4 Descriptor heap split — GATED on Phase 0 profiling. If heap pressure is low, cut it.
- #5 Command allocator tuning — GATED on allocator churn measurement.
- #21 Affinity toggle — off by default, ini toggle, auto-detect CCX, log mask. Set once at load, never on a timer.
- #22 Render-thread pinning
- #23 Background thread isolation (audio/IO to other CCX)

Gate: 1.0.3 timed benchmark, proxy vs no-proxy, mirror on High/Ultra, 1% lows reported. If a feature doesn't move 1% lows, it doesn't ship in V3 core.

## Phase 2 — Smoothness (finishing V2's unfinished half)

- #31 Real XAudio2 (real source voices, 3-4 buffer queue, WASAPI 48kHz)
- #32 X3DAudio basic (distance + panning)
- #33 Audio buffer depth toggle
- #34 Audio device resilience (endpoint change mid-game)
- #36 Frame pacing smoothing (test against Azimuth fixed vs real-time pacing)
- #6 Low-latency present mode (reduced queue depth — trade vs micro-stutter, measure both)
- #7 Flip model / tearing control (only if Azimuth's present path is actually weak)
- #38 Menu vs gameplay frame cap split
- #25 SMT on/off per-process test (physical cores only vs SMT included — measure, don't assume)
- #26 Timer resolution helper (set while game runs, restore on exit)
- #24 Priority toggle (off by default, test against Windows Game Mode)

Gate: audio stutter test (long session, no underruns) + 1% low test. Audio and FPS are judged separately.

## Phase 3 — Image quality / streaming

- #10 Spatial upscaler at present (FSR 1 / CAS / NIS — spatial-only, NOT FSR 3.1)
- #11 Sharpening pass (CAS, tunable — watch LucksterYo's pixelated-lines complaint)
- #12 LOD / mip bias toggle
- #13 Texture streaming priority (near / in-town first — direct test for the in-town blurry-building issue)
- #14 Shadow map / cascade tuning (test against shadow pop-in at 1440p)
- #53 In-town streaming test (use #13 + logging to diagnose, not just to tune)
- #56 Photo mode profile (full mirrors + max quality when photo mode opens, if detectable)
- #8 Per-scene profiles (menu vs free roam vs race)

Gate: image-quality ideas need screenshots + FPS/1% low numbers, side by side. No screenshot, no ship.

## Phase 4 — Polish, safety, release

- #49 Clean installer / uninstaller (drop d3d12.dll + ini, delete to uninstall, no registry)
- #50 Mod compatibility check (EO 0.3.2 + drive maps loaded; FH3 in its OWN separate install — EO crashes FH3 unless solo, 2026-10-30, Arthur's lane, not a proxy fix)
- #51 Wine / Linux compatibility (1.0.3's launcher shader-compiler fix helps — test proxy under Wine/Proton)
- #52 README: AMD-only, no NVIDIA/Intel, up front
- #57 Tester pack (proxy + ini + README + benchmark instructions, versioned for 1.0.3 — WireShark / LucksterYo first)
- #58 Plain-language changelog
- #59 Honest known issues list
- #60 Feedback template (GPU, driver, Azimuth version, proxy version, settings, CSV)
- #42 A/B hotkey (toggle optimizations live, same session)
- #43 Hot-reload config (ini watcher, where safe)
- #44 Crash-safe logging (flush per frame batch)
- #16 Parallel pipeline precompile (startup time, not FPS)
- #17 Async / background pipeline compile (gated: fallback if PSO needed before ready)
- #20 Warm cache shipping (per-GPU, per-Azimuth-version, validated or not shipped)
- #27 VRAM budget reporting + guard
- #28 Texture pool tuning (8GB+ vs 4GB)
- #29 Staging buffer pooling
- #30 Heap reuse for long sessions
- #9 Dynamic resolution scaling (gated: only if render-scale hook is clean, otherwise cut)
- #54 Storm Island / DLC path check (proxy must not interfere with DLC mounting)
- #55 Car / thumbnail path check (mediaoverride cars still load with proxy on)

Gate: tester pack passes on 5700 XT + at least one other RDNA GPU, clean uninstall verified, no EO/drive-map breakage.

## Phase 5 — Moonshots / research only (NOT in V3 release unless proven)

- #61 Depth-derived motion estimation (for frame gen — extremely hard, probably not worth it)
- #62 ML spatial upscaler (only if fast enough on RDNA1 — not today)
- #63 DirectStorage-style prefetch (high risk, needs game IO hooks)
- #64 Multi-GPU display trick (spare 580 as display card — document as a setup guide, not a proxy feature)
- #65 Cross-game proxy (FM5 / FH3 on Azimuth 1.0.3 — test in separate installs, don't assume FH2 core transfers; EO/FH3 solo rule applies)

Gate for any moonshot: a standalone prototype with numbers, before it ever touches V3.

## DO NOT BUILD (explicit cut list)

- Intel CAT / CAT-like L3 partitioning on Ryzen — hardware doesn't exist on 3800X, AMD PQoS is EPYC 9004+ only. Affinity (#21) is the replacement.
- CAT / affinity on a timer — re-partitioning / re-pinning mid-game evicts cache and bounces threads. Set once at load.
- FSR 3.1 / frame generation in a proxy — needs motion vectors Azimuth never emits. Parked, stays parked.
- Touching Azimuth's detile `[numthreads(64,1,1)]` — already one RDNA wavefront, optimal.
- Forcing CPU settings system-wide / BIOS / power plans from a graphics proxy — out of scope, needs admin + external helper.
- Fixing EO's FH3 crash inside the AMD proxy — Arthur's lane, EO memory addresses are not a D3D12 problem.
- Hardcoded affinity masks for one CPU — auto-detect or don't ship it.

---

## How Freebuff gets this (prompting plan)

Do NOT send one giant "build all 65" prompt. Freebuff's context burns fast and loops.
Send one prompt per phase, in order, each with:
1. The Phase 0 benchmark harness first (so every later phase has numbers)
2. Its idea numbers from the full list, with gate + test metric per idea
3. "If a gated idea fails its measurement, report it cut, don't build it anyway"
4. Deliverable per phase: DLL + ini + README delta + benchmark CSV from the 5700 XT

The project owner sends, Muse verifies file state + numbers before the next phase prompt goes out.
