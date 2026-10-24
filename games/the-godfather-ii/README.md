# The Godfather II (2009)

| | |
|---|---|
| Engine | Visceral Engine (per PCGamingWiki; not confirmed from the binary) |
| Versions tested | `1.0.789.0` as shipped inside the SE v0.56 Nexus mod bundle - an **unverified third-party executable**, not a build to follow: run only in a disposable copy. Boots to the campaign there, menu and gameplay confirmed. Clean retail `1.0.764.0` inspected statically only |
| Projects | Multiplayer Revival (match multiplayer, not campaign) <https://www.nexusmods.com/thegodfather2/mods/95>; The Godfather II SE script extender (modding hook, not co-op) <https://www.nexusmods.com/thegodfather2/mods/40>; VisceralToolkit (asset/stream tooling, WIP) <https://github.com/Greavesy1899/VisceralToolkit> |
| QA level | automated for the sync layer (scripted two-client checks of position, animation state and stability; no campaign playthrough, no public release) |
| Last updated | 2026-10-24 |

## Where things stand

There is still **no public campaign co-op project** for this game. What exists
now is a working two-client prototype: an own-code ASI plugin that makes the
second player's character appear in the first player's world, on two copies of
the game running on one PC over UDP loopback.

Measured on the prototype (both clients are real game clients with the save
loaded, positions read from inside the game):

| what | result |
|---|---|
| puppet position vs. peer position | **0.00 m** (exact, measured repeatedly); `conv` 15-31 ms in v136 |
| heading | follows the peer's yaw |
| animation state | the peer's CCT state id is transferred and re-applied; state names map to the borrowed body (`Ped` walk, `MeleeAttack`, fire/strafe) |
| appearance | the body is rebuilt once to the headful `unq_tommy_cipolla` model (`model 1`) |
| automatic puppet pick | a real crew recruit/associate near the player, with dead or streamed-out bodies replaced |
| world clock | day/night time propagated (`G2CT`) |
| nearby traffic | moving cars mirrored as proxies (`G2CV`) |
| doors | open state and angle mirrored (`G2CD`) |
| peer's car | position and yaw match exactly; visual proxy without collision (`G2CF`) |
| vehicle flag and vehicle class | both arrive at the other client |
| host/join | F9 host, F10 join loopback, role persisted in the config |
| round trip | 15-31 ms |
| frame rate | playable; ~40-60 fps in the focused window with traffic and doors on, actors off (earlier measured runs, no plugin-induced stutter) |
| crashes | none caused by the plugin since the instrumentation was added; v136 ran a 6-minute idle soak and a 4-minute movement soak (319 scancode moves) clean: 70k/72k applies, `setfail=0`, `posfail=0`, `snaps=0/1`, both clients alive |

Research log with every address, measurement, crash cause and dead end:
[NOTES.md](NOTES.md). The plugin source is not public yet.


## Existing work

- **Godfather II Multiplayer Revival** (<https://www.nexusmods.com/thegodfather2/mods/95>,
  by `paindubreak`, last upload 2026-08-06): restores the original online match
  modes through the community FESL/matchmaking server; play is peer-to-peer once
  a match starts. Requires build `1.0.789.0` plus an ASI loader. This is the
  game's original competitive multiplayer, not a shared story campaign, and it
  was not installed or launched here. The original EA service is shut down
  (PCGamingWiki); the revival is community reports only.
- **The Godfather II SE** (<https://www.nexusmods.com/thegodfather2/mods/40>,
  by `Greavesy`/`Greavesy899`, v0.56 last updated 2026-08-26): script
  extender/mod menu with loose-file loading, NPC/item/vehicle spawning,
  checkpoint loading, and SimGroup overrides. It ships an **unverified
  third-party game executable** of unknown build and no public source or
  license - do not treat it as a target build and do not redistribute it. Its
  bug list includes API/startup crash reports. **Its loader is the hook used
  here**: the `dinput8.dll` in the bundle is the Ultimate ASI Loader and it
  loads third-party `scripts\*.asi` plugins, so a co-op mod can ship as an ASI.
- **VisceralToolkit** (<https://github.com/Greavesy1899/VisceralToolkit>, no
  license published): inspection/editing tools for Visceral engine games. The
  wiki names Godfather II Version 12 `.str` stream files and lists resource
  types (navigation graphs, MetaModels, `VariableDictionary`), with most formats
  marked unknown. Useful as an asset reader lead, not a co-op framework.
