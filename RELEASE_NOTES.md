# Moneta Home — Free 0.1.20260921

Moneta Home is a personal money planner that runs on your own computer: your
accounts, budgets, forecasts and reports stay there — no account to create, no
cloud, and it works with no internet connection.

**This release replaces 0.1.20260920.** It fixes a way the app could report
that it had saved a copy of your data when it had not, and it makes a Raspberry
Pi on your network ask for a password instead of a key that changed at every
start. Anyone running the previous packages should install these.

## Which file

| Your machine | File |
|---|---|
| PC or laptop, Intel or AMD, 64-bit | `…_FREE_LINUX_PC.deb` |
| Raspberry Pi, 64-bit Raspberry Pi OS | `…_FREE_RASPBERRY_PI_64.deb` |
| Raspberry Pi, 32-bit Raspberry Pi OS | `…_FREE_RASPBERRY_PI_32.deb` |

Not sure which Raspberry Pi OS you run? `dpkg --print-architecture` prints
`arm64` for 64-bit and `armhf` for 32-bit.

## Install

```bash
sudo apt install ./moneta-home_0.1.20260921-<build>_FREE_<your machine>.deb
```

Installing over an existing version keeps your data. Then open **Moneta Home**
from your applications menu, or run `moneta-home`.

## What changed

* 🔴 **"Start again" could say your export was saved when nothing was saved.**
  Resetting the app writes a copy of everything you have and then deletes it.
  In some browsers the second automatic download on a page is blocked silently —
  the export was the second — so the message could be telling the truth about a
  file that did not exist. **The export now waits for you to press the button**,
  which no browser refuses in silence.
* **A machine on your network now has a password of its own.** Before, a browser
  on another computer needed a key that changed at every start. Now you set a
  password once with `moneta-home --set-password` and sign in as `owner`. The
  app **refuses to answer the network until you have set one** — and there is no
  default password, because a password printed in a README is a door every
  installation shares.
* **A port that stays put:** `MONETA_PORT`, so the address you bookmark keeps
  working after a restart.
* **Your machine, named in plain words.** The files say `LINUX_PC`,
  `RASPBERRY_PI_64` and `RASPBERRY_PI_32` instead of `amd64`, `arm64`, `armhf`.
* The free edition holds **one current account and two savings accounts**, and
  carries a small banner linking to the website. The **MAX** edition is not sold
  for local installs — it is a Cloud edition.
* Each package carries its licence and the licences of the 54 open-source
  components it is built from, in `/usr/share/doc/moneta-home/`.

## What you need

* Ubuntu 24.04 or later, or Debian 12 or later, on a PC
* Raspberry Pi OS 12 (bookworm) or later, 32-bit or 64-bit
* A desktop install — this is a desktop application
* About 300 MB of disk space

## Your data

```
~/.local/share/moneta-home/
```

Removing the package leaves that folder alone. There is no copy anywhere else —
that is the point of this edition — so back it up as you would any other folder.

## Verify your download

```bash
sha256sum -c SHA256SUMS
```

## Notes

* These packages are not signed. Linux does not refuse an unsigned package; the
  checksums are how you confirm you have what we published.
* Serving the app on your network is plain HTTP: the sign-in cookie travels
  unencrypted on that network, so never forward the port on a router. The README
  shows an SSH tunnel for when you would rather expose nothing.
* Found a problem? [Discord](https://discord.gg/QesugQzTp), an
  [issue](../../issues), or <info@moneta-home.com>. Please include the output of
  `moneta-home --diagnose`, which says where your data is and which build you
  have, and nothing from your records.
