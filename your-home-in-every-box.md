# Your Home in Every Box

Outcome: you can predict exactly what a
program inside a box sees of your files,
your devices and your desktop session —
and what it means that the user inside
the box is *you*.

## The home folder is the same folder

A distrobox mounts your home directory
into the box at the identical path (the
dry run in chapter 2 shows the line:
`--volume "/home/hatch":"/home/hatch"`).
Not a copy, not a sync — the same files.
Edit a document in the box and it is
edited, everywhere, at once. Downloads,
dotfiles, SSH keys, browser profiles in
your home: all present, all live. This
is why a box feels like a room in your
house, and it is why chapter 1's rule
exists. When you do not want it, the
`--home` option (chapter 5) gives the
box a private home folder instead — the
choice is made at creation and cannot be
changed for an existing box.

Two consequences surprise people:

- **Dotfiles are shared too.** The box's
  shell reads the same `~/.bashrc` as the
  host's. Mostly that is a gift (your
  aliases follow you in). When you need
  box-specific shell setup, the box sets
  the environment variable `CONTAINER_ID`
  to the box's name (visible in the dry
  run), so a shared rc file can branch on
  it — and scripts can use its presence
  to detect that they are inside a box at
  all.
- **The host is one path away.** The
  whole host filesystem is mounted in the
  box at `/run/host`: `/run/host/etc`,
  `/run/host/usr`, and so on. Tools that
  need to peek at the host (and the
  export machinery of chapter 8) use it.
  So can anything else running in the
  box, which is the rule from chapter 1
  again, wearing a path name.

## You, inside

The box contains a user with your name,
your user and group IDs, and your shell —
created by the setup on first entry.
Files you create in the box are owned by
you on the host, with no translation
layer and no permission surprises.
`sudo` works inside the box, against the
box's own system: during first setup you
may be asked to choose a password for it,
and that password lives in the box's
`/etc/shadow`, nowhere else. Sudo inside
a (rootless) box grants root *over the
box's filesystem* — it does not make you
root on the host, though with your home
mounted read-write and the host one path
away, chapter 13 declines to call that a
security boundary.

## Devices, display, sound

The sharing that makes graphical and
hardware work pleasant is set up by the
same creation command: `/dev` and `/sys`
are mounted into the box, and the
session's sockets are passed through, so
inside the box you can typically:

- open windows on the host's display
  (Wayland or X11), which is what makes
  chapter 8's exported GUI apps possible;
- play and record sound through the
  host's audio server;
- use USB and serial devices, cameras
  and printers as the host sees them;
- reach the network exactly as the host
  does — the box shares the host's
  network namespace by default, so
  `localhost` inside the box *is* the
  host's localhost, ports and all.

`--unshare-devsys` and friends (chapter
5) trim parts of this at creation time,
for the rare box that needs less.

## Where you land when you enter

`distrobox enter` starts your shell in
the same directory you were standing in
on the host — possible because that
directory is the same directory inside.
If the directory exists only in the box's
own filesystem (you were deep in a
previous box session's `/opt`, say),
entry falls back to your home. The
`--no-workdir` flag skips the courtesy
and always lands in the box's home.

Check: on the host, stand in some folder
under your home and create a file
(`touch marker.txt`). Enter a box: the
file is there, `pwd` agrees with the
host, and `echo $CONTAINER_ID` names the
box you are in. One folder, two
distributions, no copying — that is the
chapter in one gesture.