- **Dead Space 3 co-op** (<https://www.pcgamingwiki.com/wiki/Dead_Space_3>) is
  the same named engine and shipped a two-player campaign co-op. That is an
  engine-family reference for what the engine can do; no code or tools were
  found or reused from it.

## How the game works (for co-op)

- **Assets:** three `GODFATH*.viv` container archives (magic `BIGF`) plus about
  2,300 loose version-12 `.str` stream files (magic `3slo`) covering mission
  and objective paths; no Lua/script files shipped.
- **Engine objects:** the engine keeps the player's world matrix in a global
  (`0x0112E210` in the `1.0.789.0` build) and the camera look-at/eye transforms
  in `0x0112A840` / `0x0112A850`. Characters are objects with a class vtable
  (`0x00D6B350`); their position is read through a virtual getter
  (`vtable+0x48` -> `this+0x3C0`). The engine rewrites every character's
  transform from its own kinematics each frame, so a puppet can only be steered
  in-process and after that update.
- **Scripting:** the engine exposes a script/behavior layer to the game data
  (`GetPlayerPosition`, `SetPositionValues`, `CreatePosition`, `GetPlatform`,
  movement commands such as `moveToPos`) - a sanctioned way to move a character
  that does not exist yet in any public mod.
- **DRM:** the clean retail exe is SecuROM-protected (`.securom` sections and
  `paul.dll`; PCGamingWiki lists 7.38.0015). Ghidra static decompilation of the
  SecuROM-protected exe fails (invalid flow/overlap errors); do not unpack it.
  The unverified third-party `1.0.789.0` exe from the SE bundle decompiles
  cleanly, which is why the memory addresses in the notes come from it; it is a
  disposable test target only, not a build to follow.

## What we changed

Nothing in any installation, and nothing published. Inside **disposable copies**
only:

- three byte patches in the copy's `godfather2.exe` (each recorded in the private
  identity notes): the under-spec capability gate, the `Window.Fullscreen`
  default, and the GPU-vendor detector that dereferences a NULL `strstr` result;
- our own ASI plugin (`scripts\coopcore.asi`, own code) with five engine hooks
  and these subsystems:
  - **networking**: UDP loopback, 20 Hz position packets, one packet carrying
    position, heading, the animation state id, the two secondary action state
    ids, the "in a vehicle" flag, the peer's vehicle class and the peer's state
    machine names; host/join keys F9/F10 and a persisted role; clock, traffic,
    door, and peer-car packets (`G2CT`/`G2CV`/`G2CD`/`G2CF`);
  - **puppet**: automatic pick of a live crew recruit near the player, with
    dead or streamed-out bodies replaced, position and heading driven every
    frame, animation state re-applied when the NPC's own AI overrides it;
  - **appearance**: the borrowed body is rebuilt once to the shipped headful
    `unq_tommy_cipolla` model (`model 1`), so it reads as a player instead of a
    random pedestrian;
  - **world layers**: day/night clock, nearby moving traffic, doors, and the
    peer's own car (position + yaw; a visual proxy without collision); while
    the peer is in a vehicle the borrowed body is dropped under the world
    (`hidecar 1`), so the proxy car is the visible representation;
  - **diagnostics**: a file-driven console (`coop_cmd.txt` -> `coopcore_cmd.txt`)
    with live counters for every apply path, plus crash and access-violation
    handlers;
- own tooling: an external inspector/writer for the running game, a position
  finder, a container dumper, an input/screenshot driver, an exe byte patcher,
  a D3D9 environment probe, an SDBM name hash tool, and a one-click test
  launcher that starts both disposable copies, loads the campaign through the
  menu with injected scancodes, and arranges the two windows side by side.

The machine's pre-existing profile, settings and campaign saves were verified
byte-identical against a pre-work backup, and the original installation's
executable hash is unchanged.

## QA

Two real game clients, both loading the same campaign save in disposable
copies, are driven by scripts that read the state from inside each process.
What the scripts check:

1. both clients are up and the UDP link is up (`net`: sent/recv counters grow,
   peer age stays small);
2. the puppet sits on the peer (`conv` in `netdiag` = ms since the puppet was
   last measured within 2 m of the peer; the position difference was 0.00 m in
   the runs recorded in NOTES.md);
3. the animation state id arrives and is re-applied (`anim`, `animforce` in
   `netdiag`);
