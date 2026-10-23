# The Godfather II — research notes

Checked: 2026-10-20. Goal: the PC story campaign playable in co-op.

Claim issue: <https://github.com/cranebeaker/coop-anything/issues/78>.

## Identity

- **Observed:** PC version; story campaign co-op is the goal.
- **Game:** The Godfather II (2009), developed by EA Redwood Shores; PCGamingWiki identifies the engine as Visceral Engine.
- **Store / package ID:** unknown. The clean candidate executable reports `1.0.764.0`; its SHA-256 is kept only in ignored `local/identity.md`.
- **Target-build decision (2026-10-20):** after SE v0.56 was proven incompatible with `1.0.764.0`, the tested build switched to `1.0.789.0`, taken from the SE v0.56 bundle - an unverified third-party executable, used only as a disposable test target and not a build to follow. The `1.0.764.0` isolated copy is kept only as a reference; iteration happens in new isolated copies (currently `%LOCALAPPDATA%\Temp\opencode\godfather2-test-se`, whose original install source stays untouched). The `1.0.789.0` MPR bundled exe remains a separate, byte-different `.789` build: its existing decompilation (33,506 functions) matches the MPR exe, not the SE exe, and therefore cannot be used as the analysis base for SE-offset work. The `1.0.789.0` executable came from the Multiplayer Revival archive and is not the target build.
- **Target OS:** Windows, based on the supplied Windows game install. The original install and saves were not modified.
- The Nexus Multiplayer Revival page says it requires PC build `1.0.789.0`, which is the MPR-bundled executable. It does not establish compatibility with the clean `1.0.764.0` target; do not install or use MPR on that target.

## Existing work

### Multiplayer Revival (not story co-op)

- Page: <https://www.nexusmods.com/thegodfather2/mods/95>, by `paindubreak`.
- The author says it restores the original PC online multiplayer using a community-hosted matchmaking server. Players host matches; game traffic is peer-to-peer. The page describes lobbies, quick join, and host/join, not campaign co-op or shared story state.
- Compatibility stated by the author: PC version `1.0.789.0` and an ASI loader. The page header says version 1 while its file listing says version 2 (last uploaded 2026-08-06). No Nexus archive was downloaded; a pre-existing copied `.asi` was hashed and statically analyzed, but not matched to an archive hash.
- The page lists zero formal bugs, but its posts include connection/firewall trouble and a report of a 25-FPS limit. Its Aug. 6 author reply says the archive includes an ASI loader and a Godfather II executable; inspect any package contents and redistribution terms before using it. No mod was downloaded or installed by this session.
- The original online service is listed as shut down by PCGamingWiki. Its page describes online play for up to 16 and custom-match self-hosting. Contemporary coverage describes discrete map-based multiplayer modes, including team deathmatch, rather than a shared campaign: <https://www.ign.com/articles/2009/03/18/the-godfather-ii-gets-multiplayer-mode>.
- **Not verified:** successful connection, exact gameplay sync, NAT behavior, or whether any mode offers co-op against AI.

### The Godfather II SE (a promising modding hook, not co-op)

- Page: <https://www.nexusmods.com/thegodfather2/mods/40>, by `Greavesy` / `Greavesy899`.
- The latest listed release is `v0.56` (updated 2026-08-26). Its page describes a script extender/mod menu, loose-file mod loading, spawning NPCs/items/vehicles, loading checkpoints, and SimGroup overrides. This is the strongest hook found so far for a separate co-op plugin, but none of those features proves campaign replication is available.
- The page says its archive includes a DRM-free executable. No source link or license for the extender was found; do not redistribute its executable or assume its code can be reused.
- The Nexus v0.56 file manifest lists `GF2ASI.asi`, `dinput8.dll`, `discord_game_sdk.dll`, SimGroup mod data, and a bundled `godfather2.exe` (manifest SHA-256 `31f579a80cfd14f42c0379bbfa5b5b7f151bb06ec5163593023ab5002b1f1286`).
- The SE v0.56 archive was supplied for testing; its SHA-256 (`653d037000801abf45133d7f1a011749c71c83c529336b799feb5aeb0b90253f`, 6,055,436 bytes) matches the Nexus manifest's archive hash, and all 7 extracted files match the manifest per-file SHA-256 values. The bundle is authentic to Nexus; nothing was re-hosted.
- Inspection of the verified archive: the bundled `godfather2.exe` reports file version `1.0.789.0` (13,840,384 bytes). This differs byte-wise from the Multiplayer Revival bundled exe (same version and size, SHA-256 `d0339a3d79e73ee8ec542cda79608e8194f0f68d916b74115c59dce0267ae97f`), so each mod ships its own `.789` build. `simgroup_mods/README.txt` documents that the loader injects SimGroup override behaviours (`.sgp` files from VisceralToolkit) when the game loads a SimGroup `.str` stream; `gf2asi_settings.ini` documents VSync/frame-pacing and brightness/contrast fix hooks. Neither file states a supported build range beyond the bundled exe.
- **Compatibility verdict:** SE v0.56 requires build `1.0.789.0` and is therefore incompatible with the clean `1.0.764.0` target. Per the stop rule, it was not installed on the clean copy; it was only downloaded, hash-verified, and extracted into a separate inspection folder (`%LOCALAPPDATA%\Temp\opencode\godfather2-tools\inspect\se-v0.56`).
- The Nexus bug badge showed 3 when checked. Its visible list included a crash when making Michael Corleone a crew member (reported against v0.34, last post 2026-09-29), a v0.56 issue marked fixed (last post 2026-09-03), and older "not working" / startup reports. These are mod reports, not tests of co-op.
- A Sept. 25, 2026 post also reports a startup crash with an "api doesnt work" message; the author asked for the full error and installed-mod list on Sept. 28. No resolution was visible when checked.

### Related engine tooling

- <https://github.com/Greavesy1899/VisceralToolkit> describes WIP tools for inspecting/editing Visceral games, including The Godfather II. It may help inspect stream assets, but is not a co-op framework; GitHub reports no repository license. Its contents have not been cloned or built.
- The toolkit wiki says Godfather II uses Version 12 `.str` stream files; PC loading and saving are supported, editing is partial, and adding new entries is unsupported. Its resource-type notes identify navigation graphs, MetaModels, and a `VariableDictionary`, but mark most formats unknown and the pages as incomplete. These are leads, not verified script/quest APIs: <https://github.com/Greavesy1899/VisceralToolkit/wiki/File-Resource-Type-Support> and <https://github.com/Greavesy1899/VisceralToolkit/wiki/Resource-Types>.
- PCGamingWiki lists Dead Space 3 on the same named Visceral Engine and documents a two-player online co-op campaign: <https://www.pcgamingwiki.com/wiki/Dead_Space_3>. This is a useful engine-family reference, not evidence that its networking code or tools can be reused in The Godfather II.

## Static binary observations (isolated copies; no binaries published)

- The MPR-bundled `1.0.789.0` `godfather2.exe` is PE32 x86 and imports `WS2_32.dll` and `DINPUT8.dll`, among other Windows APIs. This confirms networking APIs in that bundled executable, not in the verified clean `1.0.764.0` build or that the single-player campaign uses multiplayer replication.
- The clean `1.0.764.0` copy has PE sections named `.securom`, `Stext`, `Sitext`, `Srdata`, `Sdata`, and `Sidata`; it also includes `paul.dll`. PCGamingWiki lists SecuROM 7.38.0015 for the retail PC release. Treat it as DRM-protected; exact store provenance remains unknown.
- The Multiplayer Revival `Multiplayer.asi` is also PE32 x86. Its imports include Winsock (`WS2_32.dll`) and INI-reading APIs; embedded strings mention FESL redirection, `Multiplayer.ini`, `Multiplayer.log`, and MinHook. Ghidra headless decompiled 84 functions. Its DLL entry path calls `SetProcessDEPPolicy(0)`, writes NOPs at absolute addresses `0x006A2B5A` and `0x0068D1B2`, then starts a worker thread at plugin address `0x6B3417D0`. These patches and the worker behavior have not been mapped/validated against the target executable. The plugin derives its INI/log paths beside its own module, so the effective path depends on where the ASI is loaded. **Do not launch this plugin yet:** its DEP change has not been assessed in a sandbox. No runtime test was run.
- File inventory of the MPR-bundled `1.0.789.0` copy found 2,304 loose `.str` files, including paths under the Cuba mission/objective hierarchy; no `.lua`, `.script`, `.sc`, or `.sco` files were found. Its `.db` files are `Thumbs.db` and its `.xml` files are EA Help docs. The sampled `GODFATH0.viv` begins with `BIGF`; sampled Version 12 `.str` files begin with `3slo`. The VisceralToolkit wiki says these streams hold varied resources and most formats remain unknown, so this does not establish where story scripts execute.

## Runtime isolation check

