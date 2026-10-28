# Limits

Outcome: you can state, without
hedging, what a distrobox cannot do and
must not be asked to do — before you
meet each limit the expensive way.

## It is not a security boundary

The largest limit, and chapter 1's rule
with its working shown. A box is created
`--privileged`, with the container
runtime's security labels disabled
(chapter 2's dry run, in writing). It
shares your home folder read-write, your
process and network namespaces, your
devices — and mounts your entire host
filesystem at `/run/host`. A program in
a box that wants your files does not
have to escape anything; it has only to
look. Rootless mode blunts none of this
for *your own* files (they were always
yours), and a `--root` box removes the
remaining distance: its processes are
root on your machine. Malware in a box
is malware on your computer, with a
different `apt`. If the software is
untrusted, the tool is a virtual
machine (chapter 12), and no flag in
chapter 5 changes that sentence.

## It cannot change the kernel

A box is a container: every box on the
machine runs the host's kernel. So a box
cannot be Windows or macOS, cannot test
a kernel module or a kernel version,
cannot run a distribution whose userland
demands a newer kernel than the host
has, and cannot natively run another
architecture's software (an ARM image on
an x86 host needs emulation, which the
manager may or may not be set up for —
distrobox's `--platform` flag asks, the
host decides). The wardrobe changes; the
body underneath is the host's.

## It has no network of its own

By default the box shares the host's
network namespace. Consequences, both
directions: a server started in a box is
listening on the *host's* ports (and
will fight the host's own services for
them), and `localhost` inside is the
host's localhost, credentials and all.
`--unshare-netns` gives a box its own
network at creation, at the cost of the
seamlessness — and firewalling a box
away from your LAN is a plain-container
job, not a distrobox one.

## The desktop integration is session-deep

Exported apps and in-box GUI programs
display through the *current* session's
sockets. Log in as a different user, run
headless, or SSH into the machine from
elsewhere, and the magic thins: windows
have nowhere to appear, audio has no
server to reach, and the classic error —
*cannot open display* — is the sound of
the session boundary, not of a broken
box. Distrobox is a tool of the logged-in
desktop and the local terminal.

## It is not a service manager (unless you ask)

Without `--init`, nothing supervises a
box's programs: no restarts, no units,
no boot order. With `--init` you get the
box's own systemd, which manages things
*inside* the box and integrates with
nothing on the host — the host's systemd
does not know your box's services exist.
A workload that must run as a real,
accountable service belongs in a unit on
the host, a plain container, or a VM.

## The two systems never truly merge

Software installs into the box's world
or the host's, never both: the host's
package manager cannot see, update or
remove the box's packages, and the box
cannot link against the host's
libraries. Exported wrappers disguise
the seam; they do not sew it shut. And
an exported command is a pointer — delete
the box carelessly and the host's
`~/.local/bin` fills with wrappers to
nowhere (removal through `distrobox rm`
cleans its own up, chapter 9; deletion
through the manager directly does not).

## Smaller print

Disk is real: each box is an image plus
every package you add, and a stable of
fat boxes is a real line in `df`. A box
pins its base release: *upgrading* a
Debian box from Bookworm to Trixie is
possible in-box, like any Debian, but
the tidy path is a new box from the new
image and your manifest (chapter 10).
And distrobox itself is a set of shell
scripts driving other people's
runtimes — when Podman or Docker change
their behaviour, distrobox inherits the
surprise first and the fix second.
