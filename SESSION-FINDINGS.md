# Session findings — backup recovery & client re-sourcing (2026-07)

Notes from a Claude Code session that traced the pre-departure backups and the
client situation. Companion to `sphere-server/MIGRATION.md`.

## Where everything lives (recovery map)

| Asset | Location | Status |
|---|---|---|
| KB, specs, wire-laws, `.claude-memory` (22 facts) | GitHub `Barahlush/sphere-server` (July 15–16) | ✅ recovered |
| Internal emu branches (`local-work`, `wp/*`, `feat/*` — 38 refs) | `sphere-server/.emu-bundles/internal-branches.bundle` | ✅ verified |
| IDA `.i64` (client ~97% annotated) | `sphere-server` release `re-artifacts-2026-07` | ✅ recovered |
| July 13–15 emu work (combat, mob-hp, repo-path, gm-spawn, console) | `Barahlush/SphereEmu` fork branches | ✅ on GitHub |
| July 4 snapshot: `sphere-server.bundle`, `memory/`, `Discord.zip` (344 MB, has `db1.rar`), `ksy/`, `MBCdecompiler-v23`, `sphere-mbl-re` | gdrive `_TRANSFER/` (baraltiva@gmail.com) | ✅ intact |

## The lost July 14 gdrive upload

Not present in baraltiva@gmail.com Drive (searched by title, date, size, archives, shared).
It was the migration plan's **"physical copy → external disk / your own cloud"** bucket:
the ~1 GB game client, `D:\SphereDev\`, and (optional) `C:\Temp\claude\ida-re\`.
Most likely destination: a **different Google account or rclone remote**.
- Next step: `rclone config show` on the upload machine to identify the remote.
- Fallback: the home Windows PC still holds the authoritative copy.

## `D:\SphereDev\` — under-inventoried in the migration doc

`MIGRATION.md` called it "5 MB, BitStream sources, recoverable from NuGet." The KB shows it held more:
- `SphereSource\source\` — source tree (at least the `BitStreams` bit-codec: `Bit.cs`,
  `BitStream.cs`, `BitExtensions.cs`, `Int24.cs`, `Int48.cs`; folder name implies broader).
- `_sphereDumps\` — data dumps (`mobs.txt` = full mob HP curve, `castle_chests.txt`),
  flagged in `spheretools-survey.md` as **"in NEITHER repo."**

The compiled `.references/BitStreams.dll` IS in this repo, so the emu still builds/runs.
What is **not** in any repo is the editable library source + the dumps — only on the home PC
and the lost upload. Highest-priority item to relocate.

## Client re-sourcing — still officially downloadable

Official launcher (verified live, code-signed by GameXP):
`https://clotho-install.gamexp.com/launcher/Sphere-12-pr.exe?v=1.2.0`

- 2.5 MB bootstrapper; fetches the ~1 GB current client on run (requires a GameXP/Nikita
  account — registration at `sphere.gamexp.com/?action=accreg`).
- A fresh install restores the 368 v4.0 `.mbc` scripts (RE ground truth).
- Local tweaks not restored by a fresh install: `game_proxy.py`, `connect.cfg`→localhost.
  `sphereclient_patched.exe` is already in this repo.

## Platform: only the client needs Windows

- Emu server (`net9.0` + Godot 4.6 C#), RE tools (Python), IDA — all run on macOS / Linux.
- Client is 32-bit x86 + DirectX 9 → needs Windows or a Wine layer.
  - Free paths on Apple Silicon: **Whisky** (free; discontinued Apr 2025 but fine for an
    old DX9 title) or **Wine via Homebrew** (free, maintained); **UTM** free VM as fallback.
  - Paid CrossOver is unnecessary for a game this old.
  - Linux is the easiest host for the DX9 client under Wine/DXVK.

## Still missing (small, reconstructable)

- `game_proxy.py` / `tcp_proxy.py` (MITM capture rig) and `START_EMU.bat` — not in git;
  rebuildable from the documented rig + `sphere-server/verify/session-0,1` captures.
