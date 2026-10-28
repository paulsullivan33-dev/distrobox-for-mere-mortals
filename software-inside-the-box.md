# Software Inside the Box

Outcome: you can install, run and update
software inside a box with the box's own
tools, and you understand the one rule
that governs it all — inside a box, the
box's distribution is the law.

## Installing: the box's package manager

There is no `distrobox install` command,
on purpose. Inside the box you use the
distribution's own package manager,
exactly as if you had installed that
distribution on a machine:

| Box family        | Install command                    |
|-------------------|------------------------------------|
| Debian, Ubuntu    | `sudo apt update && sudo apt install <pkg>` |
| Fedora, RHEL kin  | `sudo dnf install <pkg>`           |
| Arch              | `sudo pacman -S <pkg>`             |
| Alpine            | `sudo apk add <pkg>`               |
| openSUSE          | `sudo zypper install <pkg>`        |
| Void              | `sudo xbps-install <pkg>`          |

The package arrives with the box's own
libraries, into the box's own filesystem,
visible to nothing on the host except
through the box. This is the daily
pleasure of the tool: the other
distribution's entire software archive,
one command away, with zero residue on
your system. Software installed this way
persists when you leave and when the box
stops; it is part of the box until the
box is deleted.

Rebuilds deserve one habit: anything you
install by hand is gone if you delete
and recreate the box. The durable
spellings are `--additional-packages`
(chapter 5) for the routine set and a
recipe file (chapter 10) for the whole
box.

## Running things

A program installed in the box runs in
the box: its libraries, its config
under the box's `/etc`, its data wherever
it writes it. The boundary cases to know:

- **Your home is shared** (chapter 6), so
  a program's per-user configuration —
  usually under `~/.config` — is the
  *same files* the host's version of the
  program reads. Two versions of one
  program, one on the host and one in a
  box, generally share settings
  peacefully; now and then a version far
  apart will migrate a config file the
  other cannot read back. The tidy fix is
  a box with its own `--home`.
- **Binaries do not cross.** A program
  compiled against the box's libraries
  runs in the box; copying the bare binary
  to the host and running it there usually
  fails on missing libraries. The supported
  way to use a box program *from* the host
  is to export it (chapter 8), which keeps
  the running part inside.
- **Links and files open on the host.**
  The setup links the box's `xdg-open`
  (and `flatpak`, if the box lacks one)
  to the host bridge, so `xdg-open
  report.pdf` inside a box opens the
  host's viewer. The bridge in the other
  direction is `distrobox-host-exec`,
  which runs any host command from
  inside:

  ```bash
  distrobox-host-exec podman ps
  ```

  It asks before running a host command
  the first time a given command is used,
  and is how box scripts legitimately
  reach host tools.

## Services inside a box

A box without `--init` has no service
manager: a program you start runs while
you keep it running (or under `nohup`,
or in a terminal multiplexer — the
usual shell tricks), and dies when the
box stops. For a database or server you
want *managed*, create the box with
`--init` (chapter 5) and use its systemd
like a machine's: `systemctl start
postgresql` inside, and the unit
persists across entries. Many people run
one `--init` box as their development
stack and keep the rest as shells.

## Updating

Updating software inside a box is the
box's package manager again (`sudo apt
upgrade`, `sudo dnf upgrade`, …).
Distrobox wraps that loop for one box or
all of them — `distrobox-upgrade` —
which is box hygiene rather than
software installation, so it lives with
the other hygiene commands in chapter 9.

Check: enter a box, install something
small from its archive (`cowsay` is the
traditional volunteer), run it, exit.
On the host, confirm the host's package
manager has never heard of it
(`apt list --installed | grep cowsay`
on a Debian-family host prints nothing)
— the install is real, and it is entirely
the box's.