4. the peer state names arrive (`state='...'` in the `net` line);
5. the world layers apply (`tapplied`, `pvapplied`, `dapplied`, `pcapplied`);
6. no plugin crash (`crash:` and `vehx:` lines stay empty in the log).

The menu path is scripted now: `boot_client.py` sends scan-code keys and the
profile click, and the one-click test launcher chains start -> boot -> window
arrangement.  The latest runs are a 6-minute idle soak and a 4-minute movement
soak (319 scan-code moves), both clean.  There is still **no** campaign
playthrough in co-op, so the QA level is "automated for the sync layer" only.

## How to play

Nothing to play: there is no co-op release. The internal test procedure is:

1. start both disposable copies (the plugin redirects the profile paths into
   the copy, so the real profile is never written);
2. in each copy: load the campaign profile and resume the save (the test
   launcher scripts the profile click; a manual run needs a real click because
   the game reads the mouse through Raw Input);
3. the plugin connects over UDP loopback and each client picks up a puppet for
   the other one automatically;
4. `coop_cmd.txt` next to the executable accepts `status`, `net`, `netdiag`,
   `sm`, `smnames`, `findsm`, `partnames`, `puppet` and more, and answers in
   `coopcore_cmd.txt`.

## Known issues

- **The peer's car is a visual proxy.** Position and yaw are synced and were
  measured matching exactly while the client drove the mansion limousine, but
  the receiver's matched car has no collision and cannot be entered: its Havok
  body stays at the original spawn. A real car needs an
  ObjectManager/SimManager spawn with a valid stream GUID; the SimManager
  packet hash entries are not compact attribute packets, so that path is still
  open. While the peer drives, the borrowed body is dropped below the world
  (`hidecar 1`) so the proxy car is the visible representation; that hiding
  path has not yet been verified on an enterable car in the current save.
- **The puppet cannot wear the peer's exact face.** It is rebuilt once to the
  shipped `unq_tommy_cipolla` model (`model 1`), a headful player-style body,
  so it reads as a player rather than a random pedestrian. Giving an NPC the
  saved `mobface` appearance is headless, and `BuildModelFromSavedData` on a
  live character crashes.
- **Ambient NPCs are not synchronised.** Pinning the positions of world NPCs
  crashed through a corrupted pointer and made the NPC scheduler spawn
  replacements, so the layer is off by default (`actors 0` in the config).
- **Campaign missions and objectives are local to each client.** Both copies
  load the same save, but mission scripts, objectives, money and progress run
  independently; completing an objective on one client does not advance the
  other. A shared story campaign needs host-authoritative mission state, which
  the campaign scripts do not expose.
- **The NPC's own AI fights the animation state.** It puts the body back into
  its own state every frame, so the peer's state has to be forced back
  repeatedly; the result is right most of the time but not frame perfect.
- Build identity is a moving target: the clean copy reports `1.0.764.0` while
  both the Multiplayer Revival archive and the SE extender bundle their own
  different `godfather2.exe` builds. Any patching must be re-derived per build -
  every address in the notes belongs to the SE `1.0.789.0` build only.
- The game's UI takes the mouse through Raw Input; keyboard navigation works in
  the menus, but the profile screen needs a real click.

## Next steps

1. Decide what to do with the vehicle: create a car in the client world and
   drive it to the peer, or drop the feature and document it as a limit.
2. Give the puppet the peer's exact face and apparel. The current body is the
   shipped headful `unq_tommy_cipolla` model (`model 1`); the saved `mobface`
   preset is headless on an NPC, and `BuildModelFromSavedData` on a live
   character crashes.
3. Sync the world, not just the peer: the ambient layer needs stable entity
   identity (a GUID per NPC) and a crash-free way to move world characters.
4. Turn the manual boot path into an automated one (script the menu with
   injected keys, add a watchdog that asserts the campaign map is reached).
5. Then pick the networking model per `playbook/networking-steam-spacewar.md`
   for players outside the same machine.
6. **Use the engine's own RPC/ghost layer.** The exe contains the EARS
   `NetConnection`, `GhostConnection`, `NetReflectable` and `RPCEV_*` system
   used by the original multiplayer (`ShowGhost`, `HideGhost`,
   `MobfaceData`, door RPCs, authority transfer). The descriptors are
   initialized even in single-player, but no transport is created; starting
   it needs the FESL/peer object that only the multiplayer frontend builds.
   That is the path to replace the hand-written sync with engine ghosts.

