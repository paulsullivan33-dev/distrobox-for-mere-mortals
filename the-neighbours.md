# The Neighbours

Outcome: you can place distrobox in its
neighbourhood — what each nearby tool
does that distrobox does not, and what
distrobox does that they do not — so
choosing it (or leaving it) is a
decision, not a habit. These are
characterisations, not benchmarks; each
tool is described the way its own
documentation describes it.

## Toolbox (toolbx)

The closest relative — close enough that
the family resemblance confuses
searches. Toolbox came first, from the
Fedora world, built on Podman with the
same idea: a container that shares your
home and session so another environment
feels native. Distrobox began as the
more distribution-agnostic take: it
works the same on any host, drives
Docker and Lilipod as well as Podman,
supports a much wider image list (its
defaults are not Fedora-shaped), and
adds the export machinery and manifests
of chapters 8 and 10. If your world is
Fedora Silverblue and one toolbox,
Toolbox is the in-house dialect of the
same language; if your boxes span
distributions and hosts, distrobox is
the generalist.

## Plain Podman or Docker, by hand

Everything distrobox does, the manager
can do with explicit commands — because
distrobox is, in the end, a generator of
those commands (chapter 2's dry run is
the whole trick, visible). What you gain
by hand: total control, including the
isolation distrobox deliberately gives
away — no shared home, no privileged
flag, a network of the container's own.
What you lose: the integration is a
weekend of flags and setup scripts to
rebuild, per box. The working rule:
distrobox for *workspaces you live in*,
plain containers for *workloads you
deploy* — and for anything you need to
be a wall.

## Incus and LXC system containers

System containers aim to feel like a
small *machine*: their own init system,
their own network identity, services
managed like a server's. Incus (and LXC
beneath it) is the polished form. They
share the host kernel, like distrobox,
but the resemblance ends there: a system
container does not dissolve into your
session — no shared home by default, no
exported apps, its own users and its own
network. Choose them when the thing you
are running is a *service* that should
behave like a host; choose distrobox
when it is a *workbench* that should
behave like your desk.

## Virtual machines

A VM is the real wall: its own kernel,
its own disk, hardware emulated or
passed through, and isolation that does
not depend on politeness. It is also the
heavy answer — gigabytes of image, a
desktop or SSH session to cross into,
files shared by arrangement rather than
by nature. Everything chapter 13 lists
as a distrobox limit — untrusted
software, a different kernel, a different
architecture (with emulation), a server
others depend on — is a VM's home turf.
Distrobox wins on friction; the VM wins
on separation. Serious users run both,
for different jobs.

## Nix and Guix

A different road to "the other
distribution's software": Nix (and its
cousin Guix) installs packages into an
isolated store on your *own* system,
with versions coexisting and
environments described declaratively.
No containers, no second filesystem,
and a learning curve with a reputation
it has honestly earned. If your need is
many versions of many tools, perfectly
pinned, on one system, look hard at Nix.
If your need is *Debian itself*, or
*Arch itself*, with its own package
manager behaving as its users expect —
that is a box.

## chroot, the ancestor

A `chroot` changes a process's apparent
root directory — the original "other
filesystem in a folder" trick, still
useful in recovery work. It has no
namespaces, no setup, no user creation,
no integration: you build every comfort
by hand. Distrobox is what chroot grows
into when someone automates it for
twenty years of edge cases. Honour the
ancestor; use the descendant.
