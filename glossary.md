# Glossary

The terms, collected in one place. Each
is defined where it is first used, too;
this page is for the second meeting.

**Assemble** — `distrobox assemble`:
building or removing a set of boxes from
a manifest file (chapter 10).

**Box** — a distrobox container: one
distribution's filesystem and tools,
created for living in, with the host's
home and session shared into it.

**Container** — a process (or set of
them) fenced by kernel namespaces and
control groups, running from an image's
filesystem. A box is a container with
the fence deliberately built low.

**Container manager** — the program that
actually stores images and runs
containers: Podman, Docker or Lilipod.
Distrobox drives one; it is not one.

**CONTAINER_ID** — an environment
variable set inside every box, holding
the box's name; its presence is also
how a script detects it is in a box.

**Distrobox-init** — the script mounted
into a box as its entrypoint; on first
start it installs base packages, creates
your user inside, and wires the sharing.

**Distrobox-host-exec** — the bridge
command available inside a box that runs
a command on the host.

**Enter** — `distrobox enter <name>`:
start the box if needed and open your
shell (or, after `--`, run one command)
inside it.

**Entrypoint** — the program a container
runs first and lives under; in a box,
distrobox-init.

**Ephemeral** — `distrobox ephemeral`: a
box created, entered and automatically
removed on exit.

**Export** — `distrobox-export`, run
inside a box: publishing one of the
box's apps (`--app`) or commands (`--bin`)
to the host as a wrapper.

**Generate-entry** — putting the box
itself into the host's application menu,
as a terminal that enters it.

**Image** — a packaged, read-only root
filesystem for a distribution; the
template a box is created from.

**Init system** — the first process in a
real system (usually systemd), which
starts and supervises services. A box
has one only when created with `--init`.

**Manifest** — the `distrobox.ini` file
for assemble: one section per box,
options in `key=value` form.

**Namespace** — a kernel partition of
some resource (processes, network,
mounts, IPC). Sharing a namespace means
seeing the host's version of that
resource; unsharing gives the box its
own.

**Privileged** — a container mode with
nearly all restrictions lifted. Boxes
run privileged by design; see
chapter 13 for what that admission
means.

**Rootful / rootless** — whether the
container manager (and so the box) runs
with root privileges or as your user.
Rootless is distrobox's default and
recommendation; `--root` is the rootful
spelling.

**/run/host** — the path inside a box
where the host's entire filesystem is
mounted: `/run/host/etc` is the host's
`/etc`.

**Wrapper** — the small script an export
leaves in `~/.local/bin` (or the desktop
file in your menus): it enters the box
and runs the real program there.
