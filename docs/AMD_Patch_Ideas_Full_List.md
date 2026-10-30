# AMD Azimuth Patch — Full Ideas List

Brainstorm for the next version after V2. V2 carried us on async compute + PSO caching.
Nothing here is built, promised, or tested — it's a menu to pick from.
Rule for all of it: measure first, build second. If the measurement says no gain, kill the idea.

---

## 1. GPU / Rendering

1. **Mirror / reflection profile (1.0.3-specific)** — 1.0.3 updates mirrors every frame on High/Ultra. Proxy toggle: full-rate, half-rate (every other frame), mirror resolution scale. RDNA1 gets perf back, RDNA3+ stays full-rate.
2. **Reflection quality split** — separate toggles for car mirrors vs world reflections vs water. Tune each independently instead of one global setting.
3. **Barrier batching** — measure barrier counts per frame first. If RDNA stalls on barriers, batch them. If counts are low, kill this.
4. **Descriptor heap split** — Azimuth uses a single 1M-entry shader-visible CBV/SRV/UAV heap + 2048 sampler heap. Try splitting to reduce RDNA page-table pressure. Measure, don't assume.
5. **Command allocator tuning** — Azimuth's pool has a 4s idle expiry. Tune pool depth / expiry for RDNA's deeper command processor queues to cut alloc/free churn.
6. **Low-latency present mode** — reduce pre-rendered frames / queue depth at present time. Anti-Lag-style, but in-proxy. Trade: lower latency, possible micro-stutter if GPU saturates.
7. **Flip model / tearing control** — clean present path, optional tear-free vs lowest-latency present. Don't touch if Azimuth already handles it well.
8. **Per-scene profiles** — menu vs free roam vs race vs photo mode get different mirror / PSO / affinity settings. Auto-switch on scene, or at least on menu vs in-game.
9. **Dynamic resolution scaling (proxy-side)** — if GPU frame time exceeds target for N frames, drop render scale a step; raise it back when headroom returns. Only useful if it can hook the game's render scale cleanly — otherwise skip.
10. **Spatial upscaler at present (FSR 1 / CAS / NIS)** — spatial-only, no motion vectors needed. This is NOT FSR 3.1 (that needs motion vectors Azimuth never emits, and stays parked). Prototype only, with a clean on/off toggle.
11. **Sharpening pass** — CAS-style sharpening after upscaling, tunable strength. Cheap, but must not oversharpen FH2's already sharp car lines (LucksterYo's pixelated-lines complaint).
12. **LOD / mip bias toggle** — slight negative mip bias for sharper distant textures, or positive bias for VRAM savings on 4GB cards. Small, measurable.
13. **Texture streaming priority** — prioritize near / in-town textures over distant ones. Ties into the FH2 in-town blurry-building diagnosis: if buildings under-load in towns, streaming priority is a real lever to test.
14. **Shadow map / cascade tuning** — if shadows pop in at 1440p (LucksterYo's shadow pop-in report), test whether proxy-side shadow resolution / distance handling helps. Game-side setting may own this — verify first.

## 2. PSO / Loading / Startup

15. **PSO cache that survives Azimuth updates** — version cache by GPU + Azimuth build, recompile only what actually changed. Kills the "new Azimuth = full Preparing graphics pipelines again" pain.
16. **Parallel pipeline precompile** — if Azimuth precompiles serially, parallelize across threads where safe. Cuts startup time, doesn't change in-game FPS.
17. **Async / background pipeline compile** — compile non-critical PSOs in the background after boot instead of blocking on the loading screen. Risk: hitches if a PSO is needed before it's ready — needs a fallback.
18. **PSO miss logging** — log which pipelines miss, when, and how long they take. Turns "stutter" into a named pipeline.
19. **Detile pass — leave the 64-thread group alone** — Azimuth's texture detiling compute shader is already `[numthreads(64,1,1)]`, one RDNA wavefront. Don't touch it. Look at *other* compute passes instead, if any are heavy.
20. **Warm cache shipping** — ship a pre-warmed PSO cache for common GPUs (5700 XT, 6700 XT, 9070 XT) so first run isn't the worst run. Must be validated per Azimuth version or it backfires.

## 3. CPU / Scheduling

21. **Optional affinity toggle (the CAT idea, AMD way)** — auto-detect CCX/CCD layout at load, pin game to one CCX, log the mask. Off by default, ini toggle. No CAT hardware needed, no timer re-pinning.
22. **Render-thread pinning** — pin the D3D12 render thread specifically, not just the whole process. Less blunt than process affinity.
23. **Background thread isolation** — audio, IO, and Azimuth worker threads to the *other* CCX, so they don't steal L3 from the render thread.
24. **Priority toggle** — High priority class for the game process, off by default, logged. Test against Windows Game Mode — don't stack both blindly.
25. **SMT on/off per-process test** — on Ryzen, test game with SMT threads excluded from the affinity mask vs included. Some games prefer physical cores only.
26. **Timer resolution / frame pacing helper** — set a sane timer resolution while the game runs, restore on exit. Tiny, but it affects frame pacing consistency.

## 4. Memory / VRAM

27. **VRAM budget reporting + guard** — log VRAM used vs available, warn before it caps. On 8GB RDNA cards this is headroom info, on 4GB cards it's a survival tool.
28. **Texture pool tuning** — if FH2 lets the proxy influence texture pool size, test a larger pool on 8GB+ cards, smaller on 4GB. Don't guess — measure town / high-speed streaming.
29. **Staging buffer pooling** — reuse upload/staging buffers instead of alloc/free per frame. Same class of win as command allocator pooling, GPU-side.
30. **Memory defragmentation / heap reuse** — long sessions fragment GPU heaps. Periodic reuse (not timer-flushed, just pooled) could help long free-roam sessions.

## 5. Audio (V2's unfinished promise)

31. **Real XAudio2 implementation** — replace Azimuth's stubbed XAudio2 with real source voices, 3-4 buffer queue depth, proper WASAPI handoff at 48kHz. This is the stutter fix, not an FPS fix.
32. **X3DAudio basic implementation** — distance attenuation + panning, even a basic version beats a stub for positional engine sounds.
33. **Audio buffer depth toggle** — let testers tune buffer depth (low latency vs no underrun) instead of hardcoding one value.
34. **Audio device resilience** — if the audio endpoint changes mid-game (headset plug/unplug), don't crash or go silent — re-route cleanly.

## 6. Frame Pacing / Smoothness

35. **1% low focus, not average FPS** — every idea above should be judged on 1% lows first. V2's win was there, not on averages.
36. **Frame pacing smoothing** — if Azimuth's frame_pacing is fixed vs real-time, test proxy-side pacing help for the real-time path.
37. **Hitch detector** — log any frame over, say, 33ms with what was happening (PSO miss? barrier? streaming? audio?). Turns "it hitched" into a cause.
38. **Menu vs gameplay frame cap split** — menus don't need 60fps GPU burn. Cap menus lower, save thermals, no gameplay cost.

## 7. Diagnostics / Tester Tools (boring, high value)

39. **Built-in stats overlay** — FPS, 1% lows, frame time graph, PSO misses, VRAM, barrier count, affinity mask, proxy version. Toggleable, off by default.
40. **CSV benchmark export** — timed run, auto-export FPS / frame times to CSV. Makes tester reports comparable.
41. **Built-in benchmark mode** — fixed route / fixed duration / fixed settings, one hotkey or ini flag. This is the timed-run idea, built into the patch.
42. **A/B hotkey** — toggle proxy optimizations live, same session, same scene. Tester can feel the difference without restarting.
43. **Hot-reload config** — watch the ini file, apply toggles (mirror rate, affinity, overlay) without restarting the game where safe.
44. **Crash-safe logging** — flush log on every frame batch, so a crash still leaves a last-known state. Helps the "it just crashed" reports.
45. **Version + GPU stamp in every log** — Azimuth version, proxy version, GPU, driver, settings. No more guessing what a tester was running.

## 8. Compatibility / Safety / Installer

46. **Per-GPU profiles** — auto-detect 5700 XT (RDNA1), 6700 XT (RDNA2), 7900 XT (RDNA3), 9070 XT (RDNA4), load the right tuning. No one-size-fits-all.
47. **Safe fallback / pass-through** — if the proxy fails to load or a hook fails, pass through to real d3d12 instead of crashing. A broken patch must never be worse than no patch.
48. **Version guard** — check Azimuth exe version on load, warn if untested (like 1.0.3 was for a 0.9.9 proxy), log it, don't silently run mismatched.
49. **Clean installer / uninstaller** — drop d3d12.dll + ini next to azimuth.exe, uninstall = delete those two files. No registry, no overwriting EO / drive maps / saves.
50. **Mod compatibility check** — verify the proxy doesn't break Extra Options, drive maps, or other mods. Test with EO 0.3.2 + drive maps loaded, since that's the live setup. Note 2026-10-30: EO itself makes FH3 crash unless FH3 is in its own separate Azimuth install — so cross-game testing must use separate installs, never a shared EO/mods folder.
51. **Wine / Linux compatibility** — Azimuth 1.0.3 removed the launcher shader-compiler requirement (Wine fix). Test the proxy under Wine/Proton too, for Linux testers. Don't break it with Windows-only calls.
52. **No NVIDIA, no Intel — say so clearly** — proxy is AMD-only. README must say that up front, so NVIDIA testers don't waste a download.

## 9. FH2-Specific

53. **In-town streaming test** — use the proxy's streaming priority + logging to test the in-town blurry-building issue directly. If it's a streaming budget problem, this is where it shows.
54. **Storm Island / DLC path check** — ensure the proxy doesn't interfere with DLC mounting (Storm Island travel is already fragile on the Xbox Live check — don't add another variable).
55. **Car / thumbnail path check** — ensure mediaoverride cars (FM5 imports, M4 CS, etc.) still load with the proxy on. Graphics proxy shouldn't touch gamedb, but verify.
56. **Photo mode profile** — photo mode can afford full-rate mirrors + max quality, since it's not racing. Auto-switch when photo mode opens, if detectable.

