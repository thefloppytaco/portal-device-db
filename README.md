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
3. It prints a report and saves it to `reports/`. **Submit it** by opening a
   [Device report issue](../../issues/new?template=device-report.yml) and pasting the
   output, or PR the file into `reports/`. That's it — thank you!

The script **changes nothing** on the device (no grants, no installs) and **does not**
collect your serial number or any personal data — only hardware/OS/permission facts.

## Device matrix

| Model | Codename | Android | SoC · RAM | Launcher | GMS | Bootloader | Cameras | adb-grantable perms | Report |
|---|---|---|---|---|---|---|---|---|---|
| **Portal+ (gen 1)** | `aloha` | 9 / API 28 | msm8998 · 3.6 GB | Immortal 1.34 | none | locked (green) | 2 (720p app-usable; 1080p tracking cam is system-held) | 24 | [📄](reports/Portal-aloha.md) |

_Want your model here? Run `collect.sh` and submit — Mini, Go, gen-2, and TV are all
unmapped so far._

## What the report captures

- **Identity** — model, codename, Android/API, full build fingerprint, security patch,
  hardware revision, ABIs, SoC.
- **Hardware** — RAM, display size + density, storage, whether auto-rotation is supported.
- **Software & security** — launcher (Immortal) version, Aloha launcher version, Google
  Play Services presence, Aurora Store / Shizuku presence, bootloader lock + verified-boot
  state, SELinux mode, root.
- **Sensors** — the actual sensor list (gen-1 Portal+ has only accel / ambient-temp / light:
  no gyro, compass, or proximity).
- **Permission census** — all platform permissions bucketed by what a non-platform-signed
  app can reach: **normal** (auto), **dangerous** (runtime prompt), **development**
  (`adb pm grant` — the elevated set, listed in full), and signature/privileged **walls**.
  This is the single most useful thing for builders: it tells you exactly which superpowers
  a sideloaded app can be granted over ADB.
- **Resource overlays (RROs)** — vendor theming/config overlays (e.g. Meta's `Niu` theme,
  which is why system dialogs render oddly on these devices).
- **Cameras** — how many, and which are held by system services.

## Deep dive (gen-1 Portal+)

The most detailed capability notes for the gen-1 Portal+ (`aloha`) — camera limits, the
audio subsystem, the screensaver-takeover pattern, the rotation-policy quirk, no-GMS
workarounds — live alongside the apps built on it. Start with the
[PortalHub README](https://github.com/thefloppytaco/portal-calendar) and its `docs/`.

## License

[MIT](LICENSE). Reports are contributed device facts — no warranty, your mileage may vary
by build.
