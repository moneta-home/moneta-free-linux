# Moneta Home — Free 0.1.20260920

The first Linux release. Moneta Home is a personal money planner that runs on
your own computer: your accounts, budgets, forecasts and reports stay there —
no account to create, no cloud, and it works with no internet connection.

## Which file

| Your machine | File |
|---|---|
| PC or laptop, Intel or AMD, 64-bit | `moneta-home_0.1.20260920-<build>_FREE_LINUX_PC.deb` |
| Raspberry Pi, 64-bit Raspberry Pi OS | `moneta-home_0.1.20260920-<build>_FREE_RASPBERRY_PI_64.deb` |
| Raspberry Pi, 32-bit Raspberry Pi OS | `moneta-home_0.1.20260920-<build>_FREE_RASPBERRY_PI_32.deb` |

Not sure which Raspberry Pi OS you run? `dpkg --print-architecture` prints `arm64` for 64-bit and `armhf` for 32-bit.

## Install

```bash
sudo apt install ./moneta-home_0.1.20260920-<build>_FREE_<your machine>.deb
```

Then open **Moneta Home** from your applications menu, or run `moneta-home`.

## What the free edition does

Current accounts and savings accounts, with transactions, recurring entries,
categories, budgets, goals, the forecast, the dashboard and reports, in your own
currency. The free edition holds **one current account and two savings
accounts**, and is supported by a small in-app banner.

Not in the free edition: credit cards and loans, cash and investment accounts,
the portfolio, bank statement import, spreadsheet templates, scenarios and the
AI help.

## What you need

* Ubuntu 24.04 or later, or Debian 12 or later, on a PC
* Raspberry Pi OS 12 (bookworm) or later, 32-bit or 64-bit
* A desktop install — this is a desktop application
* About 300 MB of disk space

`apt` pulls in the few system libraries the window needs (GTK 3 and WebKitGTK),
which a desktop install usually has already.

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
  checksums above are how you confirm you have what we published.
* A machine with no screen can serve the app to a browser on your network —
  see "Using it from another computer" in the README, **including the warning**:
  there is no password, so the per-launch key is the only lock.
* Found a problem? [Discord](https://discord.gg/QesugQzTp), an
  [issue](../../issues), or <info@moneta-home.com>. Please include the output of
  `moneta-home --diagnose`, which says where your data is and which build you
  have, and nothing from your records.
