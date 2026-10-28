# Many Boxes, Kept Tidy

Outcome: you can see your boxes at a
glance, stop and remove them safely,
refresh their software in one pass, and
spin up a throwaway box that deletes
itself — the whole hygiene kit.

## list: the roll call

```bash
distrobox list
```

prints every box the manager knows, with
its status (`Created`, `Up`, or exited).
The lab used it as its health check
throughout — empty before the first box
(`t04-list-empty.txt`), one `Created`
box after creation (`t08-enter-attempt.txt`),
empty again after removal (`t09-rm.txt`).
For the space boxes and images occupy,
ask the manager: `podman system df` (or
`docker system df`). Boxes share their
base image, but every package you install
inside is a box's own layer, and a
forgotten box with a full TeX install is
a gigabyte-scale paperweight.

## stop: shelves, not destruction

```bash
distrobox stop writing
distrobox stop --all
```

Stopping ends a box's processes; the box,
its installed software and its files all
remain, and the next `distrobox enter`
starts it again (starting is automatic —
you never *start* a box by hand). A
stopped box costs disk and nothing else.
Leaving boxes running costs a little
memory and their processes; on a personal
machine the honest rule is that it
rarely matters, and `--all` before a
system upgrade or a reboot is tidy.

## rm: the point of no return

```bash
distrobox rm writing
distrobox rm --force writing   # no questions, running or not
```

Removal deletes the container and
everything installed in it that is not
in your (shared) home folder, and —
shown live in `t09-rm.txt` — removes the
box's exported commands and apps from
the host as it goes, so no menu entries
are left pointing into the void. It asks
first unless `--force` is given. What it
does *not* delete: the base image (other
boxes may share it; removing images is
the manager's `image rm`), and anything
in your home folder, which was never the
box's to delete. Remove with confidence,
but rebuild from a recipe (chapter 10)
rather than from memory.

## upgrade: refresh every box's software

```bash
distrobox-upgrade writing     # one box
distrobox-upgrade --all       # every box
```

It enters each box and runs its package
manager's upgrade (with the right
incantation per family, so you do not
have to remember whether this one is
`pacman -Syu` or `zypper dup`), plus a
refresh of distrobox's own in-box
plumbing. It starts stopped boxes to
upgrade them and leaves them running —
worth knowing before running `--all`
last thing at night.

## ephemeral: the box that deletes itself

```bash
distrobox ephemeral --image docker.io/library/alpine:3.20
```

creates a box with a random name, enters
it, and removes it when you exit. This
is the safe shape for "I just need to
run this one thing in a clean Ubuntu" —
no residue, no name to think of, nothing
to tidy later. Any create option can
shape it (`--home`, `--additional-packages`
and friends all apply). Remember what it
cannot do: anything you installed or
changed outside the shared home folder
vanishes with it, by design.

Check: with three boxes created,
`distrobox list` names them all; stop
one and re-enter it — entry restarts it,
with your earlier installs intact. The
mental model to keep: boxes are cheap,
state lives in the box until removal,
and `list` never lies about what exists.
