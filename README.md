# Moneta Home — Free, for Linux

**Moneta Home** is a personal money planner that runs on your own computer.
Your accounts, budgets, forecasts and reports stay on that computer: no account
to create, no cloud, and it works with no internet connection.

This repository is where the **Linux packages** are published. It is a
distribution channel — releases, installation notes and issues. The application
source is not published here.

Website: <https://www.moneta-home.com> · Guides: <https://www.moneta-home.com/guides>
Chat with us on [Discord](https://discord.gg/QesugQzTp).

## Download

Take the newest package from [Releases](../../releases), **matching your
machine**:

| Your machine | File |
|---|---|
| A PC or laptop (Intel or AMD, 64-bit) | `moneta-home_<version>_FREE_LINUX_PC.deb` |
| Raspberry Pi, 64-bit Raspberry Pi OS | `moneta-home_<version>_FREE_RASPBERRY_PI_64.deb` |
| Raspberry Pi, 32-bit Raspberry Pi OS | `moneta-home_<version>_FREE_RASPBERRY_PI_32.deb` |

Not sure which Raspberry Pi OS you run? Ask the machine:

```bash
dpkg --print-architecture
```

`arm64` means 64-bit, `armhf` means 32-bit. (On a PC it prints `amd64`.)

🔴 **The files are not interchangeable.** A program is compiled for one kind of
processor: the PC package cannot run on a Raspberry Pi, and `apt` will refuse it
rather than install something that could not start.

## Install

**From a terminal** — on a Raspberry Pi with no screen of its own, this is the
whole job. Take the file name and the tag from
[Releases](../../releases):

```bash
wget https://github.com/moneta-home/moneta-free-linux/releases/download/<tag>/moneta-home_<version>_FREE_<your machine>.deb
```

Check it is the file we published (`SHA256SUMS` is in the same release):

```bash
wget https://github.com/moneta-home/moneta-free-linux/releases/download/<tag>/SHA256SUMS
sha256sum --ignore-missing -c SHA256SUMS
```

`--ignore-missing` matters: the file lists all three packages, and without it
the two you did not download are reported as failures.

Install it:

```bash
sudo apt install ./moneta-home_<version>_FREE_<your machine>.deb
```

`apt` installs the few system libraries the window needs (GTK 3 and WebKitGTK),
which a desktop install usually has already. Then open **Moneta Home** from your
applications menu, or run `moneta-home`.

**From a browser**, download the package from the release page and either
double-click it or run the same `apt install` line in its folder.

## What you need

* **PC:** Ubuntu 24.04 or later, or Debian 12 or later.
* **Raspberry Pi:** Raspberry Pi OS 12 (bookworm) or later, 32-bit or 64-bit.
* A **desktop** install in either case — this is a desktop application, not a
  service (see "Opening it from another computer").
* About 300 MB of disk space.

## Where your data is kept

```
~/.local/share/moneta-home/
```

The database, a log and a key this installation generates for itself. Removing
the package leaves that folder untouched; delete it yourself if you want the
data gone. Back it up as you would any other folder — there is no copy anywhere
else, which is the point of this edition.

## Using it from another computer

Moneta Home is a desktop application: by default it listens on **127.0.0.1
only**, on a port it picks at each start, so nothing outside that machine can
reach it.

On a machine you keep running — a Raspberry Pi on a shelf, with no screen of its
own — you can have it answer in a browser on your network instead. Two settings
do that:

| Setting | What it does | Default |
|---|---|---|
| `MONETA_PORT` | Listen on this port every time | a free port, chosen at each start |
| `MONETA_BIND` | Which interface to listen on | `127.0.0.1` — this machine only |

```bash
MONETA_NO_WINDOW=1 MONETA_PORT=3470 MONETA_BIND=0.0.0.0 moneta-home
```

The app then answers at `http://<that machine>:3470`, and the log says so.

🔴 **A machine that answers the network must have a password first, and the app
refuses to start without one.** On your own computer there is none: the lock is
your login session, and the app opens straight in. The moment it answers other
machines that is no longer true, so before using `MONETA_BIND` run:

```bash
moneta-home --set-password
```

It asks twice, needs at least 10 characters, and sets the password of the
account **`owner`** — the one account this install has. From a browser on
another computer you sign in with `owner` and that password. **There is no
default password**, and none is printed anywhere: a password published in a
README is a door every installation in the world shares.

If you skip this, the app stops at the start and tells you the line to run —
it will not serve your records to a network with no password on them.

⚠️ **What a password does not fix.** This is plain HTTP, so the session cookie
travels unencrypted and can be read on the way; every device on that network can
reach the door, including a guest's phone. Never forward this port on a router,
and prefer the SSH tunnel below when you can.

The app prints the address and the warning at every start:

```
  serving http://127.0.0.1:3470/app
  🔴 REACHABLE ON THE NETWORK (MONETA_BIND=0.0.0.0).
     Anyone who can reach this machine can reach the sign-in
     page, and this launch's key opens it without signing in.
     This is plain HTTP: the session cookie travels unencrypted.
     on this network: http://192.168.1.24:3470
```

The last line is the address to open on another computer. (The first line names
the loopback address because that is what the app's own window talks to, even
when it is listening on every interface.)

### Starting it automatically

To have it running after every reboot, as a service of your own user:

```bash
mkdir -p ~/.config/systemd/user
cat > ~/.config/systemd/user/moneta-home.service <<'EOF'
[Unit]
Description=Moneta Home (no window)
After=default.target

[Service]
Environment=MONETA_NO_WINDOW=1
Environment=MONETA_PORT=3470
ExecStart=/usr/bin/moneta-home
Restart=on-failure

[Install]
WantedBy=default.target
EOF
systemctl --user daemon-reload
systemctl --user enable --now moneta-home
sudo loginctl enable-linger $USER      # so it starts without you logging in
```

Add `Environment=MONETA_BIND=0.0.0.0` only if you accepted the warning above.

Nothing changes after a restart: the port is the one you set, and you sign in
as `owner` with the password you chose. **You do not have to look up a new key
each time** — that per-launch key is for the app's own window, on the machine
itself.

📌 The log sits **beside your data**, so if you moved the data folder, look
there instead — `moneta-home --diagnose` prints the path it is using.

### After a restart

Your installation and your data survive a reboot, and with the service above the
app comes back by itself, on the same port, with the same password.

📌 **If you would rather not put the app on your network at all**, leave
`MONETA_BIND` unset and forward the port over SSH from the other computer
(`ssh -L 8088:127.0.0.1:3470 <user>@<that machine>`, then open the address from
the log with `127.0.0.1:8088` in place of its port). Your SSH login then guards
the app, and nothing is exposed.

## What the free edition does

One current account and two savings accounts, with transactions,
recurring entries, categories, budgets, goals, the forecast, the dashboard and
reports, in your own currency.

Not in the free edition: credit cards and loans, cash, investment and child
accounts, the portfolio, bank statement import, spreadsheet templates,
scenarios, and the AI help. Those belong to the paid editions.

## Support

Ask on [Discord](https://discord.gg/QesugQzTp), open an
[issue](../../issues), or write to <info@moneta-home.com>. Please say
which package and version you installed, and include the output of:

```bash
moneta-home --diagnose
```

That prints where your data is, which build you have, and nothing from your
records.

## Licence

Moneta Home is proprietary software — see [LICENSE](LICENSE). The packages here
may be downloaded and used free of charge; they may not be redistributed,
repackaged or reverse-engineered. "Moneta Home" is a trade mark of the copyright
holder.

The application includes open source components under their own licences; the
notices are in `/usr/share/doc/moneta-home/THIRD-PARTY-NOTICES.txt.gz` once
installed.
