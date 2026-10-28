# What Distrobox Is

Every Linux distribution is, roughly, the
same kernel wearing a different wardrobe:
a different package manager, different
libraries, different versions of the same
tools. Sometimes the wardrobe is the whole
point. A program is packaged for Arch but
not for your Fedora. A build guide assumes
Ubuntu's `apt`, and you run openSUSE. An
old project needs an old library your
system has moved past. Normally the answers
are a virtual machine (a whole second
computer, with its own disk and desktop to
maintain) or giving up.

**Distrobox is a third answer. It runs
another distribution inside a container on
your machine, and then dissolves the wall
between the two.** You type
`distrobox enter writing`, your prompt
changes, and you are — to every practical
test — on the other distribution: its
package manager installs its packages, its
shell runs its tools. But your files are
your files, in the same home folder, at
the same paths. Your display, your sound,
your network and your devices are shared.
When you leave the box, everything it
installed stays in the box, and your host
system is exactly as clean as before.

Underneath, a distrobox is an ordinary
**container** — the same kind Podman or
Docker runs — created with a long list of
sharing options and a setup script that
runs inside it on first start. Distrobox
itself is a set of shell scripts that
drive a **container manager** (Podman by
preference, Docker or Lilipod if asked).
It does not reimplement containers; it is
a opinionated front door to them, tuned
for one job: making a second distribution
feel like a room in your own house rather
than a machine across the street.

## Why anyone would do this

- **The package exists somewhere else.**
  A tool is packaged for one distribution
  and not yours. Enter a box of that
  distribution, install it, use it — or
  export it to your host's menus
  (chapter 8) and forget the box exists.
- **A clean room that costs nothing.**
  Compiling a project drags in forty
  build dependencies. In a box, they land
  on the box's system, and deleting the
  box deletes the mess. Your host never
  sees them.
- **Old software on a new system, or the
  reverse.** A box can be an older release
  with the libraries a legacy program
  expects, or a rolling release with
  compilers newer than your stable host
  dares to carry.
- **Trying a distribution from inside.**
  Living in Arch's package ecosystem for
  a week — from your own terminal, with
  your own files — teaches more than any
  review, and the exit door is one
  command.
- **One machine, many toolboxes.**
  Developers keep a box per project or
  per client: each with its own toolchain
  versions, none of them fighting on the
  host.

## The rule, stated before everything else

Distrobox's integration is also its one
real danger, and this guide states it
before any procedure depends on it:

> **A distrobox shares your home folder
> and your user account. It is a workshop,
> not a jail. Software you would not run
> on your host does not become safe
> because you ran it in a box.**

The sharing is not a bug to be patched
later; it is the product. Your documents
are visible in the box because that is
what makes the box useful, and it means
a program in the box can read, change or
delete them exactly as a program on the
host can. The box itself runs with high
privileges over its container (chapter 2
shows the actual creation command, with
`--privileged` in it), and the `--root`
option (chapter 5) makes a box that is
root on your machine in every way that
matters. Nothing in this guide treats a
box as a place to run software you
distrust. If you need a wall, you need a
different tool — a virtual machine, or a
container configured for isolation
(chapter 12 is honest about the
neighbours, and chapter 13 about the
limits). Chapter 13 returns to this rule
with specifics.

## A first taste

After installing (chapter 3), the whole
tool in four commands:

```bash
distrobox create --name writing --image docker.io/library/debian:bookworm
distrobox enter writing
```

The first builds the box from a Debian
image. The second steps into it: the
first entry takes a minute while the box
sets itself up, and then your prompt is a
Debian prompt, `apt` works, and
`cat /etc/os-release` says Debian — while
`ls ~` shows your own files. Type `exit`
and you are home, and the box keeps
everything you installed for next time.

## Who it is for

The developer whose instructions assume a
distribution they do not run. The curious
user who wants another distribution's
software without another computer. The
tidy system-keeper who wants experiments
to leave no residue. The common thread is
one person, one machine, and a wish that
the distribution boundary mattered less.

## What it is not

Distrobox is not a security sandbox (see
the rule above, and chapter 13). It is
not a virtual machine: a box shares your
host's kernel, so it cannot run a
different kernel, a different
architecture's software without help, or
an operating system that is not Linux.
It is not a way to run a production
server: boxes are workspaces for a person
at a terminal, not services for a
network. And it is not a replacement for
learning your own distribution — it is a
way to borrow the neighbours' tools
without moving house.
