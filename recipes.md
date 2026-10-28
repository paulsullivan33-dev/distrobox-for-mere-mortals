# Recipes

Outcome: a shelf of small, complete
answers to the requests that actually
bring people to distrobox. Each names
its box shape and its catch, if it has
one.

## Try a distribution for an afternoon

```bash
distrobox ephemeral --image docker.io/library/archlinux:latest
```

A rolling-release terminal with your own
files in it, gone when you exit
(chapter 9). The cheapest honest review
of a distribution ever devised: live in
its package manager for a day, then
decide. Catch: nothing you install
inside survives, so take notes outside
the box.

## The newest tool on a conservative host

Your stable host carries last year's
version of a tool; the new release is
what the documentation describes. Make a
box of a fast-moving distribution,
install the tool there, export it
(chapter 8):

```bash
distrobox create --name fresh --image docker.io/library/archlinux:latest
distrobox enter fresh
sudo pacman -S pandoc
distrobox-export --bin /usr/bin/pandoc
```

On the host, `pandoc` is now the new
one. Catch: the host's own older copy
still exists; which one runs depends on
your `PATH` order, with `~/.local/bin`
usually winning.

## One box per project

A project with a fussy toolchain gets a
box named after it, with the toolchain
declared up front:

```bash
distrobox create --name sitegen --image docker.io/library/debian:bookworm \
  --additional-packages "git hugo nodejs make"
```

Work happens in the project's folder —
shared, as always — with the box
supplying the versions the project
pins. Catch: write the box into a
manifest (chapter 10) on day one; a
project box you cannot rebuild is a
liability with a prompt.

## The app your distribution does not package

An application exists as a package for
another distribution only. Box that
distribution, install the app inside,
export the app itself
(`distrobox-export --app <name>`), and
it appears in your host's menus labelled
with the box's name. Catch: the app
shares your real home folder, so its
settings live alongside your other
apps' — usually a convenience, and
chapter 6 explains the rare clash.

## A cleaner room for an untrusted build

A stranger's build script wants to
install half the internet. Give it
less of yours:

```bash
distrobox create --name quarantine --image docker.io/library/debian:bookworm \
  --home ~/boxes/quarantine-home
```

Copy *only* the project into that
private home, and run the script inside.
The catch must be stated without
flinching: this limits litter and
accidents, it does not create safety.
The box still shares your network and
can see the host at `/run/host`
(chapter 13). For software you truly
distrust, the answer is a virtual
machine, not a tidier box.

## Keep a legacy toolchain alive

An old project builds only with an old
compiler on an old release. Pin both:

```bash
distrobox create --name legacy --image docker.io/library/debian/eol:bullseye
```

Old-release images stay on the
compatibility list for exactly this
reason. The box never upgrades its
base, the project always builds, and
your host stays current. Catch: an old
box has old vulnerabilities too — build
in it, do not browse from it.
