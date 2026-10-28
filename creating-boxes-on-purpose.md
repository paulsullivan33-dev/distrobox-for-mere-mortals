# Creating Boxes on Purpose

Outcome: you can shape a box at creation
time — its image, its home, its extra
mounts and packages, whether it has an
init system, whether it is rootful — and
you know which choices can only be made
here, because a box's shape is set when
it is created.

The full option surface is the lab's
transcript `t01-create-help.txt`; this
chapter walks the ones that matter, in
the order you will meet them.

## Choosing the image

With no `--image`, you get the default:
the current Fedora toolbox image — a sane
default, and the one upstream tests most.
The choice that matters is the
*distribution family*, because it decides
the package manager you will live with
inside: `apt` (Debian, Ubuntu), `dnf`
(Fedora and the RHEL family), `pacman`
(Arch), `apk` (Alpine), `zypper`
(openSUSE), `xbps` (Void), and more.
`distrobox create --compatibility` prints
the tested image list (the lab counted
122 entries, from Oracle Linux to Gentoo
to a pinned Alpine 3.20). Prefer a pinned
release (`debian:bookworm`,
`alpine:3.20`) over `:latest` for a box
you intend to keep: the box's identity
stays put when you rebuild it.

## Home: shared, or the box's own

By default the box shares your real home
folder — the integration chapter 6 is
about. `--home` gives the box a home of
its own:

```bash
distrobox create --name tidy --image docker.io/library/archlinux:latest --home ~/boxes/tidy-home
```

Programs in that box see the custom
folder as `~`. Your real home is still
reachable at `/run/host/home/…` — a
custom home tidies, it does not isolate
(chapter 13). It is the right choice for
a box that will run software which
sprays configuration files everywhere.

## Extra mounts and packages

`--volume /host/path:/box/path` adds a
mount to the defaults (chapter 6 lists
what is already mounted). Repeat it for
several. `--additional-packages "git tmux vim"`
installs packages during the first-entry
setup, so a rebuilt box arrives already
equipped:

```bash
distrobox create --name writing --image docker.io/library/debian:bookworm \
  --additional-packages "git pandoc texlive-latex-recommended"
```

`--pre-init-hooks "command"` runs a
command inside at the very start of
setup (classically, to enable an extra
package repository before the main
install), and `--init-hooks "command"`
runs at the end — the place for a box's
own finishing touches. Both are stored
with the box and re-applied if it is
recreated.

## An init system inside: --init

Normally a box has no init system; its
entrypoint is distrobox's setup, and the
box exists to serve your shell. With
`--init`, the box boots its own init —
systemd on the distributions that use it
— and can run real services inside:
databases, web servers, a whole stack
behind one enter command. The costs are
real: the image may need extra packages
(the project's documentation lists them
per distribution), the host's process
list is no longer visible inside (the
option implies `--unshare-process`), and
startup is heavier. Reach for `--init`
when the box must behave like a small
machine, not before.

## Rootful boxes: --root

`--root` makes the manager run the box
with root privileges, and asks for your
password through sudo (or the program set
in `distrobox_sudo_program`). Inside, you
are still you — but the box's processes
are root-owned on the host side, and the
distance between the box and your system
shrinks to nothing. Some setups need it
(certain VPNs, low-level tooling, hosts
where rootless mode is unavailable). It
is never the default for a reason, and
chapter 1's rule applies with full force:
a rootful box is your machine, with no
polite fiction left. Never run
`sudo distrobox …` — `--root` is the
supported spelling.

## Unsharing, a little

The defaults share the host's network,
process, IPC and devices. Each has an
unshare switch — `--unshare-netns`,
`--unshare-process`, `--unshare-ipc`,
`--unshare-devsys`, `--unshare-groups`,
or `--unshare-all` — for boxes that
should see less of the host: a box with
its own network namespace for network
experiments, say. Every unshare trades
away some of the integration that makes
distrobox pleasant, so treat them as
surgical options, not a hardening
checklist; a box you must unshare
completely is a box that wants to be a
plain container or a VM (chapter 12).

## Clones and dry runs

`--clone oldbox` creates a new box from
an existing box's current state rather
than from its image — the way to fork a
tuned environment, or to rename one
(clone, verify, remove the original).

And the option this guide's lab leaned
on: `--dry-run` prints the exact manager
command distrobox would run, and runs
nothing:

```bash
distrobox create --name test --image docker.io/library/alpine:3.20 --dry-run
```

When a box behaves strangely, the dry
run is the ground truth about what was
actually asked for — every mount, every
namespace, every flag — and chapter 14
starts there more often than anywhere
else.

Check: create two boxes differing in one
option (`--home` is the easiest) and
diff their dry runs. The difference you
see is the whole effect of the option;
there is no other hidden state.