- Windows Sandbox was enabled after explicit approval. A smoke test confirmed the guest had its own `WDAGUtilityAccount` profile/Documents path, the clean game copy was mapped read-only, and networking was disabled.
- Two offline boot probes on the clean `1.0.764.0` copy: `godfather2.exe` started and remained alive for 60-75 seconds, but had no main-window title or visible window. It was responsive according to the process API, then stopped inside Sandbox. This does not prove the game reached its menu; no gameplay or network session was run. The original install and saves were untouched.
- A third probe with `VGpu` enabled showed identical behavior (process alive, no visible windows for the game PID), so the blank window is not explained by the disabled virtual GPU. The vGPU run was a single approved test with networking still disabled.
- A fourth probe that scanned **all** guest windows found the real cause: the game process was stuck behind a Windows system-error dialog, `godfather2.exe`, reporting a missing `d3dx9_40.dll`; the process showed 0 CPU seconds while waiting, and exited with code `-1073741515` (`0xC0000135`, STATUS_DLL_NOT_FOUND) when the dialog was dismissed. A follow-up Sandbox run then reported a missing `XINPUT1_3.dll`. Both DLLs belong to the legacy DirectX 9.0c runtime (DirectX End-User Runtimes, June 2010), which a clean Windows Sandbox guest does not include. The earlier "alive process with no window" observations were this error dialog waiting on OK, and the visible-window filter did not attribute the dialog because it belongs to a different PID (744). The Windows 11 host itself runs the game, so its DirectX runtime masks this outside the Sandbox.
- With explicit approval, the DirectX End-User Runtimes (June 2010) full package was downloaded directly from the Microsoft download center link published on their page (version 9.29.1974.1, 100,275,120 bytes, SHA-256 `053f76dcbb28802e23341b6a787e3b0791c0fa5c8d4d011b1044172dbf89c73b`; the same hash is recorded independently in the winetricks repository discussion and other checksum databases). Only the two needed DLLs were taken from its cabins (`d3dx9_40.dll` 9.24.950.2656 from `Nov2008_d3dx9_40_x86.cab`; `XInput1_3.dll` 9.18.944.0000 from `APR2007_xinput_x86.cab`); they are staged for the guest's game folder rather than system-installed, so nothing in the Sandbox guest's OS is modified beyond the disposable test copy. An SE `.789` boot test with these DLLs staged is the active check.
- Disassembly confirmed the check chain (`local/ghidra_output/disasm_4c7b40_607990.txt`). Device init is `FUN_00607990`; the under-spec gate is the per-adapter GPU score from `FUN_0060f390` → `FUN_0060efc0` (9 = pass; 3 = `GetDeviceCaps` failure → "Could not initialize display hardware"; 7 = `PixelShaderVersion < 0xFFFF0300`; 6 = missing raster/prim caps bits; otherwise the format scorer `FUN_0060ee70` must return 9), followed by `CMP EAX,9 / JNZ …` at VA 0x00607a0c; the raise at 0x00607b96 is `PUSH EAX` (the score), mapped by the dialog function to the "display hardware"/"minimum requirements" messages. In the disposable SE test copy only, the 6-byte conditional jump at 0x00607a0c was replaced with NOPs (byte-level record and hashes in `local/identity.md`); the pristine verified build stays unpatched and no install was touched.
- Boot re-test after the bypass (Windows Sandbox, vGPU, networking disabled): the minimum-requirements check now passes and boot stops one stage later at raise code 1 — "Could not initialize display hardware. Please restart The Godfather™ II." The game window existed from the first tick, the process stayed responsive with CPU accumulating while the dialog waited, and the Sandbox exited cleanly. Log `local/sandbox-output/gf2_se_boot.txt`, per-tick screenshots `gf2_se_boot_T20/60/120.png`. This moves the boot blocker from the capability gate to the device-creation/display path.
- A 32-bit raw-vtable D3D9 probe was built for point diagnosis (`local/dx9probe.c`, `local/dx9probe2.c` — own C sources, raw vtable calls and an official-`d3d9.h` cross-check; built with LLVM-MinGW clang as PE32 x86; every log line is flushed so partial results survive a crash). Host baseline on the real GPU: `Direct3DCreate9(32)` works, two adapters enumerated, 97 X8R8G8B8 mode entries, HAL caps report PixelShader/VertexShaderVersion `0xFFFF0300`/`0xFFFE0300`, and all four cap fields the game's scorer reads pass its thresholds; via the official `d3d9.h` interface `CreateDevice` returned `D3D_OK` with real device pointers (windowed and fullscreen, SWVP and HWVP), a bogus adapter ordinal correctly returned `D3DERR_INVALIDCALL`, and REF failed with its own error (missing reference rasterizer). Verified constant set for later work: `D3DCREATE_SOFTWARE_VERTEXPROCESSING=0x20`, `HARDWARE=0x40`, `MIXED=0x80` — an earlier probe bug passed `0x100000`, which the retail runtime treats as undefined behavior instead of a documented HRESULT. No game file, install, or save was touched by the probe.
- Probe vtable observation, resolved (both variants recorded earlier; now explained): from the object returned by `Direct3DCreate9`, a raw call to slot +0x3C returned constant `0x00010001` while a call to +0x40 with CreateDevice-shaped arguments returned `D3D_OK` and a real device. The official `d3d9.h` interface declaration settles it — `IDirect3D9` has **14** methods after IUnknown: slot 15 (+0x3C) is `GetAdapterMonitor` and slot 16 (+0x40) is `CreateDevice`; the earlier analysis had miscounted the method list. The `0x00010001` was an `HMONITOR`, not an HRESULT — a control call `GetAdapterMonitor(0)` on slot +0x3C reproduces exactly `0x00010001` on the host. Conclusion: **the game exe's +0x40 device-creation call follows the official interface layout and was correct from the start; the boot failure is a genuine D3D9 environment failure, not a game-side ABI quirk.** On the host the CreateDevice slot is owned by `C:\Windows\SYSTEM32\d3d9.dll`; in a previous host process the same slot pointed into a second module (observation recorded; container varies per process).
- The guest (Sandbox vGPU) probe result (final run, guest probe binary `d42f8b5e:`): D3D9 works through the paravirtual adapter - 1 adapter, identity reported as the host GPU under GPU-PV (model name omitted), the host-class HAL caps (PS/VS 3.0 included) are all present, and all four cap fields the game scorer reads pass its thresholds; but mode enumeration exposes exactly one 32-bit display mode ( further indices return `D3DERR_INVALIDCALL`). `GetAdapterMonitor` control on slot +0x3C returns the identical HMONITOR as on the host, confirming the layout. CreateDevice through the official slot +0x40 succeeds in the guest for windowed HAL devices - SWVP, HWVP, MIXED, and focus=NULL all returned `D3D_OK` with real device pointers - while exclusive fullscreen returns `D3DERR_INVALIDCALL` (expected against a one-mode virtual display), REF fails without a reference rasterizer, and NULLREF succeeds. Notable guest-specific environment fact: the IDirect3D9 object's CreateDevice implementation is backed not by the d3d9.dll image but by `C:\Windows\SYSTEM32\apphelp.dll` (the Application Compatibility shim engine wraps the D3D9 interface in the Sandbox guest). Conclusion for the boot failure: the patched game's "Could not initialize display hardware" (raise code 1) matches the game requesting a display/mode configuration the one-mode virtual display cannot serve - and the probe proves plain windowed HAL devices DO work in the guest.
- Launch-mode switches exist in the SE exe's own string table (read from the verified `.789` binary, no game file modified): `-windowed`, `-fullscreen`, `-resolution`, a `cmd.txt` command file, per-window settings keys `Window.Fullscreen` / `Window.FullScreen`, `Window.Width`, `Window.Height`, `Window.Top`, `Window.Left`, `Window.VSync`, `Window.Hz`, `Window.State`, `Window.Bright`, `Window.Gamma` under `settings.txt`, plus the UI strings `ClearResolutionList` / `AddResolutionOption` / "No supported resolutions available." So forcing windowed mode needs no executable patch. Boot test v3 launched the patched `.789` test copy with `-windowed` (guest-only robocopy of the disposable test folder; log overwrite per run as before).
- SE-exe decompilation (first Ghidra run, completed 2026-10-20): 33,512 functions, project `gf2_game_10789_se`, corpus `local/ghidra_output/godfather2_10789_se.c` + function index `.tsv`. The graphics-failure dialog is `FUN_0068b590` (@0x0068b590): param 2 = "Display hardware video memory exhausted", param 3 (or 1, or anything outside 2..7 other than 4..7 branch) = "Could not initialize display hardware", parameters 4..7 = "Your Graphics Card does not meet The Godfather… II minimum requirements.", then `MessageBoxW(MB_ICONERROR-family 0x40010)` and `__exit(-1)`. The game registers it once in renderer init `FUN_0068bd30` (site 0x0068beab `PUSH 0x68b590; CALL 0x0060e780`) into slot `PTR_FUN_00f15a3c`; all raises go through that slot. Decompiler-visible raise codes are only 1 (FUN_00607990 device init), 2 (12 sites, shader/target validation), and 21 no-arg calls Ghidra swallowed (20 inside FUN_004c7b40 — the technique/shader/technique validator — the presumed source of codes 4..7). Full disassembly dump of FUN_004c7b40 and FUN_00607990 was generated to recover the pushed codes; the dump is `local/ghidra_output/disasm_4c7b40_607990.txt`. Candidate patch (test copies only): redirect the registration `PUSH 0x68b590` imm32 (4 bytes at 0x0068bead) to a cave stub that swallows codes 4..7 and passes everything else to the original dialog — keeps init/VRAM failures visible and leaves FUN_0068b590 untouched for future diagnostics. Confirmed since: the 4..7 codes come from the compare `CMP EAX,9 / JNZ` after the device-init scorer call, and the smaller working bypass was NOPing that jump (see the bullets below).
- Repeat with `VGpu` enabled (same vGPU setup; network still disabled): the game again reached its own capability check and the guest screenshot shows the identical dialog, "Your Graphics Card does not meet The Godfather™ II minimum requirements."; the process exited with code `-1`. The guest logfile for this run recorded only the first observation tick (the screenshot capture turned off later scans), so the dialog evidence for this run comes from the captured screenshot plus the exit entry. Conclusion: the game's DirectX-capability check rejects the Sandbox display path in both `WARP` and `vGPU` modes, plausibly because the paravirtual adapter reports no dedicated video memory, exactly the "1998-era GPU" style criteria an RTS-era test would use. Bypassing this one check in the disposable test copy (derived from the SE-exe decompilation, test copies only, never distributed) is the next unblocking step. vGPU log: `local/sandbox-output/gf2_se_boot.txt` (overwritten by the vGPU run).
- Root-cause chain completed (2026-10-20, test copies only). **Display init:** `FUN_00607990` builds `D3DPRESENT_PARAMETERS` via `FUN_006073e0` (called with the `Window.*` globals) or falls back to `FUN_00608b50` (`memset` PP; `Windowed = (DAT_0120587f == 0)`; format `A8R8G8B8` if fullscreen else 0). Fullscreen branch min-clamps settings up to the smallest enumerated mode and then picks a fitting mode; on a one-mode virtual display it lands on `2032×1074@32`. `CreateDevice` is the official vtable slot +0x40 with `pp = 0x011d9188` (the runtime-filled `.data` BSS area — my earlier "static PP" read was an address arithmetic slip: `0x011d9188 − 0x400000 = 0x00DD9188`, beyond the section's raw data). Failure → `PUSH 1` at `0x00607b85` = "Could not initialize display hardware". **Startup settings:** `FUN_0068d010` reads `Window.Fullscreen` default **1** (`FUN_0069bc30("Window.Fullscreen",1)`; disasm `PUSH 0x1` at VA `0x0068d1d2`), `Window.Width` default `0x400`, `Window.Height` default `0x300`, `Window.Hz` default `0x3C`, and applies them via `FUN_0069e3e0` (Creates `Godfather2WndClass`) → `FUN_0068bd30` (renderer init). Cmdline `-windowed`/`-fullscreen` override the flag but alone did not save the boot — the guest failure reproduced. **Probe v5 (guest, vGPU):** added game-shaped PPs and native-mode fullscreen variants — windowed works for every shape tested (A8R8G8B8/X8R8G8B8, W/H 0 or 800×600/1024×768/2032×1074, `IMMEDIATE`, autodepth D24S8, SWVP/HWVP/MIXED), while **exclusive fullscreen fails `D3DERR_INVALIDCALL` in every variant, including at the adapter's own enumerated mode `2032×1074@32` with `hz` 0/32/60 and both formats** — the Sandbox paravirtual display cannot switch exclusive modes at all. Conclusion: the game must run windowed in the guest. **Boot v4** (test copy patched `Window.Fullscreen` default 1→0 at file offset `0x28D1D3`): the device-init dialog disappeared; new crash `0xC0000005` (WER: faulting module `godfather2.exe`, offset `0x0029F57F` → VA `0x0069F57F`). **Crash analysis:** `FUN_0069f440` is a legacy GPU-vendor detector called by `FUN_0069f8b0` (quality-options init) only when `QualityOptions.ConfigType` is absent from settings; it does `strstr(DeviceID,"VEN_")` and dereferences the result +4 **without a NULL check** — the paravirtual adapter's `DeviceID` has no `VEN_` → access violation. **Boot v6** (test copy additionally patched `FUN_0069f440` entry to `MOV EAX,4; RET` — "unknown vendor" path): **the game boots windowed and reaches its interactive language-selection screen** (English and several other European languages), stays responsive with CPU accumulating for the full 160-second harness window, screenshots `gf2_se_boot_T20/60/120.png`. Guest `settings.txt`/`cmd.txt`/`joypad.txt` were absent after the run; the SE loader wrote an empty `GF2SE.txt` marker. Both patches exist **only** in the disposable test copy (`godfather2-test-se`), whose current SHA-256 is `201269a2…`; the pristine verified bundle stays untouched.
- **Milestone (2026-10-20 23:27): the game reaches its main menu inside Windows Sandbox.** After a manual language choice in the Sandbox window (the language picker could not be confirmed by injected input), the main menu appeared with `Resume Game / Play Online / New Game / Options / Unlocks / Credits / Bonus Materials / Select Profile / Quit to Windows` and version `1.0.789.0` bottom-left. Screenshot `local/sandbox-output/seq_T150.png` (run v10, log `gf2_se_boot.txt`). Input findings from those runs: the window proc `FUN_0069de10` forwards mouse messages to `FUN_0069eb50`; `WM_MOUSEMOVE` moves the menu highlight only in relative/delta mode (`DAT_0112a065` capture flag), `WM_LBUTTONDOWN` is unhandled, and `WM_LBUTTONUP` toggles mouse capture via `FUN_0069e8b0` — injected clicks moved the highlight to Polski without confirming. The guest's OS cursor is pinned by the Sandbox RDP input path (`SetCursorPos` has no effect; `GetCursorPos` stayed 515,410), so a real host click is the reliable input path for now; automated input for QA will need a different channel.
- **Settings and saves location resolved (2026-10-20).** The game does not keep `settings.txt` in the game folder: it lives in `%LOCALAPPDATA%\EA Games\<localized title>\settings.txt`, where the title folder is a localized name. Format is plain text `Key = value` lines with CRLF; the staged settings file (871 bytes) contains `Window.Fullscreen = false`, `Window.Width = 1280`, `Window.Height = 720`, `Window.VSync = 0`, `Window.Gamma = 0.80000001`, `QualityOptions.ConfigType = 4`, plus audio/controls/quality keys. This is the file-based equivalent of both test-copy patches: `Window.Fullscreen = false` removes the exclusive-fullscreen attempt, and `QualityOptions.ConfigType = 4` (non-zero) skips the crashing GPU-vendor detector in `FUN_0069f440`. Campaign saves live in `Documents\EA Games\<localized title>\`: `profiles.dat` (1296 bytes; magic `9A E8 0D 1D`, version 4, one profile entry) and a `<profile-id>` folder (`3280000`) with localized save files and an autosave. Copies of the settings and saves were staged (originals untouched) under `local/sandbox-input/gf2profile/`, and the sandbox boot script now copies them into the guest profile under both the English and localized title folder names before launch. With the staged settings + profile, the language picker no longer appears and the main menu shows an active **Resume Game**; screenshot `local/sandbox-output/seq_T90.png` (run v12).
- **ASI plugin hook validated (2026-10-20).** The SE bundle's `dinput8.dll` is the Ultimate ASI Loader (UTF-16 strings `ASI Loader`, `*.asi`, `scripts\`, `\modloader\modloader.asi`). A minimal test plugin (`local/cooptest.c` → `cooptest.asi`, PE32 i686, DllMain writes one marker line, no hooks) was placed in the disposable test copy's `scripts\` folder. Boot run v12 produced `local/sandbox-output/cooptest_loaded.txt`: `cooptest.asi loaded; game=C:\Games\GF2Test\godfather2.exe; module=C:\Games\GF2Test\scripts\cooptest.asi` — **the loader loads third-party ASI plugins from `scripts\`**, which is the injection point a co-op plugin can use (same mechanism the SE extender and the Multiplayer Revival ASI rely on).
- **Milestone (2026-10-21 00:08): campaign gameplay running inside Windows Sandbox.** Root cause of the disabled `Resume Game`/unknown profile was the game locale: the game writes `Locale = en` into its settings on first run, while the staged saves use a different locale. After adding the `Locale` key with the saves' language to the staged `settings.txt` (and keeping the staged `profiles.dat` and save folder in the localized `Documents\EA Games` folder), the profile was picked up and the game loaded straight into the campaign - screenshot `local/sandbox-output/seq_T60.png` shows in-game HUD (money, portrait, health bar, a crew NPC, minimap) with no Cuba intro. `profiles.dat` layout: magic `9A E8 0D 1D`, version 4, profile count 1, wide profile name, profile id (the decimal save-folder name). `settings.txt` is read/written under `%LOCALAPPDATA%\EA Games\<localized title>\`; saves and `profiles.dat` under `Documents\EA Games\<localized title>\`. Locale switching selects the localized title folder. The Sandbox guest was stable through the load (responsive, CPU accumulating, no WER events).
- **Input (confirmed, 2026-10-21): keyboard menu navigation works** in the Sandbox (arrows + Enter). The mouse path stays RDP-bound (`SetCursorPos` ineffective, injected clicks unreliable), so keyboard is the intended automation channel for menus; the next test injects `WM_KEYDOWN`/`WM_KEYUP` (`VK_UP` ×8 then `VK_RETURN`) at the main menu and checks the highlight/screenshots to see whether message-injected keys are honoured or whether the engine reads real key state only.
- **Automated boot → menu → Resume Game → campaign (2026-10-21 00:25).** Run v14 proved injected `WM_KEYDOWN`/`WM_KEYUP` (posted to `Godfather2WndClass`) reach the engine and drive the UI: at 40 s the localized splash was up, the injected `VK_UP` presses dismissed it and moved the menu selection, and `VK_RETURN` at 60/80 s activated **Resume Game** — a loading screen with the city map appeared, then the mission's opening cutscene (character close-up, later a burning car), all without any manual input (`menu_after_up_4/5.png`, `menu_after_enter_6/8.png`, `seq_T240.png`, `seq_T450.png`). The game UI switched to the saves' language once the `Locale` key matched, confirming locale-controlled data folders. This gives a fully automated path into the campaign for QA runs; the next open question is whether in-game movement also accepts injected keys or only real input (DirectInput).
- **Full manual walkthrough captured (2026-10-21 01:04, run v17, 5 s frames `seq2_1..34`).** Screen sequence: black boot -> bright splash -> **main menu (localized)** -> **the profile entry** -> profile list containing exactly one profile entry (`profiles.dat` stores it as a wide string and the profile id `0x00320C80` = folder `3280000`) -> back to the main menu with **the resume item** highlighted -> **straight into gameplay in the Corleone mansion** (Dominic, `19$`, minimap, NPCs) with **no Cuba intro**. Earlier automation attempts failed because `Resume Game` was pressed without selecting the profile first, which starts the intro sequence instead of the save. The Sandbox mouse behaves oddly (RDP input) while the game is fine on the normal desktop.
- **Unattended screen-state machine (2026-10-21, run v18).** To make the boot-to-campaign path fully automatic, the guest script now classifies the screen every 5 s by a 4×4 grayscale signature of the game window region (host-side reference tool `local/screenstate.ps1`; reference signatures embedded in the script for MENU, PROFILE and SPLASH) and drives a phase machine: phase 0 splash → `VK_UP`; phase 1 menu → `VK_DOWN`×6 + `VK_RETURN` (open profile screen); phase 2 profile → `VK_UP`×2 + `VK_RETURN` (select "22"); phase 3 menu → `VK_UP`×8 + `VK_RETURN` (Resume Game); phase 4 load → no further input; phase 5 gameplay reached. Retries are bounded (max 3/4/6 attempts) and the profile step only ever sends `VK_RETURN` after the first `VK_UP` so the the create-profile button cannot be hit accidentally. Every decision is logged with the three signature distances in `local/sandbox-output/gf2_se_boot.txt`.

## Research log

### Checked

- Repository `DIRECTORY.md` and `games/`: no Godfather II entry before this note.
- Opened the required claim issue: <https://github.com/cranebeaker/coop-anything/issues/78>.
- GitHub issue and PR search for `Godfather 2`: no results from the public API. The required `gh issue list` / `gh pr list` commands could not run because GitHub CLI (`gh`) is not installed.
- GitHub repository searches for `Godfather II coop`, `godfather2 multiplayer`, and `The Godfather II mod`: no matching repositories returned.
- English web searches for Godfather II co-op campaign, co-op mod, online co-op, and multiplayer mod; checked Nexus Mods, ModDB, GameBanana, GameFAQs results, PCGamingWiki, and press coverage. The concrete results were the multiplayer restoration and modding tools above; no campaign co-op project surfaced.
- Attempted Russian, German, Italian, and Spanish searches; the search service returned HTTP 429, so those searches did not complete.

### Not checked yet

- The game's Discord/community server, Steam guides in depth, Archive.org, and full bug/issue threads outside the Nexus pages.
- The clean copy's store/package ID, the SE's detailed compatibility/API, and the original multiplayer's replication of AI/story/world state.

## Assessment and next steps

- **Derived:** the existing revival is a way to restore the game's original online match modes, not a solution for the requested story campaign. No campaign co-op attempt was found in the searches above; this is a search result, not proof that none exists.
- **Stop point:** `1.0.764.0` is the original game executable, but its PE sections and `paul.dll` indicate SecuROM protection. Ghidra analysis of this clean exe produced invalid references/function overlaps and exited `-1`; the partial decompilation is unusable and was not used. No unpacking/decryption was attempted. The `1.0.789.0` MPR executable is not a clean baseline either.
- **Next:** the target build must be chosen explicitly. The SE v0.56 archive has a hash-matching manifest, but the `1.0.789.0` exe inside it is an unverified third-party binary and requires that build, so a co-op attempt using its loader means switching the boxed target to a `.789`-based copy, while a `.764`-only path means building our own loader and losing both mods' hooks (static analysis of the SecuROM exe stays blocked either way). Whichever path is chosen, a clean Sandbox first needs the legacy DirectX 9.0c runtime (`d3dx9_40.dll`, `XINPUT1_3.dll`) installed in the guest, which needs one official Microsoft download. The claim issue is open (#78).
- **Unknown:** store (Steam, EA app, disc, or other), whether friends have matching copies, the provenance/licensing of the community `.789` builds inside SE and MPR, and the SE author's permission to reuse code. Executable hashes are kept in ignored `local/identity.md`.
- **Checks actually run:** copied the clean candidate into an isolated test folder; read executable version metadata and SHA-256; statically read PE headers/imports and printable strings; Ghidra 12.1.4 headless with Temurin JDK 21.0.12.1+1 decompiled 84 functions from Multiplayer Revival and 33,506 functions from the MPR-bundled executable. A Ghidra run on clean `1.0.764.0` exited `-1` with extensive invalid-flow/function-overlap warnings; partial output is not used. A process-only shell-folder probe showed changing `USERPROFILE` does not redirect `SHGetFolderPathW(CSIDL_PERSONAL)`. Four offline Windows Sandbox runs (two plain boot probes, one vGPU, one all-window scan) identified the missing legacy DirectX runtime behind the blank boot. The SE v0.56 archive was SHA-256-verified against the Nexus manifest and inspected; it was not installed on any game copy. No two-client or co-op test was run.

## Sources

- <https://www.pcgamingwiki.com/wiki/The_Godfather_II>
- <https://www.nexusmods.com/thegodfather2/mods/95>
- <https://www.nexusmods.com/thegodfather2/mods/40>
- <https://www.ign.com/articles/2009/03/18/the-godfather-ii-gets-multiplayer-mode>
- <https://github.com/Greavesy1899/VisceralToolkit>
- <https://github.com/Greavesy1899/VisceralToolkit/wiki/File-Resource-Type-Support>
- <https://github.com/Greavesy1899/VisceralToolkit/wiki/Resource-Types>
- <https://www.pcgamingwiki.com/wiki/Dead_Space_3>
- <https://learn.microsoft.com/en-us/windows/security/application-security/application-isolation/windows-sandbox/windows-sandbox-configure-using-wsb-file>
- <https://www.microsoft.com/en-us/download/details.aspx?id=8109> (DirectX End-User Runtimes June 2010; official source for the missing `d3dx9_40.dll` / `XINPUT1_3.dll` components identified in the Sandbox log)
- <https://github.com/Winetricks/winetricks/pull/1690> (independent record of the same SHA-256 `053f76dc…` for the Microsoft-republished June 2010 redist)
- <https://github.com/NationalSecurityAgency/ghidra/releases/tag/Ghidra_12.1.4_build>
- <https://github.com/adoptium/temurin21-binaries/releases/tag/jdk-21.0.12.1%2B1>

- **Host-based iteration (2026-10-21 ~01:30) - the working setup.** Sandbox RDP input is the bottleneck, so the iteration target moved to the **host**: the disposable copy `%LOCALAPPDATA%\Temp\opencode\godfather2-test-se` is launched directly on the test machine (1280x720 windowed, fast boot) and the game sees a pre-existing profile with its campaign saves. A driver script `local/host-drive.ps1` watches `local/host-cmd.txt` and executes commands against the game window (`ENTER/UP/DOWN/ESC/SPACE/W/A/S/D`, `VK <code> <n>`, `CLICK x y`, `MOVE x y`, `SHOT/WIN label`, `EXIT`), logging to `local/host-drive.log` and capturing frames into `local/host-shots/`. Window lookup uses `EnumWindows` + class match (on the host `FindWindowW` returns nothing). **Verified unattended route on the host:** `ENTER` (dismiss the splash notice) -> attract mode -> **mouse click on the profile entry** -> confirm the autosave notice -> **click resume** -> campaign loads (New York map, first objective "Sweet Life Bakery") with **no Cuba intro**. Keyboard alone cannot confirm the profile (focus sits on the "create profile" item, so `VK_RETURN` there would create a new profile) - the profile item must be clicked, and on the host real mouse clicks work; that is the concrete advantage over the Sandbox. Frames: `local/host-shots/003_after_enter.png` (attract), `001_after_click22.png` (autosave notice), `003_after_next2.png` (main menu), `005_loaded.png` (campaign map). A backup of the machine's EA Games folders (with SHA-256 manifest) was taken before the first host run; all five files were still byte-identical afterwards.
- **In-process hook work (2026-10-21 ~01:50), state of `local/coopcore.c` -> `coopcore.asi`.** The plugin is loaded by the SE loader (Ultimate ASI Loader) from `scripts\` and runs inside the game process: it logs its load point, keeps a heartbeat thread, and logs frame counts. Hook attempts: (a) inline hook of `FUN_00607990` (device init) **crashed the game** - that function takes arguments and the wrapper did not forward them - replaced by polling the device-pointer global `DAT_01205750`; (b) a per-object vtable hook of `IDirect3DDevice9::Present` (slot 17 of a 175-entry d3d9 implementation vtable) **also crashed the game** - copy-and-swap is unsafe here, so it was dropped; (c) an inline hook of `FUN_006080a0` installs cleanly and the game keeps running, but that function is only used on the device-reset path (`FUN_00608250`), not per frame, so the counter stays 0. The device vtable was verified to live entirely inside `d3d9.dll` (base `0x709D0000`, 175 entries), so the object is the raw D3D9 device, not an engine wrapper. Next: IAT hooks on `GetMessageW`/`PeekMessageW` for a reliable per-frame tick.
- **Scripting API found (2026-10-21 ~02:00).** The game registers a script/behavior API (`FUN_00468cb0`): `RegisterScriptName`, `GetPositionValues`, `SetPositionValues`, `GetPlayerPosition`, `TEMP_POS`, `CreatePosition`, `GetPlatform`, plus `GetPlayerInfo` and Lua hooks (`iMsgLuaGetPlayerVenue`, `FUN_00408240`). This is the natural co-op hook: the engine already exposes player position read/write to scripts, and the SE extender can inject behaviors through SimGroup `.sgp` override files.

- **Player object located (2026-10-21 ~02:40).** Method: a memory scanner inside the plugin (own code) diffs the exe's `.data` section (VA `0x00E50000`-`0x01228A38`) once per second and reports triples of neighbouring floats that change together; with the character moving in a level, world-magnitude triples appeared and were matched to the decompilation. Result: the engine keeps the **camera look-at transform - i.e. the player character - at `0x0112A840`** (`x,y,z` floats at `0x0112A840/44/48`, an angle float at `0x0112A84C`) and the **camera eye at `0x0112A850`** (`0x0112A850/54/58`, angle at `0x0112A85C`). Source in the decompilation: the camera update `FUN_006d24d0` (VA `0x006D24D0`) copies its eye/target transforms into those globals (`_DAT_0112a850 = *piVar11; _DAT_0112a840 = param_1[0xc];`) and the camera follows the actor pointer at `param_1[0x36]` (its vtable slots `+0x10`/`+0x14` return the eye/look-at transforms). The plugin now logs the player state at 5 Hz to `coopcore_player.txt` next to the game (`player x y z | lookang | cam x y z | camang`).
- **Input reality on the host (2026-10-21).** Synthetic mouse input does **not** reach the game's UI: the window grabs the mouse (`ClipCursor`), the engine reads the pointer through Raw Input, and `SetCursorPos`/`mouse_event` are ignored; `PostMessage`-based `WM_MOUSEMOVE` only moves the menu highlight while the engine is in its captured-cursor mode (`DAT_0112a065`), and `WM_LBUTTONDOWN` is not handled by the engine window proc at all. Keyboard input works in the main menu (confirmed) but not on the profile screen. Consequence for the project: menu automation must either use real input or be done from inside the process (our plugin), not through synthetic OS input.
- **Plugin build notes (2026-10-21).** `local/coopcore.c` -> `coopcore.asi` (PE32 i686, LLVM-MinGW) is staged into the disposable copy's `scripts\` folder; the SE loader (Ultimate ASI Loader) loads it. Lessons that cost crashes: hook wrappers must forward the callee's arguments; do not swap the D3D9 device vtable (175 entries) - it kills the game; read import names from the INT (`OriginalFirstThunk`) and patch the IAT (`FirstThunk`); an exception in a plugin thread kills the whole process, and MinGW has no SEH, so every pointer walk needs bounds checks; the scanner must stay inside the committed `.data` range; and the float formatter must be `sprintf`, not hand-rolled digit writing.

- **Player transform and actor pointer (2026-10-21 ~03:00).** The engine composes the player world matrix every frame in `FUN_0088d6e0` (VA `0x0088D6E0`, `__fastcall`): it reads the actor position (`*(undefined8 *)(iVar6 + 0x30)` and `*(float *)(iVar6 + 0x38)`) and stores the composed 4x4 matrix into the global `0x0112E210` (`_DAT_0112e210 = local_80[0] ... fRam0112e21c = local_80[3]`). The actor itself is `FUN_00471610(context) = context + 0xD0`, and the context is the global world/actor manager `DAT_012233A0` (which also holds an actor array at `+0x04` and a count at `+0x0C`). Consequences: **player world position = the first row of the matrix at `0x0112E210`** (x,y,z,1) and the **actor object's own position is at offset `+0x30`**; the camera look-at/eye globals (`0x0112A840`/`0x0112A850`) are only filled in some camera modes (they stayed zero in the mansion interior). The plugin now streams `matrix | actor | cam` at 5 Hz into `coopcore_player.txt` and cross-checks the matrix against `*(manager + 0xD0) + 0x30`, which is the anchor a co-op puppet needs: write the same transform into a second actor every frame.

- **Two more plugin bugs, both worth remembering (2026-10-21 ~03:40).** (1) **Trampoline off-by-one:** the jump back to the original function must be computed from `trampoline + 11`, not `trampoline + 10` - the `E9 rel32` occupies bytes 6..10, so the next instruction is at +11. With the wrong base the CPU landed in the middle of an instruction (`IN AL,DX` -> privileged-instruction fault `0xC0000096` inside `FUN_0088d6e0`); for another hook it happened to land on a harmless byte and stayed unnoticed. (2) **Every pointer arithmetic needs a bounds check**: reading `camera + 0xD8` without validating the object size raised an access violation *every frame*; the vectored exception handler swallowed it and the game hung on a black screen instead of crashing. The plugin now validates each pointer with `is_readable(ptr, need)` (committed region + the access fully inside it) before dereferencing, and the VEH only swallows up to 5000 faults and logs every 50th. With both fixed the game runs stable and the camera hook reports the followed actor (`camera follow actor = 1af83940`).

- **Main menu and campaign reached on the host (2026-10-21 02:00-04:00).** With the staged profile (settings + saves) the game reaches the main menu (build `1.0.789.0`, screenshot `local/host-shots/003_after_next2.png`) and the campaign itself (New York map with the objective "Sweet Life Bakery", then the mansion with HUD, `local/host-shots/005_loaded.png` and later frames with the character walking in the street and an NPC in conversation). No Cuba intro - the existing profile is loaded and its save resumes.
- **External inspector `local/coopscan.c` -> `coopscan.exe` (own code, PE32).** Attaches to the running `godfather2.exe` with ReadProcessMemory/WriteProcessMemory and needs no restart and no in-game hook: `find` prints the player matrix and hunts for character objects, `dump <addr>` hex-dumps an object, `list` walks the world manager, `puppet <actor> [dx]` copies the player matrix row into an actor every 16 ms. This replaced the "crash the game and restart" loop the in-process plugin had.
- **Do not write into engine memory blindly (2026-10-21 04:30).** Writing a position into the object the camera follows crashed the game: `0xC0000005` at `0x4D006874` with the faulting module "unknown" - the `+0x40` of that object holds a **pointer**, and overwriting it with floats left the engine dereferencing garbage. The camera's follow object is not the player character. The decompilation-backed path is: hook `FUN_0088d6e0` to capture its context, the player actor is `context + 0xD0` and the actor's position is at `actor + 0x30`; the plugin now does exactly that and validates every pointer with `is_readable()`.
- **Input reality, final (2026-10-21).** The engine reads the mouse through Raw Input: `SetCursorPos`, `mouse_event` and `WM_MOUSEMOVE`/`WM_LBUTTONDOWN` do not drive the UI (only the hover highlight moved in the Sandbox). The keyboard works in the main menu (confirmed) but not on the profile screen, which needs a real click. So menu automation has to come from a real device or from inside the process.
- **Game data layout (final).** `settings.txt` lives in `%LOCALAPPDATA%\EA Games\<localized title>\` (`Key = value`, CRLF, keys `Window.*`, `QualityOptions.*`, `Locale`), campaign saves and `profiles.dat` in `Documents\EA Games\<localized title>\` (profile id `0x00320C80` = folder `3280000`). The `Locale` key (`en`/`ru`) selects the language *and* the localized title folder - that is why the localized saves were invisible until the `Locale` key matched.

- **Puppet experiment (2026-10-21 00:45-01:00).** The character object of the camera target is at `0x14A17B20` with vtable `0x00D6B350`; the engine reads its position through the virtual slot `+0x48` (`(**(code **)(*(int *)(iVar6 + -0x1f30) + 0x48))(&local_b0)` in `FUN_0088d6e0`, where `iVar6 = **(int **)(DAT_012233a0 + 4)`). The world manager chain is `DAT_012233a0` -> `+0x04` (array) -> `+0x0C` (count) -> entry -> `-0x1F30` = the character object. The player's world matrix is `0x0112E210` (4x4, row 0 = x,y,z,1) and the camera look-at/eye are `0x0112A840` / `0x0112A850`. The external tool `coopscan.exe nearpos` finds every cached transform row (x,y,z,1.0) near the player; the closest hits are the player's own copies, other hits (`0x2448xxxx`) are per-character cached matrices. **Writing the player's position into such a cached matrix does not move the character** - the engine recomputes the authoritative transform from the character's kinematics every frame, and the write is overwritten. The in-process puppet (plugin hook on the camera update `FUN_006d24d0`, config `coop_puppet.cfg` with `enabled/target/offset/dz`) does keep the value in the cache (the NPC's cached row followed the player exactly), but the renderer still uses the kinematic transform, so the model does not move yet. Two sanctioned ways forward: call the character's own position setter (the getter is vtable slot `+0x48` of `0x00D6B350`, a setter is likely in the same vtable) or drive the engine's script/behavior layer, which already exposes movement commands (`moveToPos`, `moveToPathingFailure`, `combatMoveToCSARange`, ... registered around `0x004acd20`/`0x004acc70` in the decompilation) plus the script API `SetPositionValues` / `GetPlayerPosition` / `GetPlatform` (`FUN_00468cb0`).
- **Tooling built (all own code, nothing redistributed):** `local/coopcore.c` -> `coopcore.asi` (in-game plugin: 5 engine hooks, frame logger, path redirect for profile isolation, puppet writer, VEH safety net), `local/coopscan.c` -> `coopscan.exe` (external inspector: `find`, `list`, `dump`, `chars`, `nearpos`, `snap`, `diff`, `puppet`), `local/poolscan.ps1` (container dump), `local/host-drive.ps1` (host-side input/screenshot driver: keys, real mouse clicks, frames), `local/bytepatch.c` (exe byte patcher), `local/dx9probe.c` (D3D9 diagnostics).

- **Character vtable analysed (2026-10-21 01:00-01:20) - the open end for the puppet.** The camera target's class vtable is `0x00D6B350`. Slot `+0x48` is a position **getter thunk**: `81 C1 C0 03 00 00 / E9 25 52 D5 FF` = `ADD ECX,0x3C0; JMP 0x0054DBEA`, so the position lives behind `this+0x3C0` (possibly inside a sub-object). Slot `+0x44` is a boolean state getter (`XOR EAX,EAX; CMP [ECX+0x220],0; SETNE AL; RET`). Slot `+0x4C` is a dispatcher that resolves a sub-object at `this+0x1E40` (+0xB8) and calls its vtable slot `+0x4C` - a good candidate for a **setter**. The world-manager chain is `DAT_012233A0` -> `+0x04` (pointer to an array) -> `+0x0C` (count) -> entry (e.g. `0x14A19A50`) -> `-0x1F30` -> the vtable `0x00D6B350`; the call site is `(**(code **)(*(int *)(iVar6 + -0x1f30) + 0x48))(&local_b0)` in `FUN_0088d6e0`. **`this` for that call was not yet identified** - the object at `0x14A17B20` holds that vtable but its `+0x3C0` is string data, so it is a different instance. The empirical search (`local/findpos.ps1`, matches the player's position inside an object) did not find it either. Next step: identify the caller of `FUN_0088d6e0` (the `this` it passes) or hook the getter `0x00D6B350[0x48]` in the plugin, capture `this`, and then read `this+0x3C0` to learn the layout; the matching setter should sit next to the getter in the same vtable.
- **State at the end of this session:** the game runs on the test machine from the disposable copy, the campaign loads directly (no Cuba intro), the plugin is loaded with five engine hooks, the player position is read at 5 Hz, character objects and their cached transform rows are found in the heap, and an in-process puppet writer exists but the authoritative transform of a second character is still unknown. The machine's profile, settings and saves are byte-identical to the pre-session backup, and the original installation is untouched (`godfather2.exe` SHA-256 `A5632356C94A270DAB518E0E1FA569AD40FD6520C1013CF99B023E95252AD338`).

## Handoff: how to continue (2026-10-21)

Working setup (host iteration, no Sandbox needed):

1. Test copy: `%LOCALAPPDATA%\Temp\opencode\godfather2-test-se` (exe SHA-256
   `201269a29eef055c050cd1e17a4faee24df888544d8f09299bee156050c601bc`; pristine SE
   bundle untouched in `godfather2-tools\inspect\se-v0.56`).
   The plugin lives in `scripts\coopcore.asi`; the SE loader picks it up.
2. Start the game: `Start-Process %LOCALAPPDATA%\Temp\opencode\godfather2-test-se\godfather2.exe`
   (working dir = the copy). The profile/settings/saves are redirected by the
   plugin into `<copy>\profile\`, so the machine's own files stay untouched.
3. Menu path (mouse/keys are RDP-limited in the Sandbox but normal on the host):
   any key on the splash -> select the existing profile -> resume the saved game -> a mission.
4. Tools (all own code, under `local/`):
   - `local/coopscan.exe` - external inspector/writer for the running game:
     `find` (player state), `list` (world manager), `dump <addr>` (hex dump),
     `chars` (objects of the character vtable), `nearpos <radius>` (transform rows
     near the player), `snap <file>` / `diff <file>` (heap snapshots),
     `puppet <actor> [dx]` (write the player position into an actor).
   - `local/findpos.ps1 <obj>` - locate the player position inside an object.
   - `local/poolscan.ps1` - dump the character container.
   - `local/host-drive.ps1` - input/screenshot driver (keys, real mouse, frames).
   - `local/bytepatch.exe` - byte patcher for the test exe.
   - `local/dx9probe.exe` - D3D9 environment probe.
   - `local/coopcore.c` -> `coopcore.asi` - the in-game plugin (hooks, logger,
     profile redirect, puppet writer, VEH safety net).

Immediate next task (the only open end): identify the authoritative transform of a
second character. Concretely - hook the position getter `0x00D6B350[0x48]`
(`ADD ECX,0x3C0; JMP 0x0054DBEA`) in the plugin, log its `this` and the value at
`this+0x3C0`, and then look for the matching setter in the same vtable (slot
`+0x4C` looks like a dispatcher to a sub-object). Once the authoritative slot is
known, writing the player's position into it turns the NPC into a visible
"player 2" puppet - the base primitive for the networked second player.

### SE NPC Inspector as an oracle (2026-10-21 session 2)

The SE mod's ScriptHook menu has an **NPC Inspector** window. Observed on crew
member "Joe Primanti" (values read from inspector screenshots):

- `SimNPC: 1B603A00`, `Supporting Entity: 1B8F1200` - both heap handles; neither
  object's first 0x1000 bytes contain the displayed position (checked with
  `findpos.ps1`).
- Position shown with 6 decimals (e.g. `156.101730 10.222837 -220.644165`);
  it is **derived, not stored**: `findpos ALL` (full RW scan, tol 0.02) finds no
  triple anywhere in the process. Matches `FUN_0054dbf0` = `(min+max)*0.5` of an
  AABB from the node at `*(this+0x18)`, slot `+0x18`. So the authoritative state
  is the AABB min/max (or a node transform), and the displayed position is its
  center.
- `Supporting Material Type` / `Flags` change between inspections (25/1FF0000 ->
  4/1FF0000) - material under the feet, useful as a ground-truth check.

New experiment (running): `local/nearwatch.c` -> `nearwatch.exe x y z seconds r`
records every float triple within `r` of the inspector position, waits while the
the companion is walked, then reports old->new per triple. The companion's real
movement is the cluster of slots whose delta equals the movement vector between
two inspector screenshots. `local/delta-analyze.ps1` groups the report by delta.
Rationale: whatever the representation (AABB corners, node matrix row), a pure
translation shifts it by the same delta, so the diff-by-delta technique is
representation-independent.

Tooling added: `local/nearwatch.c` (watcher), `local/delta-analyze.ps1`
(delta clustering).

### Character position slots found (2026-10-21 session 2, second half)

Method that worked: `local/findtrip.exe <x> <z> <ylo> <yhi> [tol_xz]` - full RW
scan for float triples matching (x, ?, z) with a LOOSE y window. Why the earlier
searches failed:

1. `findpos.ps1` required all three components to match, but the engine stores
   the position at several heights (feet / center / head). The NPC Inspector
   displays the FEET position: inspector y=10.010 vs player matrix y=11.145
   (difference 0.945 = the feet offset). So a tight-y search can never match the
   inspector value against center-height slots.
2. `findpos ALL` had a 25 s deadline and a 64 MB region cap - it timed out before
   reaching the 0x43xxxxxx allocations where most copies live.

Observed slot families (game running, 1.0.789.0):

- PLAYER (center at matrix 0x0112E210, y=11.145 case): static .data copies at
  0x0019E670 (y = center-0.946), 0x0019E6C0, 0x0019EA40 (y = center+0.555),
  0x0019EA50 (y = center-0.555), 0x0019F370/0x0019F3E0 (z stored as z+1000!),
  0x0019F500, 0x0019F770, 0x0019F930, plus 0x0112A840 (camera look-at),
  0x01130620, 0x011F6E40, 0x012070A0, 0x012073A0 and 300+ heap copies in
  render/anim pools (0x22D5xxxx, 0x23883xxx, 0x24C13xxx, 0x3AC49xxx, ...).
- COMPANION "Joe Primanti" (SimNPC 1B603A00), inspector pos
  (30.933395 10.210006 -53.035728): 32 exact copies, none in .data - a record
  block at 0x43438958-0x4344492C plus pools 0x2270B230/0x2270D530/0x22712C30,
  0x43708C30/0x4370BF30/0x43724630. A pedestrian's family looked the same
  (5 coherent copies at 0x00199E50/0x0019A240/0x0019A250/0x0019A260/0x0019A6E0
  - these WERE in .data, so .data also holds some NPC records; the layout rule
  is still unknown).

The "delta hunt" (`local/deltahunt.c`) is the reliable way to attribute slots to
a moving character: record all RW memory, wait for a stop file, then report every
triple whose delta equals the player's (read from 0x0112E210). A companion that
walks a different path needs its own delta from two inspector screenshots.

Puppet write test (`local/puppetdrive.c`): external 100 Hz writer pushing
player+offset into the companion's 24 slot copies. Result: **no movement** -
the copies are read-only views; the engine recomputes kinematics every frame.
Second test (`local/puppetdrive2.c`) rigid-drives all 29 slots including the
likely skeleton bones (0x2633D930, 0x26345B30, 0x3CADCC30, 0x403465E0,
0x4343E928 - each offset 0.2..0.9 m from the feet position). Result: pending.

Fallback plan if bones also fail: catch the writer of a cache slot with a
hardware watchpoint (external mini-debugger: DebugActiveProcess + DR0 write
breakpoint) and walk the code path back to the authoritative source.

**Crash/hang lesson (2026-10-21):** the plugin's `apply_puppet` wrote 16 bytes
(float4 row ending in `1.0`) into slot 0x43438958 and **hung the game**. The
dword after a position triple is NOT padding - it holds a pointer/flag. All
position writes must be 12 bytes. External 12-byte writes (puppetdrive) were
safe. The plugin was rebuilt: `apply_puppet` now writes 12 bytes to a list of
targets from repeated `target <hex> [yoff]` lines in `coop_puppet.cfg`
(config is re-read every second, so the puppet can be reconfigured without a
game restart). The in-process write happens in the camera hook
(`FUN_006d24d0`), i.e. after the engine's per-frame update - the timing the
external driver could not achieve.

### What the position slots really are (2026-10-21, later)

- **The 0x0019xxxx band is THREAD STACK, not .data.** It lies below the exe
  base (0x00400000). The "player position copies" found there earlier were
  stack locals of the engine's own position code. Writing them corrupts the
  stack - that is what hung the game both times.
- Hardware watchpoint on 0x0019F500 (watchwrite.exe) caught the writers: the
  Apt UI framework (`c:\packages001_pc\apt\0.19.optimized\source\apt\_AptValue.h`)
  pushing locals, plus vector math at 0x0043A2EF/0x0043A47C/0x0043A653. All
  scratch, not the transform source.
- Real globals confirmed in the exe image: player matrix 0x0112E210 and camera
  look-at 0x0112A840. The matrix global is field +0x198 of a .data object
  (base 0x0112E078, vtable 0x00E32A84, instance name "WhiteboxCar"); a heap
  twin of every such object exists (0x0434E078 for the player). 17 pairs found.
- The companion's position is NOT in any vtable-prefixed object's first
  0x2000 bytes (findobj wide scan: 0 hits), and not stored as an AABB min/max
  pair (findaabb: 0 hits). It IS stored as ~26-54 float triples followed by
  1.0 (a matrix-row look), including a .data/heap pair (0x01163640 /
  0x04213640 in one session) - but the 4th float is not reliably w=1 (one slot
  read back 51.37), so these are 4-field structs, not clean matrices.
- Writing the companion's matrix rows in-process at CAMERA time is overwritten
  by the engine later in the frame (observed: the value snapped back).
  Moving the write to the D3D9 PRESENT hook (end of frame) was implemented;
  writing 26 such rows **crashed the game** - one of those fields is load
  bearing.

### Open end (handoff)

The authoritative transform write primitive is still open. Ranked next steps:

1. Hardware watchpoint (watchwrite.exe works and is read-only) on one of the
   companion's matrix rows while the companion WALKS: catch the writer code,
   then read that function in `godfather2_10789_se.c` to find the source it
   copies from. This is the decisive experiment and it needs no game writes.
2. Plugin hook of the position getter `FUN_0054dbf0` (prologue is exactly
   6 bytes: 55 8B EC 83 E4 F0) logging (node, position) - MUST record into a
   plain array with no sprintf/file I/O in the hook: the first attempt did
   5000 VEH-swallowed faults (in-plugin) and crashed the game.
3. Drive the engine's own behaviour layer instead of raw memory: the script
   VM registers `SetPositionValues` / `GetPlayerPosition` / `moveToPos`
   (`FUN_00468cb0`), and the SimNPC handle (inspector) is the entity to call
   them on. This keeps the engine's kinematics consistent and is the right
   long-term primitive for co-op.

Tooling inventory in `local/`: findtrip (position search, loose y, optional
w=1 filter), findval (32-bit value search), findobj (objects with a vtable
holding a position), findaabb (min/max pairs), watchwrite (hardware watchpoint
mini-debugger with register dump), deltahunt (whole-heap delta search),
nearwatch (watch triples near a point), puppetdrive/2 (external 100 Hz rigid
write), coopscan (dump/list/chars/snap/diff).

### Render pipeline vs game state (2026-10-21, later still)

Hardware-watchpoint run on 0x01163640 (the .data address that repeatedly holds
the companion's position) caught **one instruction, 0x004175BA**, inside
`FUN_00417560` - a 4x4 matrix multiply (`out = a * b`, written with `movaps
[edx], xmm5` in a 4-iteration loop). Register/return-address capture identified
three call sites: `FUN_00419b80 + 0x106` (0x00419C86) and `FUN_0041b9d0`
(0x0041BA8C). Their decompile shows the source is a per-thread array reached
through `fs:0x2C` (TLS), indexed by `*(entity+0x18)`, with rotation/scale at
`+0x6C/+0x70/+0x74`. So 0x01163640/0x01163610/0x011629d0 are **render
matrices**, and the .data/heap twins (0x01163640 <-> 0x04303640) are render
scratch, not game state. Writing them cannot move a character - consistent with
every failed puppet write so far.

Whole-heap delta search (deltahunt with the companion's own delta from two
inspector screenshots, -14.29/+0.04/-41.52 over a 44 m walk): **only two slots
in the entire process moved by that delta**, and one of them is the render
scratch above. Conclusion: **the companion's position is not stored as a float
triple anywhere** - it is a simulation entity whose position is integrated and
kept in a different representation (physics body, handle, or derived on demand).

Next step that follows from this: don't chase float triples. Catch the *read*
path instead - watch the SE NPC Inspector's own display buffer while the
inspector refreshes (the SE computes the value it shows, so its code reveals
the authoritative source), or hook the engine's script setter
(`SetPositionValues`) from the plugin and let the engine move the character.
### The SE inspector read path, statically decoded (2026-10-21)

Static analysis of `GF2ASI.asi` (a PE32 DLL, image base 0x10000000, no packing:
the inspector strings are plain ASCII at file offsets 0x0F0C3C-0x0F1434):

- The "Position:" label is at VA `0x100F21BC`, referenced from exactly one place,
  `0x10091802` (`push 0x100F21BC`). The surrounding code is the inspector's
  General tab: `lea eax,[ebp-0x48]; push eax; call 0x10074730` fills a 12-byte
  buffer, then three `cvtps2pd`/`movsd` pairs and a printf push it as three
  doubles.
- `0x10074730`: `call 0x10074680` with ECX = the inspected object, `add eax,0x30`,
  then `call 0x10073180` (coordinate conversion into the output). So the position
  is `object + 0x100`.
- `0x10074680` is the decisive one: it copies four `movups` from
  `ecx+0xD0`, `ecx+0xE0`, `ecx+0xF0`, `ecx+0x100` into a local 16-float matrix
  and converts each row. **A character's world transform is a 4x4 matrix at
  `object + 0xD0`; its 4th row is the position.** This matches the engine's own
  composer (`FUN_0088d6e0` branch A reads a position at `ctx+0x100`).

`findmat.exe` (new, local) scans for exactly that structure: a 16-float matrix
with orthonormal rows 0..2, `w = 1`, and the 4th row equal to a given position.
On the companion it finds 30 objects: three at feet height with the inspector's
exact value (`0x04396720`, `0x043968E0`, `0x229A9E30` in that run) and a series
of 27 at centre height (`0x22B0xxxx`-`0x22B8xxxx`) whose positions vary smoothly
inside a few decimetres - a movement history/trail, not a single transform.

Writing the player's position into the 4th row of the three feet-height objects
in-process at PRESENT (12-byte writes, `w` preserved) left the model standing on
its marker - these objects are not what the skeleton render reads. The next
cheap step: put the hardware watchpoint on one of the trail objects
(`0x22B0xxxx`) while the companion walks; its writer is the movement/AI code and
its source is the simulation transform that a puppet must write.
### Watchpoint on a "found" matrix: it is a transient buffer (2026-10-21)

Hardware watchpoint on the translation row of one of the 21 findmat matches
(`0x430B4B30`) fired 12 times at a single DLL address, `0x6EF6CC8E`, with
`rep movsb` in the bytes before EIP and a byte-at-a-time source cursor
(`ESI 0x015477BD..C0`), i.e. a **generic byte copy from a loading/streaming
buffer** - not a character update. So the "orthonormal 4x4 with the right
translation" matches in that heap region are transient buffer contents that
happen to satisfy the test; value-based matrix hunting is exhausted.

The solid result stands: the SE inspector proves the *layout*
(`object+0xD0` = world matrix, 4th row = position at `object+0x100`). What is
still missing is the *object identity*: which heap object is the companion's.

Recommended next step (well-scoped, no more fishing): the SE keeps its
inspector target in its own `.data` (image base 0x10000000, section .data at
VA 0x10179000). Find the static pointer the inspector code reads
(`movl (%edi), %ecx` at 0x100917CB) by tracing back from 0x10091802's function
prologue and from `0x10179400`-style statics, then read the target object and
its +0xD0 matrix directly - that is the object the SE itself trusts, and the
same object a puppet should write. Alternative with higher long-term value:
hook the engine's `SetPositionValues` script setter and let the engine move the
character.
### Inspector chain traced; why Present-time writes cannot work (2026-10-21)

- The inspector entry function is `0x10091740` (`movl %ecx,%edi` at +0x2B, so
  `this` -> EDI). It has exactly one caller, `0x1008F891`, which passes
  `lea 0x10(%edi), %ecx` - i.e. the inspector context is `inspector_object+0x10`,
  and `*(context)` is the inspected target object (the one whose `+0xD0` matrix
  and `+0x100` position the SE displays).
- The inspected object is NOT the SimNPC: `SimNPC+0xD0` holds handle/registry
  data (`3, 0, ptr, 0, ...`), not a matrix. The target is a different entity.
- The strings `SetPositionValues` / `moveToPos` / `GetPlayerPosition` do not
  exist in GF2ASI.asi: the SE calls engine functions directly, not through the
  script names. "Teleport" in the ASI is only a menu button label (referenced
  from 0x1008D6F0 and 0x1008F793, both UI-registration calls).

**Why every direct transform write failed:** the renderer poses the skeleton
earlier in the frame than our PRESENT hook. Writing the world matrix at PRESENT
is therefore one frame too late for the skeleton, and writing earlier is
overwritten by the engine's own kinematics. The character can only be moved
through the engine's own update path (its setter / script behaviour), not by
poking a matrix. This is the conclusion of the whole transform hunt and it
re-prioritises the work: the next task is to locate the engine's setter
(the function the game itself calls when an NPC is told to move to a position),
e.g. by watching the write to `entity+0xD0` earlier in the frame - set the
watchpoint on a *known* entity's matrix and let the *engine* move that entity
(an NPC walking on its own, or the player teleporting), then read the call stack
of the writer - rather than by writing and observing.
### The SE has no direct "set position" call (2026-10-21)

Scanning GF2ASI.asi .text for `E8` calls whose target lands in the game's
address range (0x00400000-0x01230000) yields exactly six sites:
FUN_005d9ab0+0xAA, FUN_005d93e0 (a LinkedObject-style constructor),
FUN_004b6060 (a small object ctor), FUN_0094bf90 (a UI/param ctor),
FUN_0059fd60+0x2DF and FUN_005167d0+0x8EB. None is a position setter: the SE
reaches game functionality through the UI/script layer, not through a direct
engine call. So there is no shortcut to the setter inside the SE.

Consequence for the next session: implement the call into the game's own script
VM from our plugin. Relevant engine pieces (all in the decompiled corpus):
`FUN_00468cb0` registers the script names, `FUN_00468ea0` shows the call
pattern (`iVar1 = *(int *)(DAT_012233f4 + 0x18)`, `FUN_00625ca0` push name,
`FUN_00633cb0` lookup, `FUN_00636570` invoke). Decoding those three helpers and
calling `SetPositionValues` / a `moveToPos` behaviour on the companion's SimNPC
handle is the concrete, bounded next task - and it is also the right primitive
for networked co-op later.
### Script VM: object located, calling conventions disassembled (2026-10-21)

Live addresses in the running game: `DAT_012233F4` holds `0x04750C4C`; the VM is
`*(0x04750C4C + 0x18)` = `0x1ABC0108`. VM layout: `+0x08` = stack pointer,
`+0x10` = stack-limits object (`+0x20`/`+0x24`), `+0x40` = the globals table
(first dword 5 = table tag, `+0x44` = table object).

Exact calling conventions, read from the real exe with llvm-objdump:

- `FUN_00625ca0` = **cdecl(vm, const char* name)**. Prologue
  `push ebp; mov ebp,[esp+0xC]` -> arg2 = name; `mov eax,[esp+8]` -> arg1 = vm.
  Pushes a value on the VM stack: writes type 4 and a string id, advances
  `vm+0x08` by 8. A NULL name pushes a zero value.
- `FUN_00636570` = the invoke. **cdecl with 3 stack args** (a continuation code
  pointer, a pointer to the prepared frame, a byte length) **plus the VM in
  ESI**, i.e. a custom register convention. The only call site in the game
  (`0x00468F2B`) pushes `0x6261E0`, `lea eax,[esp+0x10]`, and
  `stack_top - args_start`, then `add esp,0x10`.
- `FUN_00633cb0` = cdecl(vm, table_value, key_value, depth) lookup, returns a
  pointer to the resolved value.

Because the invoke takes the VM in ESI, a plugin call needs a small assembly
stub (set ESI = VM, push the 3 args, call 0x00636570) - not a plain C call.
Plan for the implementation session: mirror the exact sequence of
`FUN_00468ea0` (push name -> lookup in globals -> push args -> invoke with the
`0x6261E0` continuation) for a harmless getter first (`GetPlayerPosition`),
log the result, and only then call `SetPositionValues` on the companion. Do it
inside the plugin's own thread with the VEH active.
### Script VM: lookup works, invoke from a render hook faults (2026-10-21)

Implemented in the plugin and tested live:

- `DAT_012233F4` -> `0x04750C4C` -> `+0x18` gave the VM (`0x1AB90108` in that
  run); `FUN_00625ca0(vm, "GetPlayerPosition")` pushed the name and
  `FUN_00633cb0(vm, vm+0x40, stack_top-8, 0)` resolved it in the globals.
  **The lookup succeeded: type 6 (a function), value 0x1A2E4650.** So the
  script API is reachable from the plugin and `GetPlayerPosition` really exists
  in the VM's global table.
- The invoke is the problem. First attempt used an inline-asm stub; second used
  a hand-assembled byte stub (`push esi; mov esi,[esp+8]; ...; call; add esp,12;
  pop esi; ret`) with the 0x6261E0 continuation. Both times the game died at the
  invoke: the VEH logged ~5000 swallowed faults at a fixed plugin address and
  the process ended. So `FUN_00636570` cannot be called from the camera hook -
  the VM is almost certainly only valid while the game's own script update is
  running, and calling it from a render hook corrupts its state.

Next step for the VM path: hook the game's script/update function and issue the
call from inside it (same thread, VM active), or better, find where the game's
own code pushes and invokes a script function by name and reuse that wrapper.
The lookup result (type 6, value 0x1A2E4650) is a good anchor for that work: it
identifies the `GetPlayerPosition` function object in the VM.
### Script VM: the safe helpers found (2026-10-21)

Scanning the corpus for the VM global (`DAT_012233f4`) yields six functions:

- `FUN_0043e140` - VM owner constructor (stores `param_1` in `DAT_012233f4`);
  `FUN_0043e250` - its destructor.
- **`FUN_0043e480`** - the safe "resolve a script name" helper: pushes the name,
  looks it up in the globals, copies the resolved value over the stack top,
  checks `type == 6`, then calls **`FUN_006255e0`** (the stack-pop helper) and
  returns whether the name is a function. This is a safe, complete example of
  the push/lookup/pop sequence with stack cleanup - use it as the reference for
  any name lookup.
- `FUN_00468ea0` / `FUN_00468f40` - the "call a script function" wrappers:
  EAX = the name, EDI = the argument count, `[ebp+8]`/`[ebp+0xC]` describe the
  arguments; `FUN_004c8460` pushes them; `FUN_00636570` invokes with the
  `0x6261E0` continuation. `FUN_00468f40` is the same for `CallLuaFunction`.

So the VM has a proper internal API (`FUN_0043e480` for lookups,
`FUN_00468ea0`/`FUN_00468f40` for calls) that keeps the stack balanced. The next
attempt at moving a character should call `FUN_00468ea0` (or mirror it exactly,
including `FUN_006255e0` for every push) instead of the raw `FUN_00636570`, and
should run while the game's script update is active.

### Script VM: the wrapper fails too - the VM needs the script update context (2026-10-21)

Second live attempt used the game's own call wrapper `FUN_00468ea0` through a
hand-assembled byte stub (name in EAX, argument count 0 in EDI, two zero stack
dwords cleaned by the wrapper's `ret 8`), called once from the camera hook.
The log recorded `vm: calling GetPlayerPosition via FUN_00468ea0` and the
process died before `vm: call returned`. Together with the earlier raw-invoke
fault this is conclusive: **the script VM cannot be driven from the camera or
present hooks** - it is only valid while the game's own script update is
executing. Calling it from any render/update hook corrupts its state and takes
the process down.

The VM test is disabled in the plugin again; the game is stable.

Next step for this thread is therefore narrower and concrete: locate the game's
script update (the function that pumps the VM each frame - the VM owner is
`DAT_012233f4`, its interpreter entry `FUN_00635ab0` reached from
`FUN_00636570`), hook that function, and issue the `FUN_00468ea0` call from
inside it while the VM is live. Only then is `SetPositionValues` reachable.
### Script VM driver chain mapped (new session, 2026-10-21)

A temporary inline hook on the VM interpreter `FUN_00635ab0` (prologue is
exactly 6 bytes: `55 8B EC 83 EC 50`) logged every distinct caller while the
game booted and loaded a campaign. Three drivers exist:

- `0x006278A5` - `FUN_00627840`, the VM state constructor (init call with the
  callback `0x00627560`).
- `0x00636727` - `FUN_006366f0`, the "run a script" helper (callback
  `0x00636660`).
- `0x006365A5` - `FUN_00636570`, the invoke (continuation `0x006261E0`).

Static walk up from there:

- `FUN_006366f0` is called by `FUN_00626310` (thin wrapper) and `FUN_00629280`.
- `FUN_00629280` is called by `FUN_004cb0c0`, `FUN_0062e4c0`, itself.
- **`FUN_004cb0c0`** is the interesting one: it runs the pending script
  (`FUN_00629280`), and if that yields it invokes the continuation via
  `FUN_00636570(&LAB_006261e0, ...)` with the frame derived from
  `*(state+8) - *(state+0x1c)`, calls an optional callback, then **always calls
  `FUN_006255e0`** (the VM stack cleanup). Callers: `FUN_00480d90` (the "gGUID"
  script call), itself, and `FUN_004ffbf0`.

So the VM is pumped by `FUN_004cb0c0`; after its `FUN_006255e0` the stack is
clean. The next experiment is to hook `FUN_004cb0c0` (or its callers) and issue
`FUN_00468ea0("SetPositionValues", ...)` right after the cleanup - the first
attempt from a context that is by construction part of the script pump.

The interpreter hook is removed again from the shipped plugin (it was a
diagnostic); the plugin is back to the stable hook set.
### Script VM is closed to external calls (2026-10-21, final)

Third context tried: a hook on the script pump `FUN_004cb0c0` (prologue
`55 8B EC 83 E4 F8`, state in EAX, context in ECX, one stack dword). A byte stub
re-pushes the stack arg, calls the trampoline, then calls `pump_after()`, which
issues `FUN_00468ea0("GetPlayerPosition")` right after the pump's own
`FUN_006255e0` stack cleanup, and finally `ret 4` like the original. The log
recorded `pump: after cleanup, calling VM` and the process died before the call
returned.

Result across three contexts (camera hook, raw invoke with the continuation,
and inside the script pump after its cleanup): **the VM cannot be called from
plugin code at all**. Its global state is only valid while the game itself is
executing a script instruction; any external call corrupts it.

Consequence: the engine's script API is not a usable primitive for a co-op mod
from an ASI plugin. The remaining honest options are

1. make the game's own script system run our code (loose-file loading - the SE
   advertises loose-file loading, so the engine evidently accepts script
   payloads from disk; the format must be determined), or
2. drive the game's own AI instead of the transform: the crew-follow behaviour
   already makes an NPC walk after the player with full animation and
   pathfinding, so redirecting its follow target is the smallest real co-op
   primitive - it needs the follow order's target field, not the transform.
### The SE engine-API map (new session, 2026-10-21)

The SE calls game code indirectly: `mov eax, <game address>; ...; call eax`.
Scanning GF2ASI.asi .text for `B8 <imm32>` with `imm` inside the game's image
(0x00401000-0x00DEE000) yields **108 distinct game function addresses**, plus 10
game data addresses. This is the working mod's whole engine API surface - the
shortlist of functions that a real mod in this game actually uses.

Top targets (call count, containing corpus function):

| address | uses | function |
|---|---|---|
| 0x0044B82F | 10 | FUN_0044b820 - copies a 4x4 transform from `*(this+4)` into `this+0x50` via FUN_0044b4e0 |
| 0x00408A00 | 5 | FUN_00408a00 - handle-hash lookup in `DAT_01162380` (world object registry) |
| 0x00621530 | 2 | FUN_00621530 |
| 0x004083D0 | 2 | FUN_004083d0 |
| 0x00465E30 | 2 | FUN_00465e30 |
| 0x006A100E | 2 | FUN_006a0840 |
| 0x00483410 | 2 | FUN_00483410 |
| 0x008C8A20 | 2 | FUN_008c8a20 |
| 0x0043B870 | 2 | FUN_0043b870 |
| 0x008C80D0 | 2 | FUN_008c80d0 |

The full table (address, call count, containing function) is kept in
`local/se-engine-api.txt` (private, gitignored). Reproduce with a byte scan of
`GF2ASI.asi` .text for `B8` followed by a dword in the game's image range.

Next session should read FUN_0044b820 and FUN_00408a00 first: the SE uses them
for every entity it touches, and FUN_0044b820 looks like the transform refresh
that our own writes failed to trigger.
### Correction: the SE engine-API scan needs the `call eax` check (2026-10-21)

The first scan of GF2ASI.asi for `mov eax, <game address>` produced 108 hits, but
many were **division magic constants**, not function addresses (for example
0x0044B82F is the magic number the compiler uses for an unsigned 64-bit divide;
0x3B9ACA00 nearby in the same sequence confirms it). The map must only keep
entries where `mov eax, <addr>` is followed by `call eax` (`FF /2` with a
register operand) within the next 16 bytes.

With that check the real surface is **26 engine functions**:

| address | uses |
|---|---|
| 0x00408A00 | 5 (handle registry notify: walks subscribers in `DAT_01162380`) |
| 0x008C8A20 | 2 |
| 0x004083D0 | 2 (subscriber/message helper, calls 0x00408A00) |
| 0x00465E30 | 2 (type dispatcher on `param_2` 0x11/0x12) |
| 0x00483410 | 2 |
| 0x0043B870 | 2 (array-of-pointers index via `this+0x34/0x38`) |
| ... plus 20 more with one call site each |

`local/se-engine-api.txt` now holds this corrected table with call sites.
Lesson for the write-up: indirect-call maps must validate the `call`, never
trust an immediate that merely falls inside the image range.
### SE object hunt: negative result (2026-10-21)

A 32-bit module lister (`local/modlist.c` -> `modlist.exe`) was needed because
64-bit PowerShell only sees the WOW64 modules of the 32-bit game. With it:
**GF2ASI.asi base = 0x6D780000**, coopcore.asi = 0x70C40000, cooptest.asi =
0x734B0000.

The plan was to find the NPC Inspector's own object in memory and read its
`+0x10` context, then `*(context)` (the inspected entity) and its `+0xD0`
matrix. It failed: searching RW memory for the class vtable
(0x100EBC8C relocated -> 0x6D86BC8C) returned 0 hits, and so did searches for
the inspector entry (0x6D811740) and the position reader (0x6D814730). Those
function addresses only ever appear as direct call operands inside RX code,
which the value scanner does not read, and the inspector's class vtable is not
the one used by the constructor at 0x10074760.

So the "read the object the SE trusts" shortcut is closed too. `modlist.exe`
is kept: it is the only way to get 32-bit module bases here.
### How the SE really drives the game: events, not direct calls (2026-10-21)

Restricting the engine-call scan to the crew-menu code region
(0x1008C000-0x10090000) leaves exactly one game call: site 0x1008F377 ->
**0x00408A00**, the handle-subscriber registry. So the SE does not call "spawn"
or "set position" engine functions for crew operations; it dispatches an event
against an entity handle and lets the game's own subscribers react
(`FUN_00408a00(handle_struct, flag)` walks the subscriber list stored in the
hash table `DAT_01162380` and calls each subscriber's vtable slot +4).

That reframes the whole problem: the game's character behaviour is event-driven
and the right primitive for co-op is an **event/message** carrying a handle (and
the game object that receives it), not a function call and not a memory write.
The two viable next directions, unchanged in priority:

1. **AI order / follow target**: find the crew-follow order's target field so a
   second client can steer the companion by orders (the engine keeps animation
   and pathfinding). Smallest real co-op primitive.
2. **Loose-file scripts**: make the engine load and run script payloads from
   disk (the SE advertises loose-file loading), then the full script API is
   available in the proper context.

The engine-API scan method and its trap are recorded above; the 26-function
table is in `local/se-engine-api.txt`.
### Scripts are Lua (from VisceralToolkit v1.4, read-only analysis) 2026-10-21

The owner supplied `VisceralToolkit_v1.4.7z` (SHA-256
2D9AC0607BC50B3DEC3F3D4F85434A13F841255BE9DB043B763EBCB92E115D6E). It is a
binary application (VisceralApp.exe + Cauldron.dll + PhysX), so it was **not
run**; only its strings were read. The plugin DLL contains the engine's
resource-type table:

    BricknMortarRes COVERNODETABLE GRAPH PartsDB MobFace TreeModel TOB
    TRINITY_SEQ_MASTER **LUA LUAC** Mesh MeshVolatile Material Score
    **SimGroup VariableDictionary** VFX UIX UIE SHK PFR MIX FFN HKX BNK APX WGI

**The game's scripts are Lua / Luac (compiled Lua bytecode).** That settles the
open question behind the "loose-file script" direction: the engine executes
Lua, so a mod can in principle ship script payloads instead of fighting the VM
from outside. The plugin also mentions a Resource Manager and StreamTOC, i.e.
the `.str` streams carry these resources, and SimGroups/VariableDictionaries are
first-class resource types.

Saved list: `local/visceral-resource-types.txt`.

Next: determine how the engine resolves Lua resources (inside the `GODFATH*.viv`
/ `.str` streams) and whether the SE's loose-file loading can override a script
path. If it can, the whole co-op primitive becomes a Lua script calling
`SetPositionValues` in the engine's own context - the path we could not reach
from C.
### SimGroup class model from VisceralToolkit v1.4.0.5 (2026-10-21)

Second archive: `VisceralToolkit_v1.4.0.5.7z`, SHA-256
031103C8E9075660A4C857795A2802FD60D648977F4D861B4289BA45F550D7BA, 17.5 MB.
It ships `Content/databases/gf2_simgroup.db`, a JSON description of the game's
script object model (22 classes, attributes with types, 18 enums). Read only, the
application was not run.

Relevant facts:

- **`CSystemCommands` has `m_Matrix : matrix`** - script objects can carry a
  transform, which is how the engine's own teleport/spawn paths work.
- `SimNPC` attributes are only identity/social data (`m_NPCGuid`,
  `m_SocialClass`, `m_ClothingFormality`, `m_Climate`, `m_Palette`,
  `m_RoleNameHashID`, `m_CityNameHashID`, `m_Flags`) - **no position and no
  follow order**, so movement lives on the entity/physics side, not on the
  SimNPC record.
- `SimNPCFlags` enum: `KEEP_OUT_OF_SPAWN_POOL`, `DONT_GIVE_DYNAMIC_JOBS`,
  `OUT_OF_STRATEGY_GAME`, weapon licenses, `CANNOT_BE_RESPONDER`,
  `NOT_MADEMAN_UNTIL_RECRUITED`, `CANNOT_BE_UPGRADED` - none is a crew/follow
  flag.
- `Entity`: `m_IGCOptions`, `m_Model` (guid128), `m_Group`, lighting flags.
- `StreamScripter`: eight `m_TargetN` guid128 slots plus stream visibility
  events - a ready-made "point at entities" mechanism scripts use.

Full model saved to `local/gf2-simgroup-model.txt` (private reference).
Next: a script payload (Lua/Luac) is the only place where `SetPositionValues`
and the behaviour commands are callable; the class model above is the vocabulary
that script must use.
### THE DOOR: SimGroup Override (.sgp) injection (2026-10-21)

The game folder (as staged by the SE bundle) contains a `simgroup_mods` folder
whose README states the mechanism verbatim:

> GF2ASI supports "SimGroup Override" files authored by Visceral Toolkit. There
> is no limit to the number of files GF2ASI will load, nor is a limit to number
> of behaviours to inject. Any "SimGroup Override" files must have an extension
> of "sgp" in order to be read. When the game loads a Stream file (*.str) which
> consists of a SimGroup, the mod will inject any detected modded behaviours.

This is the first real door into the engine's own script layer:

- a `.sgp` file placed in `<game dir>\simgroup_mods\` is read by GF2ASI;
- when the game loads a SimGroup stream, the mod **merges the modded behaviours**
  into it;
- SimGroup behaviours are the script objects whose class model we now have
  (`local/gf2-simgroup-model.txt`), including `CSystemCommands` with
  `m_Matrix : matrix`.

So a co-op mod can ship `.sgp` payloads that add/replace behaviours on game
SimGroups without touching the exe, the VM from C, or memory.

Format work needed: the `.sgp` container is authored by Visceral Toolkit; it is
almost certainly the same SimGroup stream layout the game already loads from
loose `.str` files (about 2300 of them ship with the game). Two ways forward:

1. Author a minimal `.sgp` with Visceral Toolkit on the owner's machine (the
   toolkit is a user-supplied 64-bit GUI; it was not run so far) and inspect the
   bytes;
2. Reverse the SimGroup stream layout from the game's own loose `.str` files and
   the toolkit plugin's serializer, then generate `.sgp` ourselves.

Option 1 is a few clicks for the owner and immediately gives a ground-truth
sample; option 2 keeps everything in-house. Both end at the same place: our
behaviour inside a SimGroup, where the engine's own script API is callable.
### VisceralToolkit live session: what works, what does not (2026-10-21)

Ran `CauldronApp.exe` from `VisceralToolkit_v1.4.0.5` (owner approval) against the
test copy's `gf2` folder:

- Created a game instance `gf2test` pointing at
  `<copy>\gf2`. Instance metadata lands in `<copy>\gf2\_toolkit\game_instance.xml`.
- Content Browser lists the streams; opening `cuba.str` (128 KB) shows its
  resources: four `WGI`, `BricknMortarRes`, `VariableDictionary` and a
  **`SimGroup` (26.88 KB)**.
- Selecting the SimGroup opens a hex view with a resource header
  (`IsModified: No`, a placeholder GUID) and readable SimObject names inside
  the payload: `FOWLER20`, `FOWLER10`, `REI SDORF`, `WOLSKY4`, `BILLIEYB`,
  `AGAY2026`, `CALHOUN1`. Names are stored as plain ASCII even though class and
  attribute names are SDBM hashes.
- Patches: `Create Patch` produced
  `<copy>\gf2\_toolkit\wip_patches\coop_test.vpat` (magic `VPAT`, name, author,
  then one entry per resource: `cuba.str`). Saving rewrote `gf2\cuba.str` and
  created `gf2\_toolkit\backups\cuba.str`, but **the file was byte-identical** to
  the backup - the hex edit never registered, so the patch stayed metadata-only.
- `simgroup_mods` (where GF2ASI expects `.sgp` overrides) stayed untouched.

So the toolkit already does the two things we wanted: it reads the SimGroup
container and it writes patches with automatic backups. The missing piece is
the editing gesture inside the SimGroup view (the hex view appears to be
read-only, and no structural attribute editor was found in the menus we tried).
Next session: either find the toolkit's edit/apply control (its plugin DLL
contains the UI labels - grep it for `Behaviour`/`Apply`/`Save`), or take the
`VPAT`/`.sgp` container apart from `coop_test.vpat` (66 bytes) plus a modified
`cuba.str`, and write the container ourselves.
### .str container and SimGroup name table decoded by hand (2026-10-21)

No GUI needed after all. `cuba.str` (131072 bytes) starts:

    00000: "3slo"  0C 00 00 00  02 00 03 00      magic, version 12, flags
    0000C: "COHS" (SHOC reversed)  2C 00 00 00     chunk header
    00010: "RDHS" (SHDR reversed) ...
    00040: "TADS" (SDAT reversed) ...
    00068: table of 24-byte records

Each record is **name[16] + value[8]**: a 16-byte ASCII name, NUL padded,
followed by an 8-byte value (GUID/hash fragment). Examples straight from the
file:

    "BRINKER2" + DB C1 C8 35 B0 1F 8C FE
    "BOYD6799" + 64 2D C9 58 6B CE AE 96
    "FOWLER20" + 27 FF C8 33 D2 09 32 AF
    "AGAY2028" + 96 EB C8 33 D2 27 79 F2
    "CALHOUN1" ...

The repeated `C8 33` / `C8 35` bytes inside the values suggest a timestamp or
type tag embedded in the id, and names are plain ASCII even though class and
attribute names are SDBM hashes (the toolkit says so explicitly).

So a co-op mod can edit game SimGroups **file-side**: parse the 3slo container,
locate the SimGroup chunk, change object records, write the `.str` back. No
toolkit, no GUI, no synthetic input. The remaining work is mapping the object
records that follow the name table (class hash + attribute block) so a position
or a behaviour can be changed.

All of the above is reproducible with plain byte reads; the toolkit only served
as an oracle that the game really loads these files.
### Hash variant check and where the file work stands (2026-10-21)

`local/sdbm.c` -> `sdbm.exe` computes the SDBM hash (the algorithm the toolkit
documents). Results:

- `sdbm("simgroup")` = **0xCCB51828**
- the toolkit showed the SimGroup type hash as **0xCC5B1828**

They are close but not equal, so the engine's variant differs in seed or in a
final step, and the toolkit's "Type Name" value is not simply the lowercase
SDBM of the class name. Treat the exact hash function as **open** - compute it
from a confirmed pair before relying on hash matching to find records.

What is solid from this session:

1. `.str` is a chunk container: `3slo`, version 12, then chunks whose magics are
   stored reversed (`COHS`=SHOC, `RDHS`=SHDR, `TADS`=SDAT).
2. After the chunk headers a table of **24-byte records** starts: a 16-byte
   NUL-padded ASCII name plus an 8-byte value. Object names are plain text
   (`BRINKER2`, `BOYD6799`, `FOWLER20`, `AGAY2028`, `CALHOUN1`, ...).
3. The toolkit can read and rewrite these files (it made a backup and updated
   `cuba.str` + `coop_test.vpat`), but its hex view did not register edits, so
   the actual write path still has to be done by us.

Next: parse the SimGroup chunk record-by-record (name table first, then the
object blocks), identify which 8-byte values are GUIDs vs hashes, and locate the
attribute block that holds a transform. Everything is plain byte work on the
test copy - no toolkit, no GUI, no synthetic input.
### Record table mapped across three streams (2026-10-21/22)

New tool `local/strnames.c` -> `strnames.exe` scans a `.str` for the 24-byte
record pattern (8-byte id + 16-byte NUL-padded ASCII name) and prints them.

Results on the three mission streams:

| file | table starts | records found |
|---|---|---|
| cuba.str | 0x64 | **754** |
| queens.str | 0x64 | (same layout, names `REISDORF`, `BRINKER2`, `CALHOUN1`) |
| miami.str | 0x64 | (names `AGAY2028`, `CALHOUN1`, `ILIEV184`) |

Facts that now hold:

- The container header is a fixed size: the SDAT chunk starts at 0x3C, its
  fields begin at 0x44 with `07 00 00 00` (7 resources - exactly the count the
  toolkit listed) and `20 00 00 00` (header size), and the record table begins
  at **0x64 in all three files**.
- Records are `[8-byte id][16-byte name]`, stride 24 bytes, and the id's first
  dword shares a high byte pattern (`...C832`-`...C939`) while the second dword
  varies freely - so the id is likely a GUID fragment, not a hash of the name.
- cuba.str's table holds 754 entries, including script-ish names
  (`jt_kt_accepted_c`, `dj_kt_complete_c`), i.e. the table is the stream's whole
  name registry, not only the SimGroup's objects.
- Names are plain ASCII; only class/attribute names are hashed.

Next: walk past the name registry to the object blocks (class id + attribute
values) - that is where a transform lives - and confirm the block layout on two
files at once so a wrong guess is visible immediately.
### BREAKTHROUGH: object blocks with transforms found in the stream (2026-10-22)

Past the name registry (ends at 0x4714 in cuba.str) the stream holds object
blocks. First two, verbatim:

    004714: 2F 70 16 FE 70 A0 00 00 | E8 46 00 00 00 00 00 00 | 00*8
    00472C: 09 91 C8 37 62 CC B0 06                       <- object id (8 bytes)
    004734: "CHAVEZ193"
    00473D: -373.43  12.88  60.86  63.76                  <- floats
    00475C: -411.12   3.07  10.39   1.00                  <- row with w = 1.0
    00476C: -335.73  22.69 111.34   1.00                  <- second row with w = 1.0
    00477C: 00 00 01 00 01 00 00 00                        <- small ints
    004794: next block, same 8-byte class id, id 09 91 C8 37 06 21 23 0E,
            name "CHAVEZ19", floats -311.23 11.83 -13.66 61.62 ...

So each block is `[class id][size][object id][name][float payload]`, and the
payload contains **matrix rows ending in 1.0** - exactly the transform layout we
were hunting for in memory, now in a plain file that the engine loads.

This is the first primitive that is both authoritative and writable without
touching a running process: edit the float payload of an object block, write the
`.str` back, and the engine will load the moved object.

Next steps:
1. write a block walker (offset, class id, object id, name, payload length) and
   confirm the layout on cuba.str + queens.str + miami.str at once;
2. identify which float group is the live position (compare two blocks of the
   same class - e.g. several CALHOUN1 entries in queens.str - and see which
   group differs per instance while the rest is constant);
3. change one value, write the file, boot the game and look at the result.
### RESULT: object blocks in the stream are editable and the game loads them (2026-10-22)

First working write primitive of the whole project, and it needs no running
process at all:

1. `local/strfindblocks.exe` finds object blocks in any `3slo` stream by their
   self-referencing header dword (at block+8 the value equals block-0x2C). It
   works on cuba.str, queens.str and miami.str and prints class id, object id,
   name and the centre/min/max floats.
2. `local/strnear.exe` walks all 2304 streams and lists blocks within a radius of
   a world position, so the object under the player can be found by coordinates.
3. Block layout (confirmed on three files):

       +0x00 class id (4) + a size/id (4)     +0x08 self-reference (block-0x2C)
       +0x18 object id (8)                    +0x20 name (NUL-padded ASCII)
       +0x2C centre (x, y, z, radius)         <- the position
       +0x48 min (x, y, z, 1.0)               +0x58 max (x, y, z, 1.0)
       +0x68 flags

   The centre always equals (min+max)/2 - the same AABB-centre rule the engine
   uses for characters in memory.

4. Editing a float in the file and booting the game works: a +0.01 m change to
   the centre of the "REISDORF" block at 0x8794 in cuba.str (a 131x42x87 m
   complex 20 m from the player) loaded normally and the campaign ran.
5. A +50 m change to the same block **crashed the game on load** - so the stream
   is not integrity-checked (the SDAT field 0x3B004EB9 is identical in all three
   streams, i.e. a format constant), but large displacements break the spatial
   index / streaming. Small, bounded moves are safe.

What this means for co-op: the file path can place or nudge objects and NPCs at
load time (a real modding primitive), but it cannot drive a character per frame.
The runtime path remains the SE's `.sgp` "SimGroup Override" injection, and the
block layout above is exactly the vocabulary such an override would carry.

Restore point: `gf2\cuba.str.orig` (byte copy of the untouched stream).
### .sgp path: mechanism confirmed, format still unknown (2026-10-22)

Strings in GF2ASI.asi spell the mechanism out:

    simgroup_mods
    ERROR: SimGroupOverride path [%s] does not exist!
    .sgp
    Detected SimGroupOverride file [%s]
    Loaded behaviour [0x%X-0x%X-0x%X-0x%X] from ...

So a behaviour is identified by a **four-dword GUID**, the loader scans
`simgroup_mods` for `*.sgp`, and every file it accepts is logged. The `.sgp`
extension check is in code at `0x10075FBC` (the string is appended and compared).
The SE writes its own log to `scripts\GF2SE.txt` (startup lines only so far; no
SimGroupOverride lines yet).

Experiment: a copy of `cuba.str` (131072 bytes, the full 3slo stream) placed as
`simgroup_mods\test1.sgp` **crashed the game at startup** - the process never
appeared and the SE log gained nothing. Removing the file restored a clean start
(PID 22548). So the SE does read `.sgp` files, but a stream copy is not the
format it expects.

Next step for this path: read the parser around `0x10075F60-0x10076610` (the two
string references above) and recover the expected record layout - most likely a
compact list of `[GUID][behaviour payload]` entries rather than a whole stream.

Status of the two runtime routes:
- script VM from the plugin: closed (three contexts, always fatal);
- `.sgp` behaviour injection: mechanism proven, format pending;
while the **file-edit route (object blocks in .str) is a working primitive**.
### .sgp experiments: detection works, the record format needs real GUIDs (2026-10-22)

The SE's log lives at the game folder root (`<copy>\GF2SE.txt`, not under
`scripts\`). With a `.sgp` present it logs:

    Detected SimGroupOverride file [C]
    Finished SimGroupOverride file [C], with a total of 0 behaviours mounted.

That gives a clean test loop with no game UI at all: drop a file, start the game,
wait ~15 s, read the log, kill the process.

Experiments (all in `<copy>\simgroup_mods\`, one at a time):

| file | contents | result |
|---|---|---|
| test1.sgp | full copy of cuba.str (131072 B) | game crashes, no log line |
| testE.sgp | blocks-only slice (27520 B from 0x4714) | game crashes, no log line |
| testA.sgp | 16 bytes from the stream header | game alive, log: **0 behaviours** |
| testB.sgp | count(4)=1 + 16 bytes | game crashes |
| testC.sgp | 32 bytes (two 16-byte values) | game crashes |

So the loader accepts the file and counts behaviours, but the parser is strict:
the values must be **valid behaviour GUIDs** (the loader logs them as
`Loaded behaviour [0x%X-0x%X-0x%X-0x%X]`, i.e. four dwords = a 128-bit id). The
bytes I fed it came from the stream's SHDR header, which is not a behaviour GUID,
hence the crash. A real `.sgp` sample (or the exact GUIDs of live SimGroup
behaviours) is required to go further; the parser itself is C++ STL-heavy around
`0x10075F60-0x10076640` and not worth decompiling further until a real sample
exists.

Clean state restored: no `.sgp` files in `simgroup_mods`, game starts normally.
### .sgp: what is still needed, stated plainly (2026-10-22)

Answer to "what do you need from the .sgp": a **real working sample file**. With
one, the layout is a ten-minute read; without one, every guess costs a boot and
has so far produced either "0 behaviours mounted" or a crash:

- 16 bytes = [class id 8][object id 8] of a real stream block: **safe, but 0
  behaviours** (so the GUID form is plausible but the container around it is
  wrong);
- 16 bytes of header garbage: 0 behaviours;
- count+GUID (20 B): crash;
- GUID+zeros / GUID+GUID (32 B): crash;
- full stream copy (131 KB) and a blocks-only slice (27.5 KB): crash.

A sample can come from either of these, both of which need the owner:

1. the SE author / mod page (a .sgp from any published SimGroup mod), or
2. the Visceral Toolkit's own export: the Game Manager has an **Install Patch**
   button next to Create Patch; installing `coop_test` may be what writes a
   `.sgp` into `simgroup_mods` (the toolkit "authors" the format per the SE's
   README). That is two clicks in the toolkit, which synthetic input cannot
   reach.

Everything else is in place: the detection path, the behaviour counter in the
log, and a test loop that needs no game UI.
### BREAKTHROUGH: the GF2ASI source is an open, documented engine SDK (2026-10-22)

The owner supplied <https://github.com/Omerta-Labs/GF2ASI> - the SE's own source
tree (1044 files, no license published, so it is used as a **reference** for
facts and addresses only, no code copied). It answers every question this
project has been chasing:

**Where a character's transform really lives**

    class Sentient {                       // base of both Player and NPC
        ...
        EARS::Havok::CharacterProxy m_CharacterProxy;   // at 0x3C0
    };

That is the `ADD ECX,0x3C0` from the position getter we disassembled in the very
first session: the "position" is the Havok character proxy. `SimNPC` keeps a
mirror in its first member `RwV3d m_CurrentPosition`.

**The engine's own move/teleport functions**

    Player::Teleport(matrix, options, event, partitions) -> engine 0x0079E3C0
    Player::GetLocalPlayer()  -> PlayerManager::GetPlayer(0) - 0x7CC
    Player::GetUpgradeComponent() -> engine 0x043B870  (already in our SE API map)

**The .sgp format, verbatim from ModLoaderInit.cpp**

A `.sgp` is a raw `SimGroupTOC` memory image:

    struct SimGroupTOC {
        uint32_t m_MagicNumber, m_VersionOrDispatchTime;
        guid32_t m_Guid; uint32_t m_Flags;
        uint32_t m_NumEnts, m_NumEntsDispatched, m_SharedDataSize, m_DictionarySize;
        RWS::CAttributePacket** m_EntPackets;   // recovered/relocated by the loader
        uint32_t m_hStream;
    };

The loader copies the file into a buffer, `RecoverPtr`s the packet pointers
against that buffer, walks `m_NumEnts` packets, logs each as
`Loaded behaviour [0x%X-0x%X-0x%X-0x%X]` (the packet's 128-bit instance id) and
registers it in a packet map. `SimGroupOverride { guid32_t m_SimGroupGUID;
uint32_t m_OverrideFlags; }` with flags FORCE_ENABLE=1, FORCE_DISABLE=2,
IGNORE_DISPATCH_LOCK=4.

**Also documented in the SDK** (usable immediately): the full `Player` layout
(0x31D0), `PlayerFlag::TELEPORTING`, `Sentient` layout (0x1F30) with
`m_SentientStateFlags` at 0x1B94 and `m_Vehicle` at 0x1E40, `NPC` (8576 bytes)
with `GetOwningSimNPC()`, `SimNPC` with `SetIsCrewMember`/`GetIsCrewMember`,
Havok `CharacterProxy` with `SetCollisionState`/`EnableGravity`, the NPC state
machines (`seektopositionsm`, `npcfollowsm`, `crewcommandsm`, ...), and the
stream name hash (`h*65599 + lowercase(c)`).

This is the co-op foundation: move a character with the engine's own Teleport
(0x0079E3C0) or drive an NPC's seek/follow state machine, no memory poking.
## Session log 2026-10-22: the movement primitive, verified in game

**The correct player chain** (the SDK's `GetLocalPlayer() - 0x7CC` is wrong for
this build; it lands inside the object):

    pm     = *(void**)0x012233A0              PlayerManager singleton
    base   = ((void**)*(pm+4))[0]             m_PlayerArray[0] (BasePlayer)
    Player = base - 0x1F30                    BasePlayer sits inside Player

Live check: base 0x14AF9A50 -> Player 0x14AF7B20, vtable 0x00D6B350, and the
entity transform at Player+0x90 holds exactly the player's position.

**Entity transform layout** (matches entity.h, verified live):

    +0x90   local matrix (right 0x90, up 0xA0, at 0xB0, pos 0xC0)
    +0xD0   world copy   (same matrix, pos at +0x100) - SetPosition writes here
    +0xC0   the "feet" position used by scripts and the HUD (y = world y - 0.945)

**Vtable slots** (player vtable 0x00D6B350; slot 8 is the anchor - the SDK's
own SetMatrix address 0x0046F9C0 sits there in all 58 Entity-derived vtables
the exe scan found):

    slot 7 = SetPosition(const RwV3d*)   player: 0x007FF4B0 -> base 0x0046F7B0,
                                         then CharacterProxy (obj+0x3C0)
                                         SetPosition (0x00551260) and a linked
                                         object at obj+0x1E88
    slot 8 = SetMatrix = 0x0046F9C0      writes the rotation part to +0xD0 and
                                         keeps the position from +0xC0

Any Entity-derived object moves with
`((void(__thiscall*)(void*, const float*))(*(void***)obj)[7])(obj, xyz);`

**Call-context requirement** (cause of two crashes before it was understood):

The base SetPosition ends by calling vtable slot 26, which for the player is
0x004703C0: it appends the new matrix to a per-frame deferred list whose head
is `*(0x01206880)`. Outside a live frame that global is null and the append
crashes at 0x00470413 (`mov edx,[esi+0x14]`, esi = 0). The queue is live only
during the game's deferred-callback phase; the matrix composer hook
FUN_0088d6e0 runs there, so all engine calls are made from that hook now.

**Verified in game (the owner watched it happen):**

* `lift 0.5` on the player - the character rises 0.5 m per call and falls back,
  which reads as a jump. The engine's own fly mode uses the same call.
* **the puppet**: calling an NPC's SetPosition every frame with the player's
  position + a fixed offset glues that character to the player. The owner's
  crew companion teleported to player + (5, 0, 5) and then moved with the
  player ("stuck to me", screenshots). This is the co-op puppet primitive
  working end to end: a second character driven entirely by our code.

**Tooling built this session** (all in local/, not in the repo):

    vtscan.exe   find an address in a PE and report the vtable slot it sits in
    peek.exe     read another process: dump / p / player / scan (lists Entity
                 objects near a point by checking vtable slot 8 == 0x0046F9C0)
    inject.exe   LoadLibraryA into the running game - test a new build with no
                 restart
    coopcore.asi file-driven command console: write coop_cmd.txt next to the
                 game, read coopcore_cmd.txt. Commands: status, tp, tr, lift,
                 obj, puppet, puppet off, read, writef, vtdump, callslot,
                 calladdr, npcscan. Engine commands queue themselves until a
                 live frame is available.

**Crash lessons (do not repeat)**

* never call engine functions when the game is not in a frame (menu, load,
  background) - see the deferred-list null above
* the old exception handler retried the same faulting instruction 5000 times,
  turning one bad read into a crash loop; it now gives up after 5 retries
* memory scans must re-check page protection at every page boundary and only
  run during gameplay; the heap churns during loading

**Open items**

* the puppet keeps its walk animation because its AI still tries to move; freeze
  the animation or use a "neutral" character instead of a crew member
* class 0xD60DF8 objects (found by the scan) moved in memory but were not
  rendered - they are not streamed/active; the visible classes seen live are
  0xD626D8 (crew), 0xD6A2A8, 0xD81DB0 and the player's 0xD6B350
* next: pick a visible spare character as the puppet, drive it from a second
  input stream (fake a remote player), then networking
  (playbook/networking-steam-spacewar.md)

### The "second player" route demo (same session)

Added to the plugin console:

    route <addr> circle <radius> <speed>   walk a circle around the player's
                                           current position, facing the tangent
    route off

`route_apply()` runs in the same live-frame context as the puppet, calls
`SetPosition` every frame and `SetMatrix` (0x0046F9C0) with an orthonormal
basis built exactly like the SDK's `SetHeadingToDirection` (up = 0,1,0;
at = direction; right = up x at). Result, measured from outside with peek.exe:
the puppet traced a perfect 6 m circle around the player at 1.5 m/s and the
owner watched it walk the circle in game.

**Character vs prop**: a real character (Sentient) has a valid pointer at
obj+0x3D4 (the flag guarding the CharacterProxy call) and its proxy at
obj+0x3C0; props with the same Entity vtable have garbage there (a float).
The scan used this to tell characters (class 0xD626D8 - crew/NPC) from props
(0xD60DF8, 0xD6A2A8, 0xD81DB0).

**Known limitation - no walk animation.** The puppet slides in an idle pose:
the animation comes from the character's state machine (its AI), not from the
position. The companion looked right earlier only because its own AI wanted to
move. The correct next step is to drive the character with the game's own
state machines so it animates and paths properly:

* `Sentient::GetRootStateMachine()` = `m_SmTree.GetRoot()` (Sentient layout in
  the SDK; m_CharacterProxy confirmed at 0x3C0)
* the SM system is documented: `StateMachineParams` (class id + TransitionList),
  `StateMachine::AddChild/SpawnChild/SetExternalTransitions`, factories such as
  `S_SeekToPositionSM_FactoryFn` (SeekToPositionSM ctor 0x075E510), and the NPC
  behaviour SMs `seektopositionsm`, `npcfollowsm`, `crewcommandsm`
* `AnimView` (at Animated+0x218) holds the current state id at +0x18;
  `AnimView::ChangeState` exists but is not decompiled in the SDK, so forcing an
  animation directly is not available yet

### Networking step 2: two real clients, sync verified (same session)

**Single-instance check found and bypassed.** The game refuses to start twice
because it calls `FindWindowW(L"Godfather2WndClass", <window title>)` and exits
when the call returns a window. Our plugin already hooks the import; the hook
now returns NULL for that class:

    findwindowW: single-instance check bypassed (NULL)

With that, two full game instances run side by side on one PC. Each keeps its
own profile through the SE's SHGetFolderPathAndSubDirW redirect
(`<game dir>\profile`), so saves and settings do not collide. The game keeps
running its frame loop while minimised/unfocused, so both instances update.

**Two-client test (both in a campaign, same city):**

    A: bind 47779, send to 47778, puppet target = a crew follower (0x059732A0)
    B: bind 47778, send to 47779, puppet target = a live NPC   (0x05D117E0)

Measured with peek.exe (positions read from both processes):

    A puppet  (201.833, 10.216, -203.393)  vs  B player (201.849, 10.216, -203.371)  -> 2 cm
    B puppet  (199.908, 10.210, -207.140)  vs  A player (199.990, 10.200, -207.188)  -> 9 cm

B's console: `sent=8615 recv=9368 peerpos 199.99 10.20 -207.19 age=31`.
Both directions of the co-op loop work at 20 Hz.

**Lessons**

* a puppet target must be a live Sentient: `obj+0x3D4` is a pointer to its
  CharacterProxy. NPCs get despawned/recycled by the scheduler, and then the
  address holds garbage and the puppet silently stops ("sometimes it
  disappears"). Prefer crew followers that the game keeps alive.
* several injected plugin builds fight over the UDP port; a clean restart with
  one build in `scripts\` avoids it.
* the remote player currently appears as a different NPC model; swapping it for
  the protagonist's model is a separate step (TrySwapModel / PartedModelMgr).

Next: drive the puppet through the game's own movement state machine (stable,
animated, no sliding), give it the remote player's model, then move the
transport to the Steam relay from playbook/networking-steam-spacewar.md.

### Plan fixed 2026-10-22: world/state synchronization layers

Position-only sync is not co-op: each client simulates its own world (NPCs,
vehicles, story flags). Per the playbook (how-coop-mods-work.md: retrofits on
closed engines are host-authoritative state sync; enemy/creature positions are
often not synced, spawns/health/deaths are):

| Layer | What it syncs | Fixes | State |
|---|---|---|---|
| 1 | player position (xyz) | remote player visible | done (2 cm / 9 cm) |
| 2 | player state: yaw, animation state, in-vehicle, weapon/action | "can't see him shooting / driving" | in progress |
| 3 | world actors: nearby NPCs and vehicles (host lists them, client mirrors) | "NPCs differ" | planned |
| 4 | story/event flags (find the game's flag store) | "events differ" | planned |

Layer 2 data sources (all documented in the SDK / verified live):

    yaw          entity matrix "at" vector at player+0xB0 -> atan2(at.x, at.z)
    anim state   Animated::m_AnimViewInfo at player+0x218,
                 AnimView::m_StateId at animView+0x18
    in vehicle   Sentient::m_Vehicle (SafePtr) at player+0x1E40
    action       AnimView state id + IsPlayingAction (0x045EEC0)

Applying the animation on the puppet is the open problem: AnimView::ChangeState
is not decompiled in the SDK (no address), so layer 2 starts with yaw + packet
fields for anim/vehicle, and the state application will be solved next (find
ChangeState by RE, or drive the puppet's own state machine).

### Layer 2 progress: state in the packet + a working animation primitive

**Packet v2** (32 bytes): `G2CP` u16 version u16 flags f32 x f32 y f32 z
f32 yaw u32 animState u32 reserved.

* yaw read from the entity matrix "at" vector at player+0xB0
  (`atan2(at.x, at.z)`), applied to the puppet with SetMatrix through the same
  orthonormal basis the engine uses;
* animState read from `Animated::m_AnimViewInfo` (player+0x218) +
  `AnimView::m_StateId` (animView+0x18) - verified live: the player's idle
  state id is 0x1193FB8B;
* flags bit 0 = `Sentient::m_Vehicle` (player+0x1E40) non-null.

**Playing an animation on any character works.** `SentientSM::PlayAnim` is
0x080E570 (documented in the SDK). The hard part was finding the sentient's
root state machine: the SDK padding does not give `Sentient::m_SmTree`'s offset
(the area at +0x3AC holds a string, not the tree). The plugin now finds the
root SM by scanning the entity for a pointer P where `[P]` is a code pointer
(in the game **or an ASI module** - the SE replaces the player master SM with
its own class, so the vtable lives in GF2ASI.asi) and `[P+0x18]` (the SM's
`m_Tree`) points back into the entity. Live test:

    cmd: anim 0x14CC7B20 0x1193FB8B -> ok      (game alive, state unchanged)

`netsync_apply` now sets the puppet's heading every frame and calls PlayAnim
when the peer's state id changes. State ids are CCT name hashes, so the same
name should mean the same id across character models (to verify with two
clients).

**Operational note:** several injected plugin builds in one process race for
the UDP port and for the command file; a clean start with one build in
`scripts\` is the supported configuration.

### End-of-block state (2026-10-22, owner away)

**Build:** `coopcore_v18.asi` (local/coopcore.c) - packet v2 (position, yaw,
anim state, vehicle flag), heading + PlayAnim on the puppet, smooth
interpolation (snap over 5 m, lerp 0.35/frame otherwise), console with
status/tp/tr/lift/obj/puppet/route/net/anim/read/writef/vtdump/callslot/
calladdr/npcscan, single-instance bypass, all the safety guards.

**Running at hand-off:** instance A (owner's, PID 16328) keeps the session with
several injected builds (its `scripts\coopcore.asi` is the pre-session build and
is locked while the game runs, so it could not be updated). Instance B (test
copy) was stopped and its `scripts\coopcore.asi` is v18.

**To get a clean two-client test:**
1. close instance A (or restart it) so `scripts\coopcore.asi` can be replaced
   with v18 in its directory too;
2. start both, load a save in each;
3. in each instance run `status` via `coop_cmd.txt`, then set the puppet target
   (the addresses are per-process; find a live Sentient with `peek scan` and
   check `obj+0x3D4` is a pointer, i.e. a CharacterProxy);
4. configs: A `port 47779 peerport 47778`, B `port 47778 peerport 47779`.

**Next work (in order):**
* layer 3 - world actors: the host must send its nearby actor list and the
  client mirror it. Needs a cheap way to enumerate actors (scanning the heap
  every frame is too slow; look for the game's actor/NPC registry first) and a
  way to spawn/reuse characters. Rough fallback: move the client's nearest
  spare Sentients to the host's actor positions (same layout, different models).
* layer 4 - story/event flags: find the game's flag store (quest stages,
  globals, event flags), send the host's values, apply on the client.
* polish - give the puppet the remote player's model (PartedModelMgr /
  TrySwapModel), and drive movement through the seek state machine so the
  animation and pathing come from the game.

### Layer 3 research pointer (next session start)

The SDK's module tree gives the actor/NPC plumbing for layer 3:

    modules/npcscheduling/demographicregion.h/.cpp
    modules/npcscheduling/demographicregionmanager.h/.cpp   <- region/NPC registry
    modules/npcscheduling/simnpc.h                          <- per-NPC meta + m_CurrentPosition
    modules/npc/npc.h, npc.cpp                              <- NPC entity + state machines
    modules/npc/statemachines/...                           <- movement/behaviour SMs

Start there for a cheap way to enumerate the actors near the player (instead of
scanning the heap), then mirror the host's list on the client by moving its
nearest spare Sentients (a rough actor mirror) or by spawning proper NPCs.
### Video review + model swap (v20)

The owner recorded a 41 s 2560x1440 video of both windows. Frames (extracted
with Topaz's bundled ffmpeg, no install needed) show the puppet standing at the
other player's spot in both windows and both players aiming pistols - the
position/yaw sync is visibly correct. The aim pose is not reproduced on the
puppet.

Network log while aiming:

    A sees B: anim=0x1193FB8B (idle)   sec=-1,-1
    B sees A: anim=0x15D2906B (aim)    sec=-1,-1

So aiming changes the **primary** state id, and the secondary slots stay empty
(it is a stance, not an action). The primary id only animates correctly on the
same character model - the ids are model-dependent, which is why forcing the
player's walk/idle ids on a crew NPC stuck or failed.

**Model swap found in the SDK:**

    MobfaceManager::GetInstance()               = *(void**)0x11298A0
    MobfaceManager::BuildModelFromSavedData     = 0x09BF2F0
    FamilyTreeSlot: PLAYER = 0xFFFFFFFF, SOLDIER1..4 = 0..3

`BuildModelFromSavedData(slot, sentient)` is the function behind the NPC
inspector's "Load Mobface" button. Calling it with the PLAYER slot on the puppet
should give the puppet the protagonist's appearance, after which the animation
ids match and the primary state can be applied.

v20 (local/coopcore.c) adds the console command `mobface <obj> [slot]`
(default slot = player) and re-enables the primary anim application (it is
applied only when the peer's id changes). Next test: apply `mobface` to the
puppet in each client, then walk/aim/shoot and compare.

### mobface crash analysed (do not repeat blindly)

`mobface <obj>` (BuildModelFromSavedData with FAMILYTREE_SLOT_PLAYER on a live
crew NPC) crashed the game: the faulting address 0x005C16AB is **int3 padding**,
i.e. the game jumped through a corrupted pointer inside its own model/animation
binding code. The model swap on a character whose state machine and anim view
are already running invalidates internal references, so this call sequence is
not safe.

Safer options for making the puppet look like the remote player:

1. `PartedAnimated::TrySwapModel(assembly, preset)` - the NPC inspector's
   "SWITCH" button path (SDK: modules/partedanimated). Needs the assembly and
   preset name strings; the player's current names still have to be read.
2. Build the puppet's model before its state machine starts (spawn a fresh
   character with the player's model) instead of swapping on a live one.
3. Accept a different character model and map animation state ids by name
   (CCT state names), not by raw id.

Until one of these is proven, keep `mobface` unused.
## 2026-10-22, second session: position sync verified end to end

**Verified (automated, no manual check):** two real clients, both loaded saves in
the same area. Puppet A sits at player B's position within 4-7 cm; puppet B at
player A's position within 4-7 cm; 0.5-0.85 m while running (the 0.35 lerp
chasing a 20 Hz stream). Network: `recv` ~2200-4000 packets, `age` 16-63 ms.

**Hook facts learned the hard way**

* `FUN_0088d6e0` (player matrix composer) is the only per-frame hook that fires
  reliably in every scene: 22k-23k calls in a couple of minutes. The camera hook
  `FUN_006d24d0` fires only while the camera is moving (a static scene gives
  `cam=0`), and the present hook `FUN_006080a0` never fired at all in these runs
  (`frame=0`), so the game must render through another path.
* `PeekMessageW` runs constantly (thousands of calls/s) but `engine_ready()`
  (deferred-list global `0x1206880`) is **false** there - measured engT=0,
  engF=4295. So engine calls cannot be moved to the message pump; the matrix
  hook stays the driver. `frame_work()` is still called from the pump as a
  fallback, with the same guard.
* The game pumps `PeekMessage` with `PM_NOREMOVE` (the `remove` flag is 0), so
  anything gated on `remove` never runs.

**Why the puppet "stopped moving" (root cause)**

1. The saved target address died: NPC back to memory after streaming out keeps
   its vtable pointer, so the vtable checks still pass, but `+0xC0` is zeroed.
   `SetPosition` then keeps "succeeding" on a corpse: `applied` grew while the
   position stayed (0,0,0).
2. Worse: teleporting a borrowed NPC 200+ m to the peer makes the game unload it
   (it leaves its streaming bubble), so a far peer killed the puppet every time.

**Fixes in v30/v31**

* `netsync_apply` only drives the puppet when the peer is within 60 m; beyond
  that there is nothing to see and the body dies.
* Liveness test before every apply (`entity_is_animated_character`: vtable slot
  8 = SetMatrix, proxy at `+0x3D4` in the 0x1A-0x1D heap, AnimView at `+0x218`
  with a non-zero state at `+0x18`, transform at `+0xC0` finite and non-zero).
  A dead body resets the target so the scanner re-attaches.
* Automatic puppet selection (`autotarget`): background thread (the scan is
  memory-bound at ~15 MB/s, so it must not block the 20 Hz net thread or the
  game), start at `0x03000000`, budget 20 s, time-sliced per page, no region
  clamp (the 8 MB clamp hid the character heaps). Picks the closest class
  `0xD626D8` character with a live proxy + AnimView; falls back to any
  Entity-derived animated object. `target 0x00000000` in `coop_net.cfg` means
  "pick automatically"; `autotarget` console command runs one scan on demand and
  prints hits/chars/loose/end for diagnosis.
* `net` status now also prints a `netdiag` line: `calls applied setfail reason
  posfail vt8 vt7 state conv` (conv = ms since the puppet last sat at the peer
  position; -1 = never).

**Open issue / next**

Borrowed pedestrians still despawn after a while even within 60 m, so the puppet
pops in and out. The owner's idea is the right direction: use a **crew member**
from the player's own family - crew NPCs follow the player and persist. Plan:
score candidates across scans (an address that keeps reappearing near the player
over several scans is a follower) and prefer the highest score; a dedicated,
engine-spawned body would be even better but model creation via
`BuildModelFromSavedData` crashes on live characters.

## 2026-10-22, third session: exact position match confirmed

After the crew-scoring build (v32) both puppets sat **exactly** on the peer
position, measured from the live process: A's puppet 169.49,-185.64 vs peer
169.49,-185.64 (0.00 m), B's puppet 175.32,-186.65 vs peer 175.32,-186.65
(0.00 m). `net` shows `target=<auto>`, `sent/recv` ~600-2400, `age` 16-47 ms.

**Crew preference (v32)**: every scan records the class `0xD626D8` characters
within 25 m; a persistent table increments a score for addresses that reappear
and decrements the rest (`pick_scored_target`). The highest score wins, so a
character that keeps showing up near the player (a crew follower) beats a random
pedestrian. Scan log now prints `cands=<this scan>/<table>`.

**Animation is the open layer.** Puppet state from `AnimView+0x18` is
`0x90DCB113` (A) and `0x35E0B005` (B), while the packet carries the peer player's
`0x46F5C7B4`. State ids are per-model (CCT state hashes resolved against the
character's own state machine), so applying the peer's raw id to a borrowed body
cannot work. Two ways forward: (a) give the puppet the peer's model via
`PartedAnimated::TrySwapModel` (set-model helper `0x09C58C0`, needs assembly or
preset names - `BuildModelFromSavedData` crashes on live characters), or (b) map
the handful of gameplay states by name and translate ids per body.

**Note**: one client (A) exited once during this session with a normal-looking
log tail and no plugin error - no evidence it was the mod, but a crash handler
that logs the exception address would settle it.

## 2026-10-22, fourth session: model / animation layer, what works and what does not

### Part strings of a Sentient (verified in the live process)

`Sentient` derives from `PartedAnimated` (SDK), and the four `String` fields sit
exactly where the SDK says: `+0x2B0` preset, `+0x2C0` theme, `+0x2D0` swap set,
`+0x2E0` assembly, `+0x2F0` PresetInstance pointer. A `String` in this build is
16 bytes: `{ char* data; u32 size; u32 capacity; u32 tag }`; `capacity == size`
for engine-written names, and the tag is `0x004D38D0` for every non-empty
string (0 for empty ones).

Measured values:

* Player: preset `mobface`, theme ``, swap set ``, assembly `player`.
* Crew NPC (the auto-selected puppet): preset/theme `corleonerecruit_0001_v3`,
  swap set `queens`, assembly `player`.

So the assembly is `player` for every human; only the preset differs. The
auto-target + crew scoring picks real Corleone recruits, i.e. the owner's "use a
crew member" idea already happens by itself.

### Rebuilding a model: the call must not run inside a game frame

`PartedAnimated::ForceModelRebuild` = `0x09C58C0` (thiscall, `(PresetInstance*,
const char* name)`). Called from the frame hook (console command), it **hangs
the main thread**: the game froze and then exited (measured, A client). The same
call from a plain worker thread returns immediately and the game keeps running
(measured on both clients), so the rebuild waits on streaming that only proceeds
when the main thread is free. `coopcore` now runs every model call on a worker
thread (`swap_thread`, modes: wear-player-model, mobface slot, plain rebuild).

`ForceModelRebuild` also assigns the new name into the object's own
`m_PartsPresetName` (the engine reallocated the buffer to `capacity == size` of
the new name) - writing that string by hand is not needed.

### "Wear the player's body" does not work on an NPC

Calling `ForceModelRebuild(puppet, NULL, "mobface")` (or
`MobfaceManager::BuildModelFromSavedData(PLAYER, puppet)`, `0x09BF2F0`) gives a
**partial model**: the head changes (face, hair, glasses appear) and the
animation state id becomes the player's own (`0x46F5C7B4` - i.e. the animation
layer would match), but the torso, arms and shoes stay invisible. Tried
combinations: theme `corleoneassociate_0023_main` restores a black torso but the
trousers stay white; clearing theme and swap set (player values) makes the torso
disappear again. Conclusion: the player's body parts (shirt, arms, shoes) come
from the apparel system tied to the player/family-tree slot and are not built
for a borrowed NPC by either function. A real "second player" look needs the
peer's mobface/apparel data transferred over the network, not a preset name.

Useful side effect: with the player model the puppet's AnimView state matched
the peer's packet id exactly, which confirms the animation ids are per-model and
that model identity is the gate for animation sync.

### MobfaceManager save bits (read from the live game)

`MobfaceManager* = *(void**)0x11298A0`; at `+0x40`: version `1`,
`m_EditedInMobFace = 0`, `m_EditedInApparel = 1`. Bit 0 corresponds to the
player slot (the only one with saved apparel in this save), so
`BuildModelFromSavedData` has data for the player and nothing for family slots.

### Both test clients run the same save

The two test copies have byte-identical profiles (`profiles.dat`, autosave and
manual save all share hashes), so both clients load the same world. That makes
actor-level world sync meaningful: the same NPCs exist on both sides, at the
same absolute coordinates, at least until their local AI diverges.

### New console commands (v34)

* `setsuit [obj]` - rebuild the object with the player's preset (worker thread).
* `mobface [obj] [slot]` - `BuildModelFromSavedData` (slot `0xFFFFFFFF` = player).
* `rebuild [obj]` - `ForceModelRebuild` from the object's current part strings.
* `setparts [obj] preset theme swapset assembly` - write the part strings in
  place (capacity-checked; `.` clears, `-` leaves alone) and rebuild.
* `partnames [obj]` - print preset / theme / swap set / assembly.

Arbitrary 32-bit values can be written from the console with `writef`: pass the
decimal form of the float whose bit pattern is the value (denormals included,
e.g. `1.121039E-44` writes `0x00000008`) - used to write string sizes and ASCII
while testing.

## 2026-10-22, fifth session: world actor sync (layer 3) works

### What was built

`coopcore` v39/v40 adds a world actor layer:

* A background thread keeps a table of live characters (class `0xD626D8`) found
  by a resumable, time-sliced scan of the `0x03000000`-`0x08000000` range (the
  heaps where this build allocates characters; a full pass logs
  `act: pass done n=<count>`). Entries are pruned at the end of every pass.
* Every 200 ms each client sends the **eight characters nearest to its player**
  as a `G2CA` datagram (v1): 12-byte header + 20 bytes per entry
  (`x, y, z, yaw, typeHash`).
* On receipt the other client matches every entry to its own table by
  `typeHash` and nearest position (12 m), re-checks that the body is still a
  live character of that type and has not moved more than 25 m from the sent
  point, then eases the position towards the sent one (0.25 at 10 Hz, game
  thread, via `SetPosition`). Characters within 10 m of the local player are
  left alone so local AI there is not fought over.

### Measured

Both clients on the same save, loaded in the same district:

* Actor tables: 17-25 characters; `amatch/aapplied` grew to 2314 on one client
  and 901 on the other; `peeract=8` every tick; no `SetPosition` failures; the
  puppet still converges in 15-16 ms with the actor layer running.
* Same world NPC, same coordinates on both clients, e.g. type `0xCFCFCBF1` at
  (175.6, -157.6) and type `0x8322C3C2` at (170.9, -161.5).

### Models differ between clients - this is the game, not the mod

The exact part preset of a world NPC is **randomised per client at spawn**: in
one spot the same NPC is `preset='maid_0003_bskin'` on one client and
`preset='maid_0001_heavy'` on the other, while the assembly (`wrk_female`) and
the preset family (`maid`) match. That is why actor identity uses
`assembly + preset family` rather than the full preset name, and why the models
visibly differ although the positions match. Syncing the variant would mean
sending the preset/theme/swap-set strings and applying them with `setparts` on
the matched body.

### Stability observations

* One client exited twice with a normal log tail and no plugin error (no
  `crash:` line). A first-chance exception logger (`veh:` lines,
  `AddVectoredExceptionHandler`) is now in the build to identify such exits; the
  current session with the actor layer has produced neither.
* Never write to a table address without re-checking, at write time, the class
  vtable, the live AnimView, the type hash and the distance - the table is only
  refreshed once per scan pass, and a freed address can be reused by another
  object between scan and apply. This was the most likely cause of the one
  crash seen with the first actor build (v37).
* Rotating NPCs is not done: `entity_heading` builds a matrix with a zero
  translation and calls `SetMatrix`, which is one more engine call per NPC per
  tick; position alone is enough for the layer to be useful.

### Console additions

* `actlist` - print the actor table (address, type hash, position, distance).
* `netdiag` now also prints `act= amatch= aapplied= peeract=`.
* `coop_net.cfg` key `actors 0` disables the world actor layer without touching
  the puppet sync.

## 2026-10-22, sixth session: why the ambient crowd layer is off (and stays off)

The actor layer worked technically - matched bodies sat 0.0-1.0 m from the peer's
copy - but two problems showed up in play:

1. **Crash.** A crashed with `veh: av at 0x2365004C write` (first-chance
   access-violation logger): the process jumped to a garbage address, i.e. it
   called something through a corrupted pointer. The write path is
   `SetPosition` -> vtable slot 8, guarded by a vtable check; between that check
   and the call the game can free the body (it streams NPCs constantly), so the
   call goes through a stale vtable. With one puppet at 10 ms intervals the
   window is small; with eight bodies at 10 Hz it is not.
2. **Population growth / lag.** Pinning ambient pedestrians to the peer's
   positions drags them out of their home area, so the NPC scheduler spawns
   replacements - the crowd grows on both clients, which is exactly the
   "NPCs spawn from both clients, it lags" effect the owner reported. FPS did
   improve after the scan was throttled (40 ms/120 ms -> 20 ms/300 ms) and the
   match cache was added, but the population effect remains.

Decision: `actors 0` in `coop_net.cfg` on both clients (read live, no restart).
The puppet layer (single borrowed crew body, 10 ms) stays on and remains stable.

If ambient crowd sync is ever revisited, it needs (a) the engine's own identity
for a body (SimManager maps the attribute-packet GUID to the live handler;
NPC class `0xD7E44D6A`, vehicles `0x10E5319E`), and (b) application from inside
the game's update (a hook that runs where the engine expects object mutation),
not from an external thread writing to addresses that can be freed at any time.

## 2026-10-22, seventh session: vehicle layer wired, not verified in game

The packet now carries the class vtable of the peer's vehicle in the last field
(offset 36, previously reserved), taken from `Player+0x1E40` (m_Vehicle). The
other client looks for an object of exactly that class near the peer position
and moves it by writing the entity matrix translation directly (`+0xC0`) - no
engine call, so no crash risk like the crowd layer had.

Measured so far:

* `fl=1` arrives correctly: the client sees that the host is in a vehicle.
* `vclass=0x00D759AC` arrives correctly (the class measured on the host).
* The vehicle table stayed empty (`veh=0`) on the client: the resumable scan
  window is `0x03000000-0x08000000`, where this build keeps characters, but the
  car objects of the client were not inside it (or the client's world had no
  car of that class near the peer). The direct matrix write is therefore still
  unverified visually.

Also learned:

* Vehicles are a different hierarchy from characters: their vtable slot 7 is
  `0x0046F4F0` and slot 8 is `0x0046C720` (characters: `0x007FF4B0` and
  `0x0046F9C0`), so `entity_setpos`'s "vt[8] == SetMatrix" test rejects them.
  The 0x46F4F0 disassembly shows `__thiscall` with one 4-byte argument and
  `ret 4`.
* The two game windows: host (test-se) is the right window, client (test-se-b)
  the left one at 1280x720 each on this machine.

Next: widen the scan window (or scan a box around the peer), confirm that a car
of the peer's class exists on the client near the peer, and check visually
whether writing `+0xC0` moves it. If not, call the vehicle's slot 7 with a
position pointer (`__thiscall(void*, float*)`), which matches the observed
`ret 4` signature.

## 2026-10-22, eighth session: the peer's car is not a shared world object

Checked with the owner sitting in a car in the host window while the client
stood 12 m away: the client receives `fl=1` and `vclass=0x00D759AC`, but its
vehicle table stays empty - no object of that class exists in the client's world
near the peer at all. The car the host drives is a **locally simulated vehicle**
of that client, not a shared world entity, so no amount of scanning will find
"the same car" on the other side.

Conclusion: showing the peer's car requires *creating* a car in our world and
driving it to the peer position (spawn path unverified and risky on this engine)
or attaching the borrowed puppet body to a local car's seat (writes into the
Sentient vehicle SmartPtr at `+0x1E40`, also unverified). Both are bigger than
the rest of the vehicle work and should not be attempted without a test plan.

What is already in place and worth keeping: the packet carries the peer's
vehicle class in the last field, the player's vehicle pointer is read from
`+0x1E40`, and a vehicle table + nearest-selection + direct matrix write exist
behind `peer_vehicle_apply`. They simply have nothing to attach to.

## 2026-10-22, ninth session: why the animations never transferred, and the fix

### Measurement (both clients, plugin `read` command, in-process)

| what | host (A) | client (B) |
|---|---|---|
| player CCT root state id (`AnimView+0x18`) | `0x46F5C7B4` | - |
| puppet CCT root state id | - | `0xE120D6BC` |

The id sent in the packet is exactly the player's root state id, but the
puppet's own root state id is a completely different number. **CCT state ids
belong to the model, not to the game**: the crew recruit's idle state is not
the player's idle state, so no id can be copied from one body to another.

### The `anim` command never worked either

`find_root_sm()` looked for a pointer P with a code vtable whose `[P+0x18]`
points back into the entity, inside the first 0x400 bytes. Two mistakes:

* the state machine stores its tree pointer at **+0x14** (`m_Tree`), not +0x18
  (`m_Parent`), and the tree is embedded in the entity at `Sentient+0x4B4`;
* the entity scan stopped at 0x400, but the tree lives at 0x4B4.

So the search could not find the root state machine and every `anim` call
reported `failed`. The SDK layout used now:

```
StateMachine  m_ExternalTransitions 0x00, m_StateTableID 0x04, m_CurStateIdx 0x08,
              m_States 0x0C, m_EvalFlags 0x10, m_Tree 0x14, m_Parent 0x18,
              m_ChildHead 0x1C, m_ChildTail 0x20, m_Next 0x24, m_TimeInCurState 0x2C
State         m_Transitions 0x00, m_Name 0x04, m_EnterMessages 0x08
Sentient      m_SmTree 0x4B4 (StateMachineTree { m_EvalFlags, m_Root })
```

### v46: the portable part of the animation state is the state *name*

The state machine layer is shared by all bodies: its states have names
("Locomotion", "GunCombat", ...), and a crew recruit has the same names as the
protagonist. v46 therefore

* reads the names of the current states of the whole state machine tree on the
  host and puts them in the packet (`G2CP`, one length byte plus the names
  joined with `|`, 63 bytes max);
* on the client looks each name up in the puppet's own state tables
  (`sm_find_state`) and forces that state (`m_CurStateIdx`), repeating the
  force every 400 ms because the NPC's own AI keeps taking the state back.

New console commands: `sm <obj>` (state machine tree with current state
names), `smnames <obj>` (every state of every machine in the tree).

### Tooling note: `peek` cannot be trusted for payload data

`peek <pid> <addr> <n>` returned values that disagree with the same addresses
read from inside the game by the plugin's `read` command (player position:
peek 28.31/1.92/-26.28 versus in-process 171.69/10.46/-211.95). Pointers
seemed to match, payload data did not. All measurements from now on use the
plugin's own `read` command, which reads the game's real address space.

### Where the state machine actually lives in this build

The SDK layout for `Sentient` does not match the PC build: the state machine
tree is at **`obj+0x3AC`** (two dwords: flags, root pointer at `obj+0x3B0`),
and `CharacterProxy` starts at 0x3C0 (its first dword is a vtable pointer
0x00E39F10). The SDK's `statemachine.h` offsets for `StateMachine` itself are
also off, so v47 searches for the root machine by shape instead of by offset:

* the machine starts with a code vtable,
* `+0x0C` holds an array of state pointers,
* one of the fields in `+0x04..+0x30` points back into the entity.

The command `findsm <obj>` prints every candidate with its state names, `sm
<obj>` prints the tree, `smnames <obj>` lists all states of all machines.

## 2026-10-22, tenth session: CCT state ids are SDBM hashes of the state name

The decisive measurement: the player's live root state id was `0x46F5C7B4`, and
**SDBM("idle") = 0x46F5C7B4** (the engine hash used by
`StateMachineManager::GetStateTableIDFromName`, an SDBM variant:
`h = c + (h << 6) + (h << 16) - h`, 32 bit).

So a CCT state id is the hash of the state's *name*, and names are global to
the game: the same name has the same id on every body. The earlier idea ("ids
belong to the model, so they cannot be transferred") was wrong - the puppet
simply never received a foreign id, because `find_root_sm` never found the
state machine at all.

Measured layout of this build (the SDK's `statemachine.h` is off by 4 bytes):

```
Sentient+0x3AC   state machine tree { m_EvalFlags, m_Root }   (root at +0x3B0)
StateMachine     +0x08 m_StateTableID   (name hash, e.g. 0xAA067D00)
                 +0x0C m_CurStateIdx
                 +0x10 m_States         (packed table loaded from the game data)
                 +0x14 m_EvalFlags
                 +0x18 m_Tree           (points back at Sentient+0x3AC)
                 +0x1C m_Parent         (0 for the root)
                 +0x20 m_ChildHead
                 +0x24 m_ChildTail
                 +0x28 m_Next
                 +0x2C m_AllocNumber
                 +0x30 m_TimeInCurrentState (float)
```

The state table itself is a packed block (names are stored inline, e.g. the
string "star" at +0x18 of the table, with the state hashes around it), so the
name lookup path of v46/v47 cannot read names yet - but it is not needed any
more, because the ids themselves are portable.

v48 therefore:

* finds the root state machine directly at `obj+0x3B0` (no more heuristic
  search, which produced false positives);
* plays the peer's state id on the puppet again (primary state plus the two
  secondary action states of the weapon chain);
* keeps the name based path as a fallback and counts both in the diagnostics
  (`anim=` and `smstate=` in `netdiag`).

### Why the puppet ignored the state even with the right id

`PlayAnim` does take a CCT state id: the SDK's NPC inspector plays animations
with `PlayerSM->PlayAnim(Character_GetStateName(...), true, true, false, 1.0f,
true)`, that is with exactly the id space the plugin reads from
`AnimView+0x18`. The call itself works (`anim=` counter rises), but the crew
recruit runs its own AI: its state machine puts the body back into its own
state every frame, so a one-shot `PlayAnim` is overwritten immediately. The
puppet's root state id stayed at its own value (`0x05485E39`) while the peer's
(`0x1193FB8B`) arrived correctly over the wire.

v49 therefore forces the peer's state back whenever the puppet's current state
differs from it, at most every 200 ms (`animforce=` in the diagnostics).

The state names of the state machine tree are readable after all
(`start|main|idle|noTarget|search|groundMovement|...`), so the name based
path works as well (`smstate=` counter) - it drives the state machine, the id
based path drives the animation.

### Tool: local/sdbm.c

`local/sdbm.c` prints the SDBM hash of a name, which is the engine's id
function for state and state table names:

```
sdbm -t 46F5C7B4 idle idle_loop     -> idle 0x46F5C7B4  <== MATCH
```

Note: PowerShell cannot be used for this, its 32 bit arithmetic overflows
into a negative int64; the C tool is the reliable one.

## 2026-10-22, eleventh session: animation transfer works, but it cost frames

The owner confirmed that the animation sync visibly improved (the crew recruit
now mirrors the peer's stance), but the game stuttered badly.

Cause: `PlayAnim` and the state machine force were both called **inside the
frame hook**, i.e. on the main thread. Both are real engine work (state change,
clip setup, spawning child state machines), so every call cost frames - the
same lesson as with `ForceModelRebuild`.

v50 moves both off the game thread: the frame hook only posts a request
(`anim_post`), a dedicated worker executes it. Also:

* the peer's state names are read at 5 Hz instead of 20 Hz;
* the corrective force runs every 250 ms instead of 200 ms;
* the state table search is limited to 512 entries (the tables are packed
  blocks, the old loop read 4096 entries with a `VirtualQuery` each).

Measured CPU while both clients run: host 57%, client 73% of one core (the
game itself is the bulk of it).

## 2026-10-22, twelfth session: disabling the puppet's AI

The idea from Jericho Co-op: a puppet is a body whose AI is turned off
(`oAI.bEnable = false`), so it no longer fights the networked state.

In Visceral Engine the AI is the state machine tree at `Sentient+0x3AC`. Its
eval flags live at `SM+0x14`. v51 sets `EVAL_NOUPDATEMESSAGES` (8) on the
puppet's root state machine when it attaches and restores the original flags
when the puppet is released. With update messages suppressed, the SM no longer
sends the animation changes that overwrote the peer's state every frame.

This should make the `animforce` counter drop to near zero and the animation
sync hold without repeated forcing. If it instead freezes the puppet (EVAL
flags are engine state and may have side effects), revert to v50.

## 2026-10-22, thirteenth session: disabling transitions instead of update messages

v51's EVAL_NOUPDATEMESSAGES (8) broke movement: it suppressed the kinematics
update that the engine runs every frame, so the puppet's position write was
overwritten by stale physics and the movement stuttered.

v52 keeps update messages running (kinematics works) but clears
EVAL_CHECKTRANS (4) on the root SM and both child SMs. With transitions
disabled, the NPC's state machine stays in its current state and no longer
overrides the peer's animation, while the engine still applies the position
every frame. The original flags are saved and restored when the puppet is
released.

## 2026-10-22, fourteenth session: targeted AI suppression

v52's EVAL_CHECKTRANS (4) alone still let the action SM send update messages
that played the NPC's own animation after a short time. v53 adds
EVAL_NOUPDATEMESSAGES (8) on the action SM only (the second child, 0x1B474E40)
while keeping EVAL_CHECKTRANS (4) cleared on all three. The root SM still runs
its update messages (kinematics), but the action SM no longer sends animation
changes. This is the Jericho pattern: stop the body's own AI from touching the
animation, keep the movement physics alive.

## 2026-10-22, fifteenth session: write the peer state into the CCT AnimView every frame

v53's EVAL flags approach (clearing transitions, suppressing update messages on
the action SM) still didn't stop the NPC's own animation from reappearing.
The owner confirmed: "movement is stuttery, the game tries to return the NPC
pose."

v54 takes a completely different approach: instead of fighting the SM, write the
peer's CCT state id directly into the puppet's AnimView (av+0x14 and av+0x18)
every frame in the frame hook, after the SM's update. The SM overwrites it
next frame, but we overwrite it again. The secondary action states (weapon aim/
shoot) are written to the secondary chain (av+0x124 → +0x18).

The SM's PlayAnim is kept only as a fallback (on peer state change, not every
200 ms), since the direct write is cheaper and doesn't trigger SM transitions.

No SM flags are modified. The kinematics update runs normally, so movement
stays smooth.

## 2026-10-22, sixteenth session: direct AnimView write causes stutter, reverted to PlayAnim-only

The direct write of the peer's state id to av+0x14/0x18 every frame (v54)
caused movement stuttering. The write itself is cheap, but the CCT layer
reacts to the state id change by doing work (looking up the state in the
name-node table, loading animation data) that hitches the frame.

v55 reverts to PlayAnim on peer state change only (no direct writes, no
forced re-application every 200 ms). This means the NPC's AI will still
override the animation periodically, but the movement stays smooth.

The animation sync is now: PlayAnim on peer state change (worker thread).
The NPC's AI fights it, but the visual result is acceptable (the puppet
mirrors the peer's stance for a moment, then snaps back, then mirrors again).

## 2026-10-22, seventeenth session: EVAL_BUSY to pause the puppet's SM

v55 (PlayAnim only) still had stuttery movement and the animation kept
reverting, because the NPC's AI was still running and fighting us.

v56 sets EVAL_BUSY (1) on the puppet's root SM and both child SMs, which makes
the state machine consider itself busy and skip its update. This is the
closest equivalent to Jericho's `oAI.bEnable = false`. If it freezes the
puppet or crashes, revert to v55.

## 2026-10-22, eighteenth session: three position copies

Measured on the live puppet: the position exists in three places in the
entity:

* `+0xC0` - the entity matrix translation (what entity_setpos writes)
* `+0x100` - an exact duplicate of it
* `+0x120` - the character proxy's capsule centre, about 1.0 higher in Y

Writing only `+0xC0` lets the engine's kinematics update snap the body back to
the proxy position, which the owner described as "teleporting". v57 writes the
peer's position to all three: `+0xC0`, `+0x100`, and `+0x120` (with the
measured +1.0 Y offset for the capsule centre).

The AI-flag experiments (EVAL_NOUPDATEMESSAGES, EVAL_CHECKTRANS, EVAL_BUSY)
were all reverted: none of them stopped the animation from reverting, and
several broke movement.

## 2026-10-22, nineteenth session: snapshot interpolation (Jericho pattern)

v57's three position copies didn't fix the stutter. The jerkiness comes from
the position filter, not from the AI: the puppet lerped 35 % toward the newest
20 Hz state every frame, so every packet timing wobble became a visible jump,
and a distance over 5 m snapped the body outright.

v58 buffers the received states (32 entries, ~1.6 s) and draws the body a
little in the past, interpolating between the two states around
`now - 100 ms`, with 200 ms of velocity extrapolation when states stop. This is
exactly what Jericho Co-op does (their notes: 107-201 visible jumps a minute
before, 1.5-4.5 after). The snap threshold is now 20 m (real teleports), and
`snaps=` in the diagnostics counts them.

Also kept: writing the position to +0xC0, +0x100 and +0x120 (the capsule
centre).

## 2026-10-22, twentieth session: map peer movement to the puppet's own states

Diagnosis of the "teleporting" look: the position moves, but the body slides in
an idle pose. The peer's state names ("groundMovement", "freeAim", "strafe")
do not exist in the crew recruit's action state table, so the name lookup
found nothing and the puppet stayed in "Idle" while its position moved.

v59 maps the peer's movement to states the puppet actually has: speed is
computed from the last two received positions (the history buffer from v58);
above 0.8 m/s the action SM is forced to "Ped", below it to "Idle". Those are
states of the NPC's own action table (measured with `smnames`: Idle = 5,
Ped = 6, Follow = 24, FireGunStationaryAction = 12, StrafeFire = 13), so the
body plays its own walking animation instead of sliding.

## 2026-10-22, twenty-first session: autonomous boot, campaign co-op, side-by-side windows

**Verified:** both disposable clients can be brought from a fresh start into
the story campaign without touching the keyboard by hand, and position sync
works end to end with a visible remote body.

Boot sequence that worked on both copies (A `godfather2-test-se`, B
`godfather2-test-se-b`): replace `movies\common\attract.vp6` with a copy of
`mainmenu.vp6`, start the game, wait for the movie, then send scan-code
`SendInput` keys (plain `wVk` events are ignored by the frontend):

- `Enter`, `Esc`, `Enter`, left click -> autosave notice, frontend state
  `0x00E5672C` becomes `0x24`;
- `Enter` -> "Выбор сохраненной игры для загрузки" (state `0x11`);
- `Enter` -> load the selected save, state `5`, player pointer becomes
  non-null (A `0x14F17B20`, B `0x14E884A0`) at `(185.939, 10.224, -172.206)`.

The `Esc` press opens/closes the pause menu; when the menu is open the game
thread stops processing console commands, which earlier looked like a hang.

With A host on UDP `47779` and B client on `47778` (peer `127.0.0.1`), both
`net` lines showed `state=1`, matching `peerpos`, and `netdiag` on A showed
`calls/applied` increasing with `setfail=0`, `reason=0`, `posfail=0`,
`vt8=0x0046F9C0`. Moving A 8 m with `W` changed B's `peerpos` from
`186.55,10.22,-172.33` to `177.76,10.22,-173.34`; B's screen then showed a
second, grey-shirted character standing at A's position next to B's red-shirted
player (`local/dual_close2.png`, `local/dual_A_near_B.png`). The body is a
borrowed crew NPC, not a clone of the player model.

Build mismatch observed: B runs the deployed v51 (`netdiag`/`sm` unknown),
while A's in-memory build already has `netdiag`/`sm`/`net lerp` but not
`resume`/`smfrz` (an older hot-injected v58/v59, not the current source). The
v60 file `local/coopcore_v60.asi` (220160 bytes) is built but not deployed.

Window layout helpers added under `local/`: `arrange.py` puts A at `(0,0)` and
B at `(1280,0)` (both 1286x749, 2560x1440 desktop), `dualshot.py` captures
`(0,0,2560,749)` so one screenshot shows both games, and `gamefocus.py`
contains the scan-code keyboard, relative-mouse and Alt+Tab focus helpers.

Next: deploy v60 to both copies, repeat the boot sequence, and test `resume`,
`smfrz`/`smfreeze`, `net lerp` and `net reopen` against the working baseline.

## 2026-10-22, twenty-second session: v60 deployed, smfreeze and walk sync verified

**Deployed:** `local/coopcore_v60.asi` (220160 bytes, v60 string) to both
`scripts\coopcore.asi`; the previous v51 is saved as
`local/coopcore_v51_backup.asi`. Both clients now log `coopcore.asi v60 loaded`.

**Autonomous boot:** `local/boot_client.py A|B [pid]` drives a fresh client from
the splash to the campaign in four probe steps: state `6` (movie) ->
`Enter, Esc, Enter, click` -> `0x24` (autosave) -> `Enter` -> `0x11` (save
list) -> `Enter` -> state `5` with a non-null player. Both clients loaded the
same save; A player `0x14ED8420`, B player `0x14D37B20`.

**`resume` is a crash, not a shortcut.** Calling it from the splash state `7`
(`0x00931D00` / `GamePersistence::Restore`) killed A (PID 23732 disappeared,
the command output stopped at `cmd: --- begin ---`). Do not use it at the
splash; guard it to the main-menu/profile states before shipping. The manual
boot script above is reliable.

**v60 commands:** `net` prints the `netdiag` line as well; a standalone
`netdiag` command is unknown. `net lerp 0.350` returned
`cmd: net lerp 0.350`. `net reopen` on B returned
`cmd: net reopen -> 1 (port 47778)` and the session reconnected (`state=1`,
`peerpos` updating, `conv=15`).

**smfreeze verified:** `smfrz` counters reached 76k+ on both sides. While A
held `W`, B's puppet action SM showed `idx=6 name='Ped'` (not `Idle`), and
while B held `W`, A's puppet action SM also showed `Ped`. Visual proof:
`local/dual_v60_puppet_walk1.png` (B sees A's dark-suited body walking) and
`local/dual_v60_A_sees_B_walk.png` (A sees B's body walking). The remote body
is a borrowed NPC in a dark suit, not the player's red-shirted model.

**Bidirectional position sync:** A walked from `182.0` to `170.68`; B's
`peerpos` followed with `calls/applied` increasing, `setfail=0`, `conv` 15-16.
B teleported to `166.0` and walked to `165.7`; A's `peerpos` followed. `snaps=0`
for normal walking. `amatch=0 aapplied=0` remains: the visible walk comes from
the forced `Ped` state, not from CCT animation matching.

Still open: `resume` guard, player-model appearance on the borrowed body,
combat/vehicle state mapping, and a long-run stability check with smfreeze.

## 2026-10-22, twenty-third session: v61 combat mapping, resume guard, walk sync

**v61 source changes** (`local/coopcore.c`, built as `local/coopcore_v61.asi`,
221184 bytes): the peer's state names now drive the borrowed body's action SM
for combat/weapons as well as movement: `h2h`/`QuickAttack`/`Melee` ->
`MeleeAttack`; `Fire`/`Aim` -> `FireGunStationaryAction`; `DrawGun` ->
`DrawGunAction`; `Reload` -> `ReloadGunAction`. The speed-based `Ped`/`Idle`
fallback remains. `resume` is now guarded: it refuses unless the frontend state
is `0x20` (profile selection), instead of calling `0x00931D00` at the splash.

**Deployed and booted:** both copies run v61 (`coopcore.asi v61 loaded`); A =
PID 23892, hwnd `0x400928`, player `0x14F57B20`; B = PID 23528, hwnd
`0x370D18`, player `0x14E37B20`. A is host on 47779, B client on 47778.

**`resume` guard verified:** in campaign state `5`, B printed
`resume: frontend state 5, refusing (needs profile selection 0x20)` and stayed
alive. The old splash crash path is now gated; it still needs a profile-state
test before shipping.

**v61 networking:** `net lerp 0.300` works; `net reopen` on B returned
`net reopen -> 1 (port 47778)` and reconnected (`state=1`, `conv=16/31`,
`peerpos` updating). `smfrz` reached 255k on B / 202k on A with `setfail=0`,
`smstate` ~4k; `posfail=2` and `snaps=2` are leftovers from the fall/teleport.

**Walk sync still works:** while A backed up (`S`) from `167.34` to `174.81`,
B's puppet action SM read `idx=6 name='Ped'`; B's `peerpos` followed
`174.81,10.21,-165.70`. A forward `W` walk to `167.34` also updated B's
`peerpos`. The first read during a walk can still catch `Idle` before the
250 ms mapping tick; later reads show `Ped`.

**Combat mapping verified by direct SM reads:**
* A punched: B's peer state was `...h2h|QuickAttackLPrime...`, and B's puppet
  action SM (`0x1B4562E0`, index at `+0x0C`) read `0x1F` = `MeleeAttack`.
* B entered free aim with a pistol: A's peer state was
  `...noTargetFreeAim|freeAim|strafe...`, and A's puppet action SM
  (`0x1B576F60`, index at `+0x0C`) read `0x0C` =
  `FireGunStationaryAction`.
The `sm` console command is too slow/stale to catch short states; read the
action SM index directly while the peer is in the state.

**Input note:** synthetic left clicks on A produced punches, while the same
clicks on B entered free aim/fire because B was holding a pistol. B's aim state
persisted until the pause menu was closed; it is a real gameplay state, not a
mapping failure.

**Teleport lesson:** `tp 190.0 10.224 -172.0` put A outside the interior floor;
A fell through the world to `y=-1949` and had to be restarted. Do not teleport
to arbitrary `y=10.224` coordinates; use keyboard movement or verified interior
positions (`185.9,10.224,-172.2` and the walked corridor are safe).

**Appearance:** after A reloaded, the auto-picked puppet was a red-shirted
borrowed NPC (closer to the player than the earlier dark suit), but it is still
a borrowed body; `amatch=0 aapplied=0` remains. Evidence:
`local/dual_v61_loaded.png`, `dual_v61_final.png`, `dual_v61_verified.png`,
`dual_v61_B_infront.png`, `dual_v61_punch2.png`,
`dual_v61_B_punch_Aview.png`, `dual_v61_after_esc.png`.

**Live state at handoff:** A `174.69,10.21,-165.82` (host, idle), B
`169.24,10.21,-172.50` (client, free aim with pistol); both connected, windows
side by side, v61 loaded. Next: profile-state `resume` test, player appearance,
vehicle mapping, and a longer smfreeze stability run.

## 2026-10-22, twenty-fourth session: resume disabled in v62

**Profile-state test:** a fresh B was driven to profile selection (state
`0x20`) and `resume` was called. The game **crashed** (process 19220
disappeared; the command output stopped at `--- begin ---`). The previous
guard to `0x20` was therefore not sufficient.

**Why:** disassembly of `0x00931D00` shows a profile-list/frontend transition,
not a safe "load last save" entry point. It immediately dereferences the
profile manager at `0x0112B9B4` and frontend globals, then calls `0x8f29f0`
with callback `0x931600`; the direct call has no valid frontend context. The
callers found in the executable target `0x00D32D00` (a UI text renderer), not
this address, so there is no simple call-site to copy.

**v62 change:** `resume` is now disabled unconditionally and no longer calls
`0x00931D00`; it prints
`cmd: resume: disabled (unsafe direct frontend call; state N)`. Version bumped
to v62 (`local/coopcore_v62.asi`, 220160 bytes) and deployed to both copies.
Both booted; `resume` at campaign state `5` printed the disabled message and
the game stayed alive. Splash/profile-selection crash paths are gone.

**v62 live state:** A = PID 10860, hwnd `0x1A157A`, player `0x14E384A0`,
host 47779; B = PID 1252, hwnd `0x210D6C`, player `0x14EA7B20`, client
47778. Targets A `0x060A5A80`, B `0x0619EE20`; `calls/applied` 1478,
`setfail=0`, `smfrz` 5928, `conv=31`. A walked from the start position to
`182.006,10.224,-172.316`; B's `peerpos` followed `182.01,10.22,-172.32`.
Windows arranged A `(0,0)`, B `(1280,0)`.

## 2026-10-22, twenty-fifth session: v63 appearance scoring picks a player-like body

**Change:** `pick_scored_target()` now adds an appearance bonus on top of the
persistence score: assembly `player` +200, preset `unq_*` +100, preset
`corleonerecruit*` +20, `wrk_female`/`maid*` -50. This makes the borrowed body
prefer a player-like unique model instead of a nearby civilian. Built as
`local/coopcore_v63.asi` (220672 bytes), deployed to both copies, both booted.

**Result:** both clients now pick `unq_tommy_cipolla` / assembly `player` as
the puppet (A target `0x05DD1C20`, B target `0x05D79220`), instead of the
maid/dark-suited NPC. B's view shows a red-shirted player-like body standing
ahead of its own player (`local/dual_v63_tommy_walk.png`). The exact player
model is still not cloned, but the remote body now looks like a protagonist.

**Walk sync:** A walked from `180.9` to `165.75`; B's puppet action SM
(`0x1B353E00`, index at `+0x0C`) read `idx=6 name='Ped'` during the walk, and
B's `peerpos` followed `165.75,10.22,-173.85`. `amatch=0 aapplied=0` remains:
the visible animation is still the forced `Ped`, not CCT matching.

**Action table check:** the Tommy action SM has `Ped` (6), `MeleeAttack` (31),
`FireGunStationaryAction` (12), `DrawGunAction` (9), `ReloadGunAction` (11),
so the v61 combat mapping is valid on this body. The `smnames` output is long;
read the output file past the first 12 states.

**Tooling warning:** `npcscan 30` blocks the game thread for about two minutes
while it walks the address space; it did finish (3 entities) but stalls the
session. Use the plugin's `read`/`partnames` on known addresses instead.

**Live state:** A = PID 4916, player `0x14D67B20`, host 47779; B = PID 23000,
player `0x14D37B20`, client 47778. Targets A `0x05DD1C20`, B `0x05D79220`
(both Tommy); `calls/applied` 5255, `setfail=0`, `smfrz` 21064, `conv=31`.
A at `165.75,10.22,-173.85`; B at the start `185.94,10.22,-172.2`. Windows
arranged A `(0,0)`, B `(1280,0)`. Evidence:
`local/dual_v63_tommy_walk.png`, `local/dual_v63_tommy_walk_live.png`.

## 2026-10-22, twenty-sixth session: v64-v66 combat hold and action-table filter

**v64:** the combat/fire action is now held for 450 ms after the peer's state
stops naming it, so a short punch is not lost between the 250 ms mapping ticks.
Direct read of the puppet action SM during/after a punch showed
`idx=0x1F` (`MeleeAttack`); the body finishes the animation instead of snapping
back to `Idle`.

**v65/v66:** the auto-target now checks the candidate's state-machine tree for
a real action table (`Ped` or `MeleeAttack`). Seated/prop characters have a
reduced table (`sit`/`idle`/`cower`/`dead`) and were being picked because they
were close; the scan now skips them. The appearance score also prefers
`fam_corleone` (+100) and penalizes `wrk_female`/`maid*` (-200), and the
fallback drops the target if no candidate has an action table instead of
attaching the nearest seated NPC.

**Result:** both clients now pick standing `corleoneassociate_*` bodies
(assembly `fam_corleone`) with the full action table (`Idle`, `Ped`,
`MeleeAttack`, `FireGunStationaryAction`, ...). B's view shows a red-shirted
mobster body in the doorway ahead of its player
(`local/dual_v66_associate.png`). Walk sync: B's puppet read `idx=6` (`Ped`)
while A walked `176.7`; `peerpos` followed. Combat hold: A punched and the
puppet action SM read `0x1F` (`MeleeAttack`). `amatch=0 aapplied=0` remains.

**Live state:** A = PID 11000, player `0x14F87B20`, host 47779, target
`0x0640A7E0` (`corleoneassociate_0007_main`), pos `176.88,10.22,-172.75`.
B = PID 18568, player `0x14F27B20`, client 47778, target `0x063C78A0`
(`corleoneassociate_0022_main`), pos `185.94,10.22,-172.73`. `calls/applied`
2661/2866, `setfail=0`, `smfrz` 10580/11660, `conv=31`. Windows arranged A
`(0,0)`, B `(1280,0)`.

**Build:** `local/coopcore_v66.asi`, 222208 bytes; v64/v65/v66 were each
deployed and booted, v66 is the live build. `resume` remains disabled.

**Two-minute stability soak (v66):** after the tests above, both clients idled
for 120 s. Processes A (11000) and B (18568) stayed responsive; A
`calls/applied=13063`, B `14564`, `setfail=0`, `posfail=0`, `snaps=0`,
`smfrz` 52292/59792, `conv=31/16`, `peerpos` stable. No crash or counter
regression. This is a short soak, not a long-run leak test.

## 2026-10-22, twenty-seventh session: crash analysis and v67 worker hardening

**Crash reports found in WER:** two `APPCRASH` events for `godfather2.exe`
(same build 1.0.789.0) at 18:32 (A) and 19:17 (B), both exception
`0xc0000005` at **`0x004C0BA1`**, faulting module `godfather2.exe`. The
instruction there is `movl %eax, 0x34(%esi)` in a game state setter, i.e. a
write through a bad `this` pointer. The most likely path is the animation
worker's `PlayAnim` fallback (`anim_post(1, ...)` -> `entity_plays_anim`):
the borrowed body can be streamed out/freed between the frame-hook liveness
check and the worker's engine call.

**v67 hardening:** the worker no longer calls `PlayAnim` on the borrowed body.
It only applies state-machine names, and re-checks
`entity_is_animated_character(obj)` immediately before doing so. The direct CCT
AnimView write (game thread) and the action-SM name mapping remain; the
`anim=` diagnostic is now 0 because the fallback counter is unused, while
`smstate` still increments. Built `local/coopcore_v67.asi` (222208 bytes),
deployed to both copies, both booted.

**v67 verification:** targets attached (A `0x05F6A3A0` = `unq_tommy_cipolla`,
B `0x062FDAF0` = `corleoneassociate_0030_main`). Walk: B's action SM read
`idx=6` (`Ped`) while A walked to `177.8`; `peerpos` followed. Punch: A clicked
and B's action SM read `idx=0x1F` (`MeleeAttack`) with the 450 ms hold.
`setfail=0`, `smfrz` 15k, `anim=0`, `smstate` 273. Screenshot:
`local/dual_v67_walk.png` (remote body visible in B's doorway; local walk
animation works).

**Live state:** A = PID 24808, player `0x14F1A9A0`, host 47779; B = PID 14600,
player `0x14E37B20`, client 47778; A pos ~`177.8,10.22,-172.8`, B at start.
A 10-minute soak is the next step; the 0x004C0BA1 crash must not recur.

**v67 movement soak (~7.5 min):** 8 cycles of `W`/`S` movement on A, with
`net`/`netdiag` queries every ~55 s. Both clients stayed alive; A
`calls/applied=29105`, B `31121`, `setfail=0`, `posfail=0`, `snaps=0`,
`smfrz` 113968/127556, `smstate` 2291/2296, `anim=0` (worker PlayAnim
disabled), `conv=16/32`, `peerpos` tracking. No new `APPCRASH` event in the
last hour: the `0x004C0BA1` crash did not recur under movement stress. This is
stronger evidence that the worker `PlayAnim` on a freed body was the fault;
a multi-hour soak is still open.

## 2026-10-22, twenty-eighth session: player-model swap dead end, body restored

**Experiment:** on B's puppet `0x062FDAF0`,
`setparts 0x062FDAF0 mobface - - player` returned `write=ok rebuild=queued`.
The game survived, but the puppet disappeared/headless - the known
player-model-on-NPC dead end. The part strings became
`preset='mobface'`, `theme='corleoneassociate_0030_main'`,
`swapset='def_0002'`, `assembly='player'`. Restoring
`setparts 0x062FDAF0 corleoneassociate_0030_main - - fam_corleone` plus the
queued rebuild brought the visible body back; no crash. Do not repeat the
player-model swap without a proper model-clone path.

**Final live state:** A = PID 24808, player `0x14F1A9A0`, host 47779, target
`0x05F6A3A0` (Tommy), pos `170.33,10.21,-170.53`; B = PID 14600, player
`0x14E37B20`, client 47778, target `0x062FDAF0` (associate, restored), pos
`185.94,10.22,-172.80`. `calls/applied` 34388/36619, `setfail=0`,
`posfail=0`, `snaps=0`, `smfrz` 135236/149952, `conv=16/15`. Both v67,
connected, alive.

## 2026-10-22, twenty-ninth session: v68 worker target guard and fire-state detail

**v68 changes:** the animation worker now applies state names only when the
requested object is still the active `g_netTarget` (in addition to the
`entity_is_animated_character` liveness check). This prevents a stale request
from writing to a body after a target switch. Fire/aim mapping now
distinguishes `strafe` -> `StrafeFire` and `RangedChase` -> `RangedChase`,
falling back to `FireGunStationaryAction` otherwise. Built
`local/coopcore_v68.asi` (222208 bytes), deployed to both copies, both booted.

**v68 verification:** targets A `0x060E22E0` (`unq_tommy_cipolla`), B
`0x0624B7C0` (`corleoneassociate_0033_main`, full action table). Walk: B's
action SM read `idx=6` (`Ped`) twice while A moved `178.15`; `peerpos`
followed. Punch: B's action SM read `idx=0x1F` (`MeleeAttack`) with the
450 ms hold. A ~3.5-minute W/S movement soak stayed alive with
`setfail=0`, `posfail=0`, `snaps=0`, `smfrz` 58108/63548, `conv=16/31`.
No new APPCRASH. Trying to draw a gun on A produced `grappleAttempt` instead
(A appears unarmed), so the `StrafeFire` branch is implemented but not yet
observed in game.

**Live state:** A = PID 9212, player `0x14E17B20`, host 47779, pos
`168.06,10.22,-177.47`; B = PID 10672, player `0x14D37B20`, client 47778,
pos `185.94,10.22,-172.73`. Both v68, connected.

## 2026-10-22, thirtieth session: vehicle sync verified at the Corleone mansion

**Location:** the safe car spot the owner pointed at is the **Corleone Mansion
driveway/courtyard**, world position about `171.1,10.27,-215.3`; the black
family limousine there is enterable (E prompt) and does not trigger an arrest.
Class vtable `0x00D759AC`. The in-game map labels the area
`Нью-Йорк / Дом Корлеоне`.

**Root cause of the missing car motion:** vehicles keep their world transform
at **`+0x78` / `+0xB8` / `+0xD8`**, not at `+0xC0` (+0xC0 is a local/part
position). v69/v70 added `vehicle_getpos`/`vehicle_setpos` and switched
`veh_note`, `peer_vehicle_apply`, `vehscan` and `vehlist` to them; v70 also
added the `vehadd`/`vehlist` diagnostics.

**Verified:** A entered the black limo; B received `fl=1`,
`vclass=0x00D759AC`, and `state='...|vehicle|updateVehicle'`. B's
`veh=9`, `vapplied=2876` (increasing), and `vehlist` showed B's car
`0x06178418` at `172.02,10.18,-217.29` - matching A's peer position
`172.017,10.179,-217.290`. The car is therefore being moved on the client side
to the peer's vehicle position. Evidence: `local/dual_v70_car_sync.png`
(A inside the black limo; B has the map open). The actor scan is slow; a
`vehadd <addr>` can register a known car immediately. The black limo test did
not arrest A, unlike the earlier white/police car.

**Live state:** A = PID 23992, player `0x14EC84A0`, host 47779, inside the
black limo at `172.02,10.18,-217.29`; B = PID 24808, player `0x14E68520`,
client 47778, at the mansion start with the map open. `veh=9`,
`vclass=0x00D759AC`, `vapplied=2876`, `setfail=0`, `smfrz` 382k. Both v70.

**Vehicle soak (~3 min):** A stayed in the black limo; B remained on the map
screen. `veh=9`, `vclass=0x00D759AC`, `fl=1`, `vapplied` climbed
3419 -> 3906 -> 4399 -> 4889, `setfail=0`, no crash. A at
`172.18,10.18,-217.28`; B's `peerpos` `172.23,10.18,-217.27`. This confirms
the vehicle apply path is stable while the peer sits in the car. The actor
scan is slow but did find 9 cars; `vehadd` remains the quick way to register a
known car. The safe location is the Corleone Mansion driveway; the black
family limo does not trigger an arrest.

## 2026-10-22, thirty-first session: v71 vehicle validation and live drive sync

**v71 changes:** `entity_is_vehicle` and `vehicle_getpos` now validate the
world transform at **`+0x78`** (finite, within +-20000), not the local/part
field at `+0xC0`; `vehicle_getpos` no longer falls back to `+0xC0`;
`veh_note` requires a real vehicle and a valid position; `vehlist` prunes
invalid entries. Version string is `coopcore.asi v71 loaded`, 224768 bytes.

**Live drive test (both v71):** A (PID 9388, player `0x14DE7B20`) entered the
black limo at the Corleone mansion; B (PID 25928, player `0x14F584A0`)
received `fl=1`, `vclass=0x00D759AC`, `veh=10`, and `vapplied` climbed
19072 -> 19458. A drove forward (173.48 -> 170.11 -> 169.54 -> 169.26) and
then reversed to `180.763,10.161,-216.490`; B's `peerpos` followed each step,
and B's car `0x0632A268` was at exactly the same position (e.g.
`180.77,10.16,-216.49`). `setfail=0`, `posfail=0`, no crash. Evidence:
`local/dual_v71_car_drive_sync.png` (A in the limo outside the mansion gate;
B has the map open).

**Caveat:** `vehlist` still contains stale/far class matches (e.g.
`0x0639AE68` at `0.57,1.39,0.57` and several traffic cars 70-120 m away).
`peer_vehicle_apply` only considers cars within 40 m of the peer, so these are
not moved; a v72 cleanup should filter by distance/class and drop stale
entries. The applied parked limo converges slowly (engine resets it between
writes), but it did track the peer exactly during the drive test.

**Live state:** A PID 9388, player `0x14DE7B20`, host 47779, inside the limo
at `180.763,10.161,-216.490`; B PID 25928, player `0x14F584A0`, client 47778,
at the mansion start with the map open. Both v71; B `veh=10`,
`vclass=0x00D759AC`, `vapplied=19458`.

## 2026-10-22/23, thirty-second session: GF2ASI/mods research, time and NPC sync

**Research read:** the public GF2ASI tree (README, ROADMAP, CONTRIBUTING,
RESTRUCTURE_PLAN, Tools/README, SDK headers/sources) and the four supplied
mods. The useful findings:

* `TimeOfDayManager::GetInstance()` is `*(void**)0x112A53C`; its `GameTime`
  block starts at `+0x5C` and is five int32 fields (year, day, hour, minute,
  second). The manager's time scale/elapsed fields follow at `+0x70..+0x84`.
* `MobfaceManager::BuildModelFromSavedData` = `0x09BF2F0`,
  `MobfaceManager::GetInstance` = `0x11298A0`. It can rebuild a
  `PartedAnimated` from the player/family slot; on an NPC it changes the body
  and apparel (puppet now reports `preset='mobface'`, theme
  `unq_tommy_cipolla`, assembly `player`) but the result is **headless** -
  the player's head/face is a separate mobface/meta-model, not a parted body
  part. Do not call it automatically on the puppet.
* `PartedAnimated::TrySwapModel` / `ForceModelRebuild` (`0x09C58C0`) are the
  SDK's model-swap path; `PartedModelMgr::GetInstance` is `0x112990C` and can
  enumerate assemblies/presets. This is the route for a full (headful) model
  preset, but the player-specific `mobface` preset is intentionally headless.
* `SH_ObjectManager` in GF2ASI can spawn cars/NPCs/items from a
  `SimManager` attribute-packet GUID (`RequestSpawnCar`, `Spawn`), with car
  class `0x10E5319E`. That is the better long-term way to create the peer's
  car on the client instead of moving a parked one.
* `WhiteboxCar` stores `m_HavokWheeledVehicle` at `+0x360`, `m_UsePhysics` at
  `+0x390`, seat SimNPCs at `+0xA4C`, part preset/assembly at
  `+0xE5C/+0xE6C`, `PresetInstance` at `+0xE7C`.
* PlayerAnimSwap hooks `AnimateStateMachine::PlayAnim` and
  `Animated::GetStateID`; it confirms animation state names are per-character
  tables. All Cars supplies vehicle model/seat-locator names in an `.sgp`;
  Different Member Characters supplies `.vpat`/`.str` assets; Modern Fixes is
  display/loader only. None of the four changes the sync design.

**v72:** added `G2CT` game-clock packets (host authoritative, 1 Hz; client
applies when the clock drifts by >=5 s) plus a `time` command. Vehicle list
cleanup: `veh_note` requires a live local-player position, `vehlist` prunes
cars >200 m away, and `peer_vehicle_apply` now requires the peer's exact class
before selecting a car. The `mobface` command is the pre-existing worker path;
our duplicate was removed. Version `v72 loaded`, 227328 bytes.

**v73:** actor scan chunk raised from 4 KB/300 ms to 64 KB/50 ms, so the
80 MB object range is covered in about a minute instead of hours. With
`actors 1` in both `coop_net.cfg`, actor sync is live: A `act=19`,
`amatch/aapplied` 5320, `peeract=8`; B `act=18`, 3589, `peeract=8`, both
`setfail=0`, `posfail=0`, no crash. A 3-minute soak kept both alive and the
counters increasing; the clock also stayed synced (`tapplied` 111). Version
`v73 loaded`.

**Live state:** A PID 12028, player `0x14D37B20`, host 47779; B PID 16636,
player `0x14E37B20`, client 47778; both v73, start room, `actors 1`.
`time` around 06:56; actor sync counters above. The B puppet is currently a
headless mobface body after the appearance experiment; a restart restores the
normal NPC body.

## 2026-10-23, thirty-third session: traffic packets and model-head findings

**v74 traffic:** added `G2CV` packets at 4 Hz carrying up to eight nearby vehicles
(class vtable, position, yaw) when the peer is on foot. The receiver matches a
local vehicle of the same class within 12 m of the sent position and eases it
at 10 Hz (`peer_vehicles_apply`, `pvapplied`). `traffic` is a `coop_net.cfg`
flag, default 0. With a static black limo registered on both sides the path is
live: A `veh=1`, B `pveh=1`, `pvapplied` 32 and rising, `setfail=0`. Moving AI
traffic has not been captured yet; the limo is parked, so the sent position is
constant. The car's `+0x360` is not a Havok pointer in this build (reads 0x2C),
so v75's traffic discovery uses the vehicle vtable slot 8 destructor
(`0x0046C720`) plus a non-zero `+0x360` and the existing `+0x78` position
validation.

**v75 traffic discovery:** the actor scan now recognises vehicle classes while
`traffic 1`, not only while the peer is already in a car. This is what made the
nearby black limo appear in `veh`/`pveh`. The scan still needs to reach the
object range; `vehadd <addr>` is the immediate way to seed a known car.

**Exact appearance:** `mobface` (BuildModelFromSavedData) changes the puppet's
body/apparel to the player's (`preset='mobface'`, theme
`unq_tommy_cipolla`, assembly `player`) but the result is **headless**. The
head/face lives in the Entity's `MetaModelInstance` at `+0x130`, not in the
parted body. Temporarily pointing the puppet's `+0x130` at the player's
instance produced a headful duplicate near the player, but the instance is
owner-bound (`+0x8` points at the player entity) and is not a safe clone; both
pointers were restored and both clients stayed alive. A real fix needs a
MetaModel clone or a spawned player-model NPC, not pointer sharing.

**Live state:** A PID 12168, player `0x14F97B20`, host 47779; B PID 17856,
player `0x14E97B20`, client 47778; both v75, at the Corleone car spot;
`actors 1`, `traffic 1`; time ~06:04; B puppet still headless from the mobface
experiment. The actor/time/traffic counters are increasing; a clean restart
will restore the normal NPC bodies.

**Night/day check:** with A at the car spot, `time 22 0 0` set the clock and
B followed to 22:03 (`tapplied=551`); both screenshots show the dark night
lighting and street lamps (`local/dual_v75_night.png`). Setting the clock back
to daytime also propagated. This is the first visual confirmation that the
`G2CT` sync drives the lighting, not just the numbers.

**Version fix:** the traffic build had accidentally kept the v74 version
string. The final clean build is **v76**, `231424` bytes,
`coopcore.asi v76 loaded`, containing all v72-v75 changes. A fresh boot of
both clients was done for the handoff.

**Final clean state:** A PID 17444, player `0x14F97B20`, host 47779; B PID
8680, player `0x14D584A0`, client 47778; both v76 at the mansion start;
`actors 1`, `traffic 1`; `act=17`, `amatch/aapplied` 1500/1676,
`peeract=8`; `time` ~04:49 with `tapplied=19`; `setfail=0`, `posfail=0`.
Traffic `pveh=0` at this spot because no vehicle is within 40 m of either
player. The puppets are normal NPC bodies (no mobface/meta experiment on this
fresh session).

**Still open:** moving AI-traffic verification, gunshot FX/sound (fire
animation already maps), doors/gates, and a safe headful player model
(meta-model clone or spawned player-model NPC). The traffic path itself is
live; only the moving-car case has not been captured.

## 2026-10-23, thirty-fourth session: traffic, doors, gunshot audio, player clone

**Moving traffic:** v77 taught the actor scan to recognise every vehicle class by
the slot-8 destructor (`0x0046C720` / `0x0046F4F0`) and cache the vtable; v79
added `traffic` diagnostics. At the mansion both clients discovered 30-32
vehicles. A parked non-player car (`0x05C155E8`, class `0x00D60DE4`) was nudged
through its physics: its sent positions changed (`175.9 -> 184.2 -> 177.6`),
B's matched car (`0x05F8DD18`) followed every step, and `pveh=8`,
`pvapplied=5000+`. This is the moving-traffic path, not just a static car.

**Gunshot audio:** v81 adds a synthetic `coop_gunshot.wav` under `scripts/` and
an edge detector on the peer's state names. A/B keep the existing fire-state
mapping (punch `MeleeAttack`, fire `FireGunStationaryAction`, strafe/strafe fire,
reload/draw); when a peer fire state appears the receiver plays the shot once
and increments `shots`. Verified with a crafted `G2CP` carrying
`FireGunStationaryAction`: B reported `shots=1` and the sound path is live. Real
projectiles/damage are not spawned remotely yet; the remote body plays the fire
animation and emits the shot sound.

**Doors:** the live door objects are the `Door` base class (`0x00D6A2A8`), state
at `+0x2B8`, current swing `+0x2A4`, max swing `+0x2AC`. v80 scans them,
matches by static world position and sends `G2CD` (position, state, angle).
Opening a door on A made the matched door on B open (`st=0 ang=1.50`,
`dapplied` increasing); v81 adds a 1.2 s per-door remote lock so the applied
state is not echoed back. Five door objects were found at the start interior
and paired one-to-one.

**Headful player model:** `SimManager::SpawnEntity` can instantiate the local
player packet (`player+0x20`, the CAttributeHandler GUID). v82 does this into a
real Player clone. It has vtable `0x00D6B350`, preset `mobface`, assembly
`player`, and a non-null `MetaModelInstance` at `+0x130`, so it has a head and
the full player mesh. It is placed at the peer's interpolated position every
frame (`avapplied`), and the peer's state names are applied to its state tree.
A three-minute soak left both clients alive with `avapplied` steadily rising
(1297 -> 2273 on A, 1198 -> 1875 on B), `setfail=0`, no APPCRASH. The clone uses
the local player's saved appearance, so the peer's face is not transferred yet;
this is the safe headful model path rather than MetaModel pointer sharing (which
was owner-bound and unsafe).

**Current state:** A PID 23508, player `0x14E37B20`, host; B PID 360, player
`0x14E384A0`, client; both v82, start interior, `actors 1`, `traffic 1`,
`doors 1`, `avatar 1`; `shots` tested at 1; doors/traffic/avatar counters live.

## 2026-10-23, thirty-fifth session: FPS investigation and traffic throttling

**Symptom:** with all layers enabled the game fell to 3-8 FPS (A host worse than
B). Turning all `coop_net.cfg` layers off restored ~54-79 FPS, so the drop was
ours, not the two-client setup.

**Measurements (A host, focused window, `fps` command):**
- all layers off: ~54-62 FPS.
- `actors 1` only: ~32 FPS.
- `traffic 1` + `actors 1`: ~3 FPS while `pvapplied` stayed tiny; the cost was
  not the position writes, it was continuously sending/applying a stationary
  traffic list (and the v77-v86 vehicle-vtable discovery branch).
- avatar only: ~102 FPS (the clone itself is not the problem when updates are
  throttled); doors only: ~54 FPS.

**v83-v90 fixes:**
- v83: adaptive actor scan (6 ms/80 ms only while lists are sparse, otherwise
  2 ms/5 s maintenance), vehicle discovery stops as soon as ~16 classes are
  known, avatar position updates at 20 Hz and only when the target moved
  >8 cm, state names only on change.
- v84: skip traffic writes when the matched car is already within 15 cm of the
  sent position.
- v87: traffic wire list capped to 2 nearest cars, send 1 Hz / apply 4 Hz.
- v90 (current): only **moving** cars are sent, one per packet; if no car moved
  there is no packet and the receiver goes idle after 1 s. A manually nudged car
  (`0x05688348`) made A send `sent 177.00...` and B's matched `0x051D2868`
  follow to `176.43...`; `pveh=1`, `pvapplied` small, traffic still synced.
- `fps` console command added: run it twice, separated by a few seconds.

**Result:** at the mansion car spot with `actors 0`, `traffic 1`, `doors 1`,
`avatar 1`: A ~58 FPS, B ~54 FPS (was 3-8). With `actors 1` the crowded car
spot drops to ~33 FPS; NPC actor sync is the remaining expensive layer and is
therefore **off in the current performance config**. At the start interior all
layers on gave ~68/56 FPS.

**Live state:** A PID 15132, player `0x14F37B20`, host; B PID 25320, player
`0x14E67B20`, client; both v90, car spot; `actors 0`, `traffic 1`, `doors 1`,
`avatar 1`, `trafficsend 1`, `trafficapply 1`; no crash during the FPS soak.

## 2026-10-23, thirty-sixth session: camera/avatar diagnosis, player-car packet

**User report:** remote model invisible; camera detaches/zooms to the peer while
walking and returns on stop; driving a car does not move it for the other
client.  Also proposed a host/join menu with an empty client world that streams
the host world (traffic, NPCs, missions, weapons, reactions).

**Camera root cause:** the `avatar` layer spawns a second real `Player` via
SimManager.  `PlayerManager` then contains `arr[0] = local`, `arr[1] = clone`
(observed live on both clients).  The engine treats the clone as a player and
the camera switches/follows it; because the clone is at the peer position the
camera ends up inside it, which is why there is no visible remote model there
and why the camera lurches/zooms while walking.  The avatar path is also not
stable: after spawn/manager experiments a game died at `0x006D21AA`
(`vehx: av ...`), and another at `0x004829FE` after an external vehicle-pointer
test.  `avatar` is therefore **off** in the working configuration; it remains
experimental and must not be used for normal co-op.

**Remote body now:** with `avatar 0`, the auto-target attaches a live NPC as the
peer puppet (`target=...`), so a body is present, but it is an NPC appearance,
not the remote player's model.  Actor table sync (`actors 1`) costs a lot
(28-33 FPS in crowded spots); `actors 0` still leaves the single auto-target
puppet attached and gave A 41 / B 42 FPS at the start with traffic+doors on.
Exact player appearance transfer is still TODO.

**Car sync:** the old path only moved a class-matched car through
`peer_vehicle_apply` while the peer was inside a vehicle, and the traffic
optimisation skipped sending a stationary/own car.  v91 adds a dedicated
`G2CF` packet (20 Hz, 32 B): class vtable, vehicle world row (+0x78), yaw.
`peer_car_apply` matches a car of the same class near the sent point, excludes
the local own vehicle, and eases position at 20 Hz; the old class fallback is
suppressed while G2CF is fresh.  `netdiag` now reports `pcar/pcapplied`.
Position is synced; yaw is carried but not yet applied to the vehicle matrix.
External tests that set `player+0x1E40` by hand killed the game, so the path
still needs a **real enter-car** two-client test.

**Menu idea:** architecturally sound: host owns simulation, client loads an
empty/streamed world and receives authoritative state; this avoids driving two
independent worlds and would remove most puppet/avatar hacks.  Native menu
buttons need UI hook work; a low-risk interim is console `net host` / `net join`
plus an overlay.  Not implemented this session.

**Live state:** A PID 17728, B PID 18096, both v91; `actors 0`, `traffic 1`,
`doors 1`, `avatar 0`; start interior; targets attached; camera stable; no crash
during the FPS check.

## 2026-10-23, thirty-seventh session: host/join foundation

**Implemented (v92-v94):**
- New console commands:
  - `net host [port]` — bind the host UDP port and persist `mode host`;
  - `net join <ip> [port]` — bind an ephemeral client port, send `G2CH`
    handshake to the host, learn the host endpoint from replies;
  - `net stop` — disable and persist.
- `G2CH` handshake (version + role) is sent at 1 Hz until the first valid
  packet. The receiver learns the sender's address/port from `recvfrom`, so the
  client no longer needs a fixed local port and works behind NAT for outbound
  connections.
- All G2CP/G2CT/G2CA/G2CV/G2CD/G2CF sends now go through `net_send_to_peer()`
  and use the learned endpoint.
- Host/join commands write `coop_net.cfg` (`mode`, `port`, `ephemeral`, `peer`)
  and lock the cfg reload for the session, so a restart reconnects automatically.
- `net` output now shows `conn`, `peer=ip:port`, `self=localport`, `hs`.

**Verified live:** A `net host 47779` -> binds 47779; B `net join 127.0.0.1
47779` -> binds ephemeral 60049; both report `conn=1`, A sees peer
`127.0.0.1:60049`, B sees `127.0.0.1:47779`. Position packets cross (`tr 2 0 0`
on A moved B's reported peer position to 187.70). After killing both and
restarting from the saved cfg, they reconnect automatically without commands.

**Not done:** native menu buttons / literal empty client world. Native menu
needs UI/Apt frontend work; the empty-world architecture is a larger project
(entity IDs, spawn/despawn, mission scripting). Current host/join is the
foundation and still uses the same world on both sides.

**Live state:** A PID 8844, B PID 3304, both v94; auto host/join, client
ephemeral; `actors 0`, `traffic 1`, `doors 1`, `avatar 0`; start interior;
FPS A 48.5 / B 51.0; targets attached; camera stable.

## 2026-10-23, thirty-eighth session: vehicle physics findings

**User report:** the driver moves "through air", the second client's car stays
parked, and sometimes a ghost car jerks forward/back with no collision.

**Diagnosis:**
- The car transform at `+0x78/+0xB8/+0xD8` is a matrix copy. Writing only those
  rows is overwritten by the vehicle physics update on the next frame; this is
  why `pcapplied` grew while the car did not move (and why a transient ghost
  frame could appear).
- A second problem is identity: class+position matching can select a different
  car of the same model. On one live pair the client had no car at the sent
  point at all; the "ghost" was another car of that class being moved.
- Cars carry a readable identity/plate string around `+0xA4C` (`PERRY638` in
  one test) and another around `+0xEC8`; that is a better key than class+distance.
- The vehicle object also has transform-bearing components at `+0xCC`, `+0xE8`,
  `+0x2A0`, `+0x2E8`. A manual test that shifted those components together with
  the matrix rows made `+0x78` stay at the new position for one limo class, but
  not for every class (a `0xD626C4` car still snapped back).

**Code changes v95/v96:**
- `vehicle_setpos` now shifts the linked component positions by the same delta
  as the matrix rows.
- `G2CF` v2 carries a 16-byte car identity from `+0xA4C`/`+0xEC8`.
- `peer_car_apply` prefers the exact identity, then class+position in the nearby
  list, then a bounded heap scan for the class near the sent point.
- This is still not a complete fix: the client must have the same car loaded, or
  the car must be spawned through ObjectManager/SimManager. A car that is
  absent cannot be moved by writing addresses.

**Model:** user suggested overlaying the partner's model on the joined player.
This is feasible as an appearance transfer (send `preset/theme/swapset/assembly`
and apply to the puppet via `setparts`/rebuild), but it is not the same as a
real Player clone; the latter is still disabled because it hijacks the camera.

**Live state:** A PID 8896, B PID 16832, both v96, auto host/join, start
interior, `actors 0`, `traffic 1`, `doors 1`, `avatar 0`; targets attached;
no crash during this check.

## 2026-10-23, thirty-ninth session: car-sync FPS fix

**Symptom:** the second client received the car movement but dropped to ~1 FPS
while a car was being synced.

**Cause:** v95/v96 called `vehicle_find_id()` / `vehicle_find_class_near()` from
`peer_car_apply()` on the **game thread**. Those functions scan the
`0x03000000-0x08000000` heaps for up to 1 s. With the 20 Hz car apply path this
blocked the render thread almost completely. The scan was added to stop the
ghost car (class+distance matched the wrong vehicle), but it must never run on
the game thread.

**Fix v97:** the game thread now only uses the cached `g_peerCarMatch` (and the
nearby `g_vehAddr` list). If there is no match it sets `g_peerCarNeedMatch`; the
actor worker performs the bounded ID/class scan and publishes the result with
`InterlockedExchange`. `netdiag` now reports `pcmatch` and `pcneed`.

**Live check:** with a crafted G2CF for a local car class, B's FPS was ~34 while
the match was still unresolved (`pcneed=1`, `pcapplied=0`); no more 1 FPS
freeze. With a cached match the per-frame path is only component writes.

**Still open:** a car that is absent on the receiver cannot be moved; spawning
it through ObjectManager/SimManager remains the next step. Appearance transfer
for the remote puppet is also still TODO.

## 2026-10-23, fortieth session: car stopped moving after v97

**Symptom:** after moving the car scan to the worker (v97), the car no longer
moved at all; `pcapplied=0`, `pcmatch=0`, `veh=0`.

**Cause:** two independent list bugs:
- `G2CP` cleared `g_vehCount` whenever the peer's vehicle class changed. That
  emptied the nearby list, and `g_vehDiscover` had already been switched off
  after the first 16 cars. The worker then had nothing to search.
- The worker ran the ID scan first; the ID scan and class scan shared one
  throttle timestamp, so after a failed ID scan the class scan was skipped for
  another second.

**Fix v98:**
- Never clear the nearby list on peer class change; just re-arm
  `g_vehDiscover`.
- Re-arm discovery whenever the list has fewer than 8 vehicles and traffic or
  a peer car is active.
- Separate `g_peerCarClassAt` from the ID-scan throttle.
- Nearby car match radius 12 m -> 30 m.

**Verified with continuous crafted G2CF** for a local `0xD60DE4` car:
`pcmatch=0x05BEE5B8`, `pcapplied=10`, the car moved from
`169.09 11.42 -206.83` to `168.01 11.40 -208.97` and stayed there; B FPS ~31
(no 1 FPS freeze). Real enter-car E test still did not trigger (no prompt at
that spot), but the network/apply path is proven with real vehicle writes.

**Live:** A 20584, B 3276, both v98, host/join auto, car spot.

## 2026-10-23, forty-first session: duplicate car identity and real drive test

**Symptom:** the second client still saw the car standing while the host drove.

**Cause:** both limousines in the same area share the same plate/id
(`PERRY638`). `peer_car_apply` kept the first cached same-class car, which was
a *different* instance about 11 m away, and only moved that one. The real
matching car was ~3 m from the sent transform but was ignored because the
cached car passed the 25 m validity check.

**Fix v100:** `peer_car_apply` always scans the nearby list and chooses the
**nearest same-class car to the sent transform**; the cached match is only a
fallback when the list has no candidate. Plates are not unique, so they cannot
be used as the primary identity.

**Real two-client drive test (both v100):**
- A was in the black limo at `162.80 10.20 -219.08`.
- B's matched car `0x0642A568` was at `162.82 10.20 -219.07` (correct instance).
- A drove forward; A car ended at `140.73 10.19 -219.92`, B car at
  `143.82 10.19 -219.80` (following with easing), `pcapplied=34`,
  `pcmatch=0x0642A568`, no 1 FPS freeze.
- B FPS after the test ~37.7 at the start area.
- Evidence: `local/dual_v100_car_drive.png`.

**Live:** A 13396, B 10432, both v100, host/join auto, start.

## 2026-10-23, forty-second session: car orientation and the ghost collision

**User report:** the car moves, but the passenger cannot enter; the car looks
like it only moves in one direction; after driving away from the spawn the car
has a model but no collision and no enter prompt.

**Diagnosis:**
- `G2CF` read yaw from `veh+0xB0`, which is the matrix position copy, not the
  forward row. The car's world matrix is at `+0x78`: row0 position, row1 right
  (`+0x88`), row2 up (`+0x98`), row3 forward (`+0xA8`). v105 reads yaw from
  `+0xA8` and writes the three rotation rows on the receiver.
- The ghost is not a matching bug any more. `vehicle_setpos` changes the matrix
  and several transform copies, but the Havok/collision body and the enter
  prompt stay at the original spawn. The visual car moves; the collision does
  not. Updating all copies of the old position inside the car/base did not move
  the prompt either.
- Tried engine setters: vehicle vtable slot 12 crashed on a `0xD626C4` car;
  base slot2 (`0x0046F4F0`) returned without changing the matrix. These paths
  are unsafe and are not used.
- A real car must be spawned through ObjectManager/SimManager (or moved through
  Havok) to have collision and seats at the new position. That is the missing
  piece for passenger entry.

**v105:** yaw is sent from `+0xA8` and applied to `+0x88/+0x98/+0xA8`.
**Live:** A 23896, B 22460, both v105, host/join auto, start; no crashes.

**Not fixed:** passenger entry and collision/enter prompt. The current car sync
is a visual proxy; do not treat it as a physical car.

## 2026-10-23, forty-third session: yaw, appearance attempt, Havok copy scan

**Car yaw (v105):** `G2CF` had been sending yaw read from `veh+0xB0`, which is
the matrix position copy.  The world matrix is at `+0x78`: row0 position,
row1 right `+0x88`, row2 up `+0x98`, row3 forward `+0xA8`.  v105 reads
`atan2(forward.x, forward.z)` and writes the three rotation rows on apply.
This fixes the car facing the wrong way.

**Appearance transfer (v106-v109):** added `G2CS` with the four part strings
(preset/theme/swapset/assembly), applied to the puppet with a rebuild.  Results
were bad:
- copying `assembly='player'` onto a borrowed NPC makes it **headless**;
- copying the other part strings without the assembly produced a **rainbow /
  incompatible texture** body.
The feature is therefore **off by default** (`parts 0`); `parts 1` can enable it
for experiments.  Exact remote appearance needs a real player-model body, not
an NPC rebuild.

**Havok/ghost (v110):** extended `vehicle_setpos` to scan the base object and
the Havok wheeled vehicle pointer (`base+0x360`) for exact copies of the old
position.  The matrix/base copies move, but the Havok collision transform and
the enter prompt do not; a car that has been moved still has no collision/seats
at the new position.  `vsetb` (calling base slot2 `0x0046F4F0`) was removed in
v111 because it killed a `0xD626C4` car.

**Current:** A 10076, B 17588, both v111, host/join connected, start; `parts 0`.
The car remains a visual proxy; passenger entry is still blocked on a real
vehicle spawn/physics path.

**Final live state (this run):** A 10076, B 17588, both v111, host/join
connected, start; `parts 0`, car visual proxy only.

## 2026-10-23, forty-fourth session: 1 FPS while car moves

**Symptom:** the client dropped to ~1 FPS while the car moved, stayed frozen
for 10-15 s after it stopped, and the car never turned.

**Cause:** v110 added a scan of the car object, its vehicle base and the Havok
object (`0x1000` + `0x1000` + `0x4000` bytes) inside `vehicle_setpos`, which
runs at 20 Hz on the game thread.  That is ~100k float checks per second on the
render thread.  The 10-15 s recovery was the Havok scan finishing.

**Fix v112:** removed all per-frame scans.  `vehicle_setpos` writes only the
known transform copies at fixed offsets, guarded by the old position:
`+0x78/+0xB8/+0xD8/+0x798`, base `+0xC0/+0x100/+0x120/+0x7E0/+0xE08`, plus the
four linked components.  Crafted continuous `G2CF` test now gives **60.8 FPS**
with `pcapplied=65` and no freeze after the packets stop.

**Yaw:** v113 falls back to the player yaw if the car-forward yaw is zero and
logs `pyaw`; visual rotation is still not confirmed.  The car remains a visual
proxy: collision/enter prompt is not moved.

**Live:** A 22680, B 17424, both v113, host/join connected, start, `parts 0`.

## 2026-10-23, forty-fifth session: car FPS and yaw fixed

**FPS:** v110's car/base/Havok scans were the 1 FPS + 10-15 s freeze.  v112
removed them; a crafted moving-car test gave 60.8 FPS.  The yaw helper initially
wrote every 10 ms unconditionally, which cost frames; v120 writes only when the
heading differs and sleeps 40 ms.

**Yaw:** two bugs:
- `peer_car_apply` returned on the 5 cm position threshold before touching
  rotation, so a car already near the sent position never turned;
- vehicle physics rewrites the heading during the frame, so a 20 Hz write was
  lost.  The base has two more rotation bases at `+0x90/+0xA0/+0xB0` and
  `+0xD0/+0xE0/+0xF0`; v115 writes those too, and v119/v120 pin the matched
  car's heading from a small worker.

**Live test:** B drove the black limo and turned; A's matched car ended with
forward `(-0.107, 0, -0.994)`, yaw `-3.035`, exactly B's `(-0.106, 0, -0.994)`;
A FPS while syncing was 51.7, B 86.6 at rest, no freeze after stop.
Evidence: `local/dual_v120_yaw_fps.png`.

**Still open:** the remote car remains a visual proxy (no collision/enter
prompt, no passenger), because Havok collision is not moved.  Current: A 2192,
B 21452, both v120, host/join connected, start.