## 10. Community / Release

57. **Tester pack** — zip with proxy + ini + README + benchmark instructions, versioned for 1.0.3. Send to WireShark and LucksterYo first, public thread second.
58. **Changelog in plain language** — what changed, what to test, what to report (FPS, 1% lows, mirror setting, GPU). No "various improvements."
59. **Known issues list, honest** — FSR toggles not implemented, NVIDIA not supported, first-run PSO compile still slow, etc. Don't let testers discover them.
60. **Feedback template** — GPU, driver, Azimuth version, proxy version, settings, benchmark CSV. Paste-ready for the fh2-mods thread.

66. **FH2-FSR4 Native AA research (added 2026-10-30, from Refil)** — FH2Mods mod by aranhoso, v0.1.0, FSR 4.1.1 Native AA via dxgi.dll proxy, replaces MSAA with temporal AA at native res, NOT upscaling, NOT an FPS boost (mod page says so explicitly). Claims motion vectors reconstructed from FH2's motion-blur data — if true, that unblocks temporal work we thought was dead. UNVERIFIED on RDNA1 5700 XT: official FSR4 ML is RDNA4 (RDNA3 added in 4.1.1, older GPUs fallback to FSR 3.1.5 per GPUOpen). Conflict risk: it's a dxgi.dll proxy, AMD patch Phase 0 is also dxgi — can't run two dxgi.dlls side by side without chaining. Seth 2026-10-30: aranhoso's code is MIT licensed, so reuse is allowed with copyright + licence notice kept in — that also solves the proxy conflict, by integrating his code into V3's single proxy instead of running two proxies. Refil volunteered as FSR4-hardware tester.

