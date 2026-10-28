# Installing Distrobox

Outcome: distrobox is installed, the
version command answers, and
`distrobox list` runs against a real
container manager and shows an empty
table — the state every later chapter
assumes.

## First, the container manager

Distrobox is a front door; the container
manager is the house. Install one first
if you have none:

- **Podman** is the recommended manager
  and the one distrobox's documentation
  is written around. Install it from your
  distribution's packages (`podman` almost
  everywhere) and prefer its **rootless**
  mode — running boxes as your own user,
  no sudo involved. Rootless Podman needs
  the usual helpers your distribution
  sets up with it (subuid ranges, a user
  session); on a normal desktop install
  this simply works.
- **Docker** works too. On most systems
  it runs as root through its daemon,
  which makes every box a rootful one —
  workable, and chapter 13 is blunt about
  what that means.
- **Lilipod** is a small third option from
  the same author, aimed at minimal
  systems.

Check what you have:

```bash
podman --version || docker --version
```

Then check that distrobox can see it,
once distrobox is installed below:
`distrobox list` should print a table
header and nothing else. If instead it
prints the *Missing dependency* message
from chapter 2, the manager is missing
or distrobox is pinned to a different one
(the configuration section below shows
how that happens).

## Installing distrobox itself

**From your distribution**, if it carries
a recent version: the package is called
`distrobox` in most repositories (Fedora,
openSUSE, Arch, Debian, Ubuntu and
others package it).

**From the project's install script**,
which always gives the current release
and is what this guide's lab used:

```bash
curl -s https://raw.githubusercontent.com/89luca89/distrobox/main/install | sudo sh
```

Run without `sudo`, it installs under
`~/.local` for your user only. The script
accepts `--prefix <dir>` to choose the
base directory; the lab installed the
official release tarball's script with a
prefix inside its scratch folder, so
nothing on the lab machine was touched
system-wide. There is a matching
`uninstall` script in the same place.

## Check the install

```bash
distrobox --version
```

The lab's answer (transcript
`t00-version.txt`):

```text
$ distrobox --version
distrobox: 1.8.2.5
distrobox version: 1.8.2.5
```

And the check that closes the chapter —
with Docker standing in as the lab's
manager (`t04-list-empty.txt`):

```text
$ distrobox list
ID           | NAME                 | STATUS             | IMAGE
```

An empty table is success: it means a
manager answered, and there are no boxes
yet. Chapter 4 fixes that.

## Configuration files

Distrobox reads shell-style settings
from, in order, `/etc/distrobox/distrobox.conf`,
`~/.config/distrobox/distrobox.conf`,
and `~/.distroboxrc`; environment
variables named `DBX_…` override both.
Useful settings include
`container_manager` (pin podman, docker
or lilipod), `container_image_default`
and `container_name_default` (change the
defaults `distrobox create` falls back
to), `distrobox_sudo_program` (use `doas`
instead of `sudo` for rootful work), and
`non_interactive=1` (never prompt).

The lab proved the file is really read,
not just documented (`t05-config-manager.txt`).
With a working Docker backend available,
this one line was placed in
`~/.config/distrobox/distrobox.conf`:

```text
container_manager="podman"
```

and `distrobox list` immediately stopped
working — it obeyed the pin, went looking
for the Podman that machine does not
have, and reported the missing dependency
instead of quietly using Docker. Deleting
the file brought the empty table back.
Two lessons ride on that small test: the
config file is honoured, and a stale pin
is the first suspect when distrobox
claims no manager exists on a machine
that plainly has one (chapter 14 returns
to it).

## Keeping it current

However you installed it, distrobox tells
you its version the same way, and
upgrading is your package manager's job
(or a re-run of the install script). The
*contents* of boxes are upgraded
separately, from inside, by
`distrobox-upgrade` — chapter 9.
