# Distrobox for Mere Mortals

Your computer runs one Linux distribution,
and most days that is the right number.
But a tool you need is packaged for
another one. A guide assumes a package
manager you do not have. An old project
wants old libraries. **Distrobox** answers
all of these the same way: it runs another
distribution in a container on your
machine, and then dissolves the wall
between the two — your home folder, your
display, your devices and your shell,
shared into the box, so the other
distribution's software feels like it was
yours all along.

This guide explains how that works: what
a box actually is underneath (an ordinary
container, created with a long list of
sharing options), how to install
distrobox and give it a container manager
to drive, how to create and enter your
first box, shape boxes on purpose, live
with your home folder in every box,
install and export software, keep a
stable of boxes tidy, and describe them
all in a recipe file so they can be
rebuilt anywhere. The limits are stated
plainly, including the big one, which is
this guide's rule from chapter 1 onward:
**a distrobox shares your home folder and
your account; it is a workshop, not a
jail, and software you would not run on
the host does not become safe inside
one.**

The lab behind this guide ran the real
thing: **distrobox 1.8.2.5**, installed
from the project's release tarball with
its own install script, against a real
container backend (Docker, from its
official static binaries) stood up in a
scratch folder. Against it the lab listed
an empty system, proved the configuration
file is honoured by pinning a manager the
machine did not have, pulled a real
image, created a real box and watched it
appear in the list, printed the full
container command distrobox generates —
`--privileged`, host namespaces, your home
mounted at its real path — parsed a
two-box recipe file into the commands it
would run, and removed the box again,
exports and all.

What the lab could not do, it did not
fake. Its machine was a restricted
sandbox whose kernel refuses the
namespace operations containers need to
*start*, so no box could be entered: a
plain container run died in the runtime,
and `distrobox enter` failed at the same
wall, both kept as transcripts. Starting
and entering a box, and everything done
inside one — the setup completing,
package installs, exported apps on a
desktop, a box's own init system — are
therefore taught from the project's
documentation and labelled as such
wherever they appear. `criteria.md`
keeps the full ledger of what was run
and what was only read.

## What this guide covers

- [**What Distrobox is**](what-distrobox-is.md) —
  the mental model: another distribution
  in a container with the wall dissolved,
  and the rule about what a box is not
- [**The moving parts**](the-moving-parts.md) —
  the scripts, the container manager, the
  image, the box, the setup entrypoint,
  and the two bridges between box and host
- [**Installing Distrobox**](installing-distrobox.md) —
  a manager first, then distrobox, the
  version and empty-list checks, and the
  configuration file that can pin it all
- [**Your first box**](your-first-box.md) —
  create, enter, three checks that prove
  where you are, one command without
  moving in, and leaving without losing
- [**Creating boxes on purpose**](creating-boxes-on-purpose.md) —
  choosing images, private homes, extra
  mounts and packages, hooks, an init
  system, rootful boxes, unsharing, clones
  and the all-revealing `--dry-run`
- [**Your home in every box**](your-home-in-every-box.md) —
  the shared home folder and what rides
  on it, you as a user inside, devices,
  display, sound, and where you land
- [**Software inside the box**](software-inside-the-box.md) —
  the box's own package manager as the
  law, binaries that do not cross, links
  that open on the host, and services
  with and without an init system
- [**Exporting apps and commands**](exporting-apps-and-commands.md) —
  a box's program in your menus and your
  `PATH`, the box itself in the
  application list, and taking it all back
- [**Many boxes, kept tidy**](many-boxes.md) —
  list, stop, remove, upgrade, and the
  ephemeral box that deletes itself
- [**Assemble — boxes from a file**](assemble.md) —
  the manifest that describes your boxes,
  applying it, rebuilding from it, and
  reading one before you trust it
- [**Recipes**](recipes.md) — a
  distribution for an afternoon, the
  newest tool on a conservative host, one
  box per project, the unpackaged app,
  a cleaner room, a legacy toolchain
- [**The neighbours**](the-neighbours.md) —
  Toolbox, plain containers, Incus,
  virtual machines, Nix, and the
  venerable chroot: what each does that
  distrobox doesn't, and vice versa
- [**Limits**](limits.md) — not a
  security boundary, not another kernel,
  not its own network, not a service
  manager, and the two systems that
  never quite merge
- [**Troubleshooting**](troubleshooting.md) —
  the missing manager that is really a
  config pin, the namespace refusal, the
  stalled first entry, the wrong shell,
  the missing display, the shadowed
  export, and the box that will not die
- [**Glossary**](glossary.md) — the
  terms, collected in one place

## What it doesn't cover

Keeping secrets from software running in
a box — a box is not built for that, and
chapter 13 explains why no flag makes it
so. Running other operating systems,
other kernels, or production services
for other people. Managing fleets of
containers for a team: distrobox is one
person's workbench, and chapter 12 points
at the tools that grew up to be more.

## How to use this guide

Read chapter 1 for the model and the
rule, and chapter 2 before installing
anything — knowing which part holds what
turns later confusion into a named
problem. Chapter 4 creates your first box
in the same sitting as the install;
chapter 5 is worth reading before your
*second*, because a box's shape is set
at creation. Chapter 6 is not the
optional chapter: most surprises in
distrobox are the shared home folder
being exactly as shared as promised.
Chapter 10, the recipe file, repays its
length the first time a box must be
rebuilt. The lab's transcripts live in
`distrobox-lab/transcripts/`; its recipe
file is `examples/distrobox.ini` and its
notes are `examples/lab-notes.txt`.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Podman for Mere Mortals](https://paulsullivan33-dev.github.io/podman-for-mere-mortals/)
- [Docker for Mere Mortals](https://paulsullivan33-dev.github.io/docker-for-mere-mortals/)
- [Linux for Mere Mortals](https://paulsullivan33-dev.github.io/linux-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/distrobox-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/distrobox-for-mere-mortals/blob/main/LICENSE-CODE).
