# The Moving Parts

Outcome: you can name every piece a
distrobox involves, say which piece holds
what, and read the actual container
command distrobox generates — so when
something misbehaves later, you know
which layer to question.

## The pieces

**The scripts.** Distrobox itself is a
set of small POSIX shell scripts, one per
command: `distrobox-create`,
`distrobox-enter`, `distrobox-list`,
`distrobox-export`, and friends, plus a
`distrobox` wrapper that dispatches to
them (`distrobox create` and
`distrobox-create` are the same call).
There is no daemon and no database. What
a box *is* lives entirely in the
container manager's storage.

**The container manager.** Podman,
Docker or Lilipod does the actual work:
storing images, creating containers,
starting them, executing commands inside.
Distrobox checks which managers are
installed — Podman first — and can be
pinned to one (chapter 3 shows a
configuration file doing exactly that,
and this guide's lab proved the pin is
honoured). If none is installed, every
command stops with the same plain error,
kept in the lab's transcript
`distrobox-lab/transcripts/t03-list-no-backend.txt`:

```text
$ distrobox list
Missing dependency: we need a container manager.
Please install one of podman,  docker or lilipod.
```

**The image.** A box starts from a
container **image**: a packaged root
filesystem of some distribution — Debian
Bookworm, Fedora, Arch, Alpine and over a
hundred more. `distrobox create
--compatibility` prints the list the
project tests against; the lab's copy is
`t10-compatibility.txt`. An image is a
template, not a running thing: many boxes
can be created from one image, and the
image itself is never changed by them.

**The box.** Creating a box asks the
manager to create a container from the
image with a long list of options, most
of them about sharing. This guide's lab
captured the real generated command with
`distrobox create --dry-run`
(`t11-create-dry-run.txt`); the load-
bearing lines:

```text
 docker create
--name "alpinebox"
--privileged
--security-opt label=disable
--ipc host
--network host
--pid host
--volume "/home/hatch":"/home/hatch":rslave
--volume /:/run/host/:rslave
--volume /dev:/dev:rslave
--entrypoint /usr/bin/entrypoint
docker.io/library/alpine:3.20
```

Read it once and the whole design is
visible: the box shares the host's
network, process and IPC namespaces
(`--network host` and friends), mounts
your home folder at its real path, mounts
the entire host filesystem readably at
`/run/host`, and runs **privileged** with
container security labels switched off.
That is chapter 1's rule written in
flags. Distrobox chooses integration over
isolation on purpose, every time.

**The entrypoint: distrobox-init.** The
container's entrypoint is a script of
distrobox's own, mounted in from the
host. On first start it turns the bare
image into a home: it installs a set of
base packages using the box's own package
manager (a shell, sudo, and the plumbing
each distribution needs), creates *your*
user inside the box with your name and
IDs, wires up the shared sockets (display,
sound, agent), and sets up the two
bridges below. First entry is slow
because this is happening; later entries
are quick.

**The two bridges.** Inside every box,
`distrobox-export` (chapter 8) pushes
apps and commands out to the host, and
`distrobox-host-exec` runs a command on
the *host* from inside the box — the
escape hatch that lets a box's build
script call the host's tools. Init also
links the box's `xdg-open` and `flatpak`
to host-exec when the box lacks its own,
so opening a link or a Flatpak from
inside the box acts on the host.

**The generated entry.** A box can put
itself in your host's application menu
(`distrobox-generate-entry`, chapter 8),
and exported apps land there too. These
are ordinary desktop files on the host
whose commands are `distrobox enter …`
in disguise.

## Where things live

- Images and boxes: in the container
  manager's storage (for rootless Podman,
  under `~/.local/share/containers`).
  Deleting distrobox the program does not
  delete your boxes.
- Configuration: `/etc/distrobox/distrobox.conf`
  for the whole machine, then
  `~/.config/distrobox/distrobox.conf` or
  `~/.distroboxrc` for you (chapter 3).
- Exported commands: wrapper scripts in
  `~/.local/bin` by default. Exported
  apps: desktop files in
  `~/.local/share/applications`.
- Nothing else. There is no distrobox
  state directory to back up; a box is
  fully described by the manager's record
  of it, and chapter 10 shows how to keep
  a recipe file that recreates one.

Check: the pieces are separable in
practice. Stop the manager and
`distrobox list` fails; remove distrobox
and the manager still lists the box as an
ordinary container with a
`manager=distrobox` label — because that
is what it is.
