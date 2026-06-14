# Portal Device DB

A community **capability map of Meta Portal devices**. Run one script against your
Portal and it produces a standardized report — hardware, OS build, launcher version,
sensors, the **permission census** (what a sideloaded app can actually obtain vs what's
walled), and the vendor theming overlays. Submit it, and it lands in the matrix below.

**Why:** these devices are discontinued and undocumented, and they vary by model
(Portal / Mini / + / Go / TV), OS build, and launcher version. If you're building
something for a repurposed Portal, you want **real data for the exact unit in front of
you** — not guesses. This is that data, contributed by the people who own them.

> Companion to [PortalHub](https://github.com/thefloppytaco/portal-calendar) and friends —
> apps built on a gen-1 Portal+. This repo is the shared groundwork under all of it.

## Contribute (2 minutes, read-only, no personal data)

1. Enable **ADB** on the Portal and connect it (`adb devices` should list it).
2. Run the collector:
   ```sh
   ./collect.sh            # or  ./collect.sh <adb-serial>  if several devices are attached
   ```
3. It saves the report to `reports/`, **copies it to your clipboard, and opens a
   pre-filled GitHub issue** in your browser — just paste it into the box and click
   **Submit**. (It does *not* auto-post on your behalf — you review and submit it
   yourself. No `gh`/login/token needed.) Prefer a PR? Add the file under `reports/`.

The script **changes nothing** on the device (no grants, no installs) and **does not**
read your serial number or any account/personal data — only the stock hardware, OS, and
permission facts below.

## Device matrix

Stock device facts. (Launcher is what the owner flashed on — not a device trait — shown
only for context, since almost everyone repurposes these with Immortal.)

| Model | Codename | Android | Chip | RAM | GMS | Bootloader | Cameras | adb-grantable perms | Launcher | Report |
|---|---|---|---|---|---|---|---|---|---|---|
| **Portal+ (gen 1)** | `aloha` | 9 / API 28 | Snapdragon 835 | 3.6 GB | none | locked (green) | 2 — 720p app-usable; 1080p tracking cam is system-held | 24 | Immortal 1.34 | [📄](reports/Portal-aloha.md) |

_Want your model here? Run `collect.sh` and submit — Mini, Go, gen-2, and TV are all
unmapped so far._

## What the script reads — exactly

Everything is **read-only** `adb shell` (`getprop` / `dumpsys` / `pm` / `wm`). No writes,
no grants, no installs, no network. Specifically:

| Section | Commands it runs |
|---|---|
| Identity | `getprop ro.product.model/.device/.build.fingerprint/.build.version.*/.boot.revision/.product.cpu.abilist` |
| Chip / RAM | `getprop ro.board.platform` · `grep MemTotal /proc/meminfo` |
| Display / storage | `wm size` · `wm density` · `df /data` · `dumpsys window` (auto-rotation support flag) |
| Stock OS & security | version of the **stock** `com.facebook.alohaapps.launcher`; presence of `com.google.android.gms`; `getprop ro.boot.flash.locked/.verifiedbootstate`; `getenforce`; whether `su` is on PATH |
| Sensors | `dumpsys sensorservice` → the `android.sensor.*` types |
| Permission census | `dumpsys package permissions` → every permission's `prot=` flags, bucketed into normal / dangerous / `development` (adb-grantable) / walls |
| Overlays | `cmd overlay list` (vendor RRO theming/config) |
| Cameras | `dumpsys media.camera` → device count + which system service holds each |
| Repurposing (context only) | version of `com.immortal.launcher` if present |

It does **not** enumerate your installed apps, accounts, files, network, or serial number.
The only owner-installed thing it notes is the custom **launcher** (because it changes how
the device behaves) — and it's clearly labelled as side-loaded, not a device trait.

## Why a permission census?

It's the single most useful thing for builders. A sideloaded app can obtain **normal**
(auto-granted) and **dangerous** (runtime-prompt) permissions like any app — but the
interesting ones are the **`development`-flagged** permissions, which a locked-down device
will still let you `adb pm grant` to a normal app (e.g. `WRITE_SECURE_SETTINGS`, `DUMP`,
`PACKAGE_USAGE_STATS`, `READ_LOGS`). The report **lists every one of those** so you know
exactly which "superpowers" are available on a given build before you design around them.
Everything else is a signature/privileged **wall**.

## Deep dive (gen-1 Portal+)

The most detailed capability notes for the gen-1 Portal+ (`aloha`) — camera limits, the
audio subsystem, the screensaver-takeover pattern, the rotation-policy quirk, no-GMS
workarounds — live alongside the apps built on it. Start with the
[PortalHub README](https://github.com/thefloppytaco/portal-calendar) and its `docs/`.

## License

[MIT](LICENSE). Reports are contributed device facts — no warranty, your mileage may vary
by build.