## 11. Moonshots (do last, or never)

61. **Depth-derived motion estimation** — FSR 3 frame gen needs motion vectors. Could a proxy *estimate* them from depth + previous frame? Extremely hard, probably not worth it — listed so it's not forgotten, not so it's promised.
62. **ML spatial upscaler** — if a lightweight spatial ML upscaler ever runs fast enough on RDNA1, that's a post-process candidate. Not today.
63. **DirectStorage-style prefetch** — prefetch game assets ahead of the player (high-speed driving, town entry) to cut streaming hitches. Needs game IO hooks — high risk.
64. **Multi-GPU display trick, done right** — spare 580 as display card, main card for compute/render. That's the iGPU-thread idea, with real hardware. Document it as a setup guide, not a proxy feature.
65. **Cross-game proxy** — if the proxy works for FH2 on Azimuth, does the same core work for FM5 / FH3 on Azimuth 1.0.3? Test, don't assume. Those titles got mirror/cockpit fixes in 1.0.3 too.

---

## Pick order (my recommendation)

1. Mirror profile (#1) — 1.0.3-specific, measurable
2. Stats overlay + CSV export (#39, #40) — makes every other test real
3. Affinity toggle (#21) — the CAT idea, AMD way
4. PSO cache survival (#15) — kills startup pain
5. Real XAudio2 (#31) — finishes V2's unfinished half

Everything else is after those five prove out.
