# Assemble — Boxes from a File

Outcome: your boxes are described by a
small text file you can keep, share and
version-control, and one command builds
them all — on this machine today, or on
a new machine next year, identically.

Everything before this chapter made
boxes one at a time, by hand. That does
not scale past three boxes, and it does
not survive a new laptop. `distrobox
assemble` is the answer: a **manifest**
file — by default `./distrobox.ini` in
the folder you run it from — with one
section per box.

## The manifest

The lab wrote and exercised this one
(`examples/distrobox.ini` in this
repository, run in `t12-assemble-dry-run.txt`):

```ini
[writing]
image=docker.io/library/debian:bookworm
additional_packages="git tmux"
init=false
pull=false
root=false
start_now=false

[small]
image=docker.io/library/alpine:3.20
init=false
pull=false
root=false
start_now=false
```

Each `[name]` section is a box, and the
keys are chapter 5's create options in
file form: `image`, `additional_packages`,
`home`, `volume`, `init`, `init_hooks`,
`pre_init_hooks`, `nvidia`, `root`,
`pull`, and two that only make sense in
a file — `start_now` (enter-worthy boxes
start as part of the build) and
`replace` (this box is always rebuilt
from scratch when the manifest is
applied). Comments start with `#`. The
file can also describe exports
(`exported_apps`, `exported_bins`), so a
rebuilt box can arrive with its apps
already on your menus.

## Building and rebuilding

```bash
distrobox assemble create                      # reads ./distrobox.ini
distrobox assemble create --file ~/boxes.ini   # or a path — or a URL
distrobox assemble create --name writing       # just one section
distrobox assemble create --replace            # force-rebuild all
distrobox assemble rm                          # remove everything in the file
```

Applying the manifest is idempotent in
spirit: boxes that exist and are not
marked `replace` and are not forced are
left alone; the rest are created. The
lab could not start boxes, so it ran the
mode that asks for nothing but the plan
— worth knowing in its own right:

```text
$ distrobox assemble create --dry-run
 - Creating writing...
--name "writing"
--entrypoint /usr/bin/entrypoint
 - Creating small...
--name "small"
--entrypoint /usr/bin/entrypoint
```

The manifest parsed, and one full
`docker create` command per box was
generated (excerpts shown; the full text
is transcript `t12-assemble-dry-run.txt`).
A dry run against a manifest is the
fastest review of what a shared recipe
would actually do to your machine —
read one before applying a manifest
someone hands you, because chapter 1's
rule applies to recipes too: a manifest
can mount, share and install a great
deal, and `root=true` in someone else's
file is still root on your machine.

## Why bother

Three habits this enables, in rising
order of value. **Rebuild without
memory:** the box you tuned over a month
is a file, not a recollection, and its
loss is a command, not a funeral.
**Same boxes everywhere:** the manifest
in a git repository gives every machine
you own the same writing box.
**Reviewable change:** changing a box
becomes editing a text file, where the
change can be read, diffed and undone —
the difference between a system you
administer and a system you merely
remember fondly.

Check: keep the manifest from a real
box of yours, delete the box, and
`assemble create` it back. Enter it:
the software from `additional_packages`
is there, and the box is, to its own
tests, the box you deleted.
