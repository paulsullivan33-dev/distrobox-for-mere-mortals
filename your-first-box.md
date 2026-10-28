# Your First Box

Outcome: you have created a box, entered
it, confirmed from inside that you are in
another distribution standing in your own
home folder, and left it — with the box
and everything in it kept for next time.

An honesty note before the commands, in
the open where it belongs: this guide's
lab ran on a restricted sandbox whose
kernel refuses the namespace operations
containers need to *start*. The lab
created a real box and listed it
(transcripts below), and the start itself
failed there with a permission error the
lab kept (`t08-enter-attempt.txt`). On a
normal machine the same commands simply
work, and the rest of this chapter
describes that normal path, which is the
project's documented behaviour.
`criteria.md` keeps the full ledger.

## Create

```bash
distrobox create --name writing --image docker.io/library/debian:bookworm
```

Pick a short name you will enjoy typing,
and an image from the compatibility list
(`distrobox create --compatibility`,
`t10-compatibility.txt`). If the image is
not already downloaded, distrobox offers
to pull it; `--pull` forces a fresh
download and `--yes` skips the asking.
Creation itself is quick — it registers
the container with the manager; the real
setup happens on first entry. The lab's
run, with Alpine as the image
(`t07-create.txt`):

```text
$ distrobox create --name alpinebox --image docker.io/library/alpine:3.20
Creating 'alpinebox' using image docker.io/library/alpine:3.20	 [ OK ]
Distrobox 'alpinebox' successfully created.
To enter, run:

distrobox enter alpinebox
```

Check: `distrobox list` shows the box,
with status `Created` until it first
runs:

```text
ID           | NAME                 | STATUS             | IMAGE
9cb8783d6700 | alpinebox            | Created            | docker.io/library/alpine:3.20
```

## Enter

```bash
distrobox enter writing
```

The first entry starts the box and runs
its setup (chapter 2's entrypoint): base
packages are installed with the box's own
package manager, your user is created
inside with your name and IDs, and the
shared sockets are wired. Expect a minute
or two and a screen of package-manager
output. Then the prompt changes, and you
are inside.

Three checks, worth running every first
time in a new box:

```bash
cat /etc/os-release    # the box's distribution, not the host's
whoami                 # you — the same user name as on the host
ls ~                   # your own files, at their usual places
```

That combination — another distribution's
`/etc`, your name, your files — is the
whole idea of the tool in one screen. The
shell you get is your usual shell if the
box has it, and the working directory is
wherever you were standing on the host.

## Do something, then leave

Inside, the box's world is complete:
install software with its package manager
(`sudo apt install …` on Debian or
Ubuntu, `sudo dnf …` on Fedora,
`sudo pacman …` on Arch) and run it.
Files you create in your home folder are
simply files — they are on the host too,
because it is the same folder. Files
created elsewhere in the box (in `/opt`,
say, or `/tmp` inside the box) belong to
the box only.

Leave with `exit`, or Ctrl-D. Leaving
does not stop the box; it keeps running
in the background until you stop it
(chapter 9) or the machine reboots.
Re-enter any time with the same command —
later entries are fast, because setup
only happens once.

## Running one command without moving in

`distrobox enter` with `--` runs a single
command in the box and returns:

```bash
distrobox enter writing -- pandoc --version
```

This is the form scripts and exported
commands use (chapter 8), and it starts
the box first if it was stopped. If the
command needs flags of its own that
distrobox might eat, everything after
`--` is passed through untouched.

## If it goes wrong

A first entry that stalls is usually the
box's package manager being slow, not
stuck — setup installs real packages.
An entry that *fails* with a message
about namespaces or permissions, on a
machine that is not a restricted sandbox,
usually means the container manager
itself is unhealthy; chapter 14 works
through the common cases, starting with
asking the manager directly
(`podman run --rm alpine cat /etc/os-release`)
whether it can run anything at all.
