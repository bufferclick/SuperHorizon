# Super Horizon

Super Horizon is the [Horizon](https://github.com/horizon-rust/horizon) launcher
with one change: its name. It is **Super Horizon**, **made by Buffer**, and
everything else — every screen, button and behavior — is exactly Horizon's.

Horizon is MIT-licensed open source, which allows this: the original
[LICENSE](LICENSE) and [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) stay in
place, and the credit for the software itself belongs to Horizon's contributors.
"Made by Buffer" is this fork's own line, on top of that work.

Super Horizon is **Windows only** (x64). To play you need a Microsoft account
that owns Minecraft: Java Edition, exactly as with Horizon.

## What changed from Horizon

- The window title, the title bar and the installer window say **Super Horizon**,
  with **made by Buffer** beside the name.
- The binary is `super-horizon.exe`; the installer is `super-horizon-setup.exe`.
- It installs into `%LOCALAPPDATA%\Programs\Super Horizon`, with its own Start
  menu shortcut ("Super Horizon") and its own Add/Remove Programs entry
  (Publisher: Buffer). An installed Horizon and an installed Super Horizon do
  not touch each other, and their data folders are separate
  (`%APPDATA%\super-horizon`).
- The command line is `super-horizon ...` instead of `horizon-launcher ...`.
- The in-app launcher self-update is off: Horizon's releases would put the
  original launcher back and undo the rebrand. Updates come from pushing to this
  repository and rebuilding; the game itself still installs and updates from
  Horizon's releases, exactly as before.
- This repository builds with its own workflow (below) instead of Horizon's.

Everything the launcher plays, downloads or shows about the **game** still says
Horizon, because the game is Horizon's, fetched from Horizon's releases.

## Build the exe with GitHub

The workflow at `.github/workflows/super-horizon.yml` builds the launcher every
time you push. No GitHub settings need changing: the workflow declares the
permissions it needs itself.

```bash
cd super-horizon
git init
git add .
git commit -m "Super Horizon"
git branch -M main
git remote add origin https://github.com/<you>/super-horizon.git
git push -u origin main
```

Then, on GitHub: **Actions → super-horizon → the run → Artifacts →
super-horizon-windows-x64**. The artifact holds:

- `super-horizon.exe` — runs the launcher as it is.
- `super-horizon-setup.exe` — the same binary under the setup's name: double-click
  it for the per-user installer (no administrator), Start menu shortcut and
  Add/Remove Programs entry.

The first build takes roughly 15–25 minutes; later pushes reuse the cache and
finish in a few minutes.

### Publish a release (for sharing without signing in)

Artifacts can only be downloaded signed in to GitHub. To get public download
links, push a tag:

```bash
git tag v1
git push origin v1
```

The same run also publishes `Super Horizon v1` under **Releases**, with both exe
files attached and a note: *made by Buffer*.

## Optional settings (repository variables and secrets)

Without these the launcher works, with two limits:

- **Microsoft sign-in**: Settings → Secrets and variables → Actions → Variables →
  New repository variable, name `HORIZON_MSA_CLIENT_ID`, value an Azure
  application (client) ID for a public desktop app with the Xbox/Minecraft
  scopes — [docs/microsoft-account.md](docs/microsoft-account.md) explains the
  registration. Unset, the launcher says sign-in is not configured; offline
  accounts and imported instances still play.
- **CurseForge in the mod browser**: Settings → Secrets and variables → Actions →
  Secrets → New repository secret, name `HORIZON_CURSEFORGE_API_KEY`. Unset, the
  browser searches Modrinth only.

## Building on your own Windows PC

If you have Rust installed:

```bash
cd launcher
RUSTFLAGS=-C target-feature=+crt-static cargo build --release --locked
```

The exe is at `launcher/target/release/super-horizon.exe`. The static C runtime
flag keeps it from asking for VCRUNTIME140.dll.

## Uninstall

`super-horizon.exe uninstall` (or Add/Remove Programs → Super Horizon) removes
the launcher; your worlds and the game's folder stay.
