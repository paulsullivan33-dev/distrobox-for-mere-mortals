# Troubleshooting

Outcome: the failure in front of you has
a name, a likely layer (distrobox, the
manager, the box, or the machine), and a
next command. Work from the symptom.

## "Missing dependency: we need a container manager"

Distrobox found no Podman, Docker or
Lilipod it can use — the lab's very
first transcript (`t03-list-no-backend.txt`).
Three causes, in order: no manager is
installed (install one, chapter 3); a
manager exists but a **config pin** names
a different, absent one — the lab proved
this by pinning `container_manager="podman"`
on a Docker-only machine and watching
the error appear (`t05-config-manager.txt`),
so read `~/.config/distrobox/distrobox.conf`
before reinstalling anything; or you are
running distrobox from a context whose
`PATH` lacks the manager (a root shell
via bare `sudo` is the classic — use
`--root`, not `sudo distrobox`).

## Entering fails with namespace or permission errors

The box exists but will not start. The
lab's kept specimen
(`t08-enter-attempt.txt`):

```text
$ distrobox enter alpinebox -- cat /etc/os-release
Error response from daemon: failed to create default sandbox: failed to create a netlink handle: failed to set into network namespace 25 while creating netlink socket: operation not permitted
```

Ask the manager to run *anything* —
`podman run --rm alpine:3.20 cat /etc/os-release`
(or the Docker spelling). If that fails
too — in the lab, plain `docker run`
died inside the runtime with its own
`setns: operation not permitted`
(`t06-pull-and-run-attempt.txt`) — the
machine itself refuses the namespace
operations containers need. That is the
signature of a restricted sandbox: CI
runners, nested containers, locked-down
VMs. It is the environment's policy, not
a distrobox fault, and no distrobox flag
negotiates with it. On a normal desktop
install the same error more often means
a half-configured rootless setup —
reinstall the manager's package and let
it recreate its user configuration.

## First entry stalls or rains package errors

First entry is the box installing its
base packages (chapter 2). If it crawls,
it is usually just slow mirrors; if it
*fails*, read the last package-manager
error, not the distrobox wrapper around
it — a box behind a proxy needs the
proxy variables inside it too, and an
image can be too minimal for its own
good (the compatibility list exists
because arbitrary images fail here).
Re-entering resumes; a box whose setup
truly wedged is cheapest deleted and
recreated.

## The prompt is not your shell, or the box feels bare

Your shell exists in the box only if the
box's distribution installed it: a
minimal image's idea of a shell is `sh`.
Install your shell with the box's
package manager and re-enter. The same
goes for missing basics (a minimal image
may lack `sudo`, `curl`, your editor) —
the box is a real system, with a real,
possibly tiny, starting set.

## A graphical program says it cannot open the display

The display reaches the box through the
session's sockets (chapter 13). Check
that you are in the same graphical
session you entered from: not SSH'd in,
not a different user, not a bare virtual
console. Inside the box, `echo $DISPLAY`
(or `$WAYLAND_DISPLAY`) should be set;
if it is empty, you entered from outside
the session, and entering again from a
terminal *in* the session is the fix.

## The exported command runs the wrong version

You exported a box's tool, but the host
answers with its own. Check the order of
your host `PATH` (`which pandoc` settles
it): the wrapper lives in `~/.local/bin`,
which must come before the system paths.
If `which` finds no wrapper at all, list
what the box thinks it exported
(`distrobox-export --list-binaries` from
inside) and re-export — a wrapper
deleted by hand, or a box removed
through the manager rather than
`distrobox rm`, leaves exactly this
shape of confusion.

## Sudo inside the box wants a password you never set

The password is the box's own, chosen at
first setup (chapter 6) — your host
password was never copied in. Locked
out, enter the box *as its root user*
through the manager
(`podman exec -u root -it <box> bash`, or
Docker's equivalent) and set the user's
password with `passwd`, then leave and
be yourself again.

## A box will not stop or will not delete

Ask in escalating order: `distrobox stop
<name>`; `distrobox rm --force <name>`;
then the manager directly
(`podman stop`, `podman rm -f`), since a
box is a container and the manager's
word is final. Afterwards, if the box
was removed behind distrobox's back,
look for its orphaned exports in
`~/.local/bin` and
`~/.local/share/applications` and delete
those by hand — the one mess
`distrobox rm` would have cleaned for
you.
