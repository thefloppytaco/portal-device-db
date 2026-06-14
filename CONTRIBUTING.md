# Contributing a device report

The whole point of this repo is breadth — every model, OS build, and launcher version we
can get our hands on. If you own a Portal, mapping it takes ~2 minutes.

## Run the collector

1. **Enable ADB** on the Portal. (On Immortal there's a toggle; on stock it varies.)
2. Connect it to a computer over USB — or wirelessly: `adb tcpip 5555` while plugged in,
   then `adb connect <portal-ip>:5555`. Confirm `adb devices` lists it.
3. From this repo:
   ```sh
   ./collect.sh                 # one device attached
   ./collect.sh <adb-serial>    # if several are attached (see `adb devices`)
   ```
4. It prints a Markdown report and writes it to `reports/<model>-<codename>.md`.

It is **read-only**: no `pm grant`, no installs, no settings changes. It does **not** read
your serial number or any account/personal data — only hardware, OS, and permission facts.

## Submit it

Either:
- **Open an issue** with the [Device report template](../../issues/new?template=device-report.yml)
  and paste the output (easiest — no git needed), **or**
- **Open a PR** adding the generated `reports/<model>-<codename>.md` file, and add a row to
  the matrix table in `README.md`.

## What's most valuable

- **New models** — Portal, Mini, Go, gen-2, TV are all unmapped.
- **Different OS builds / launcher versions** of a model we already have — capabilities and
  the permission census can differ between builds.
- **Corrections** — if something the script reports is wrong or misleading on your unit,
  say so in the notes.

Thank you — this is the groundwork everyone building for these devices gets to stand on.
