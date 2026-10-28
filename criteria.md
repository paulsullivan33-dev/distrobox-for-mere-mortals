# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   The lab's traffic is the lab talking to itself.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 13 names what the tool
   cannot do: it is not a security boundary (a box runs
   privileged and shares your home folder, your processes and
   your network, and a `--root` box is real root), it cannot
   change the kernel running underneath it, it cannot give a
   box a network or a desktop session of its own, and it
   cannot merge the host's software and a box's software into
   one system.
8. **The ethics rule is in the text, early.** Chapter 1 and the
   README state the rule plainly — *a distrobox shares your
   home folder and your account; it is a workshop, not a jail,
   and software you would not run on the host does not become
   safe inside one* — before any procedure depends on it, and
   later chapters point back to it where the tooling would
   make crossing the line easy (a `--root` box, an exported
   app with your files in reach, a stranger's assemble
   manifest applied unread).
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

The build machine is a 2-CPU, 7.7 GB Ubuntu 24.04 sandbox,
running as root. Distrobox **1.8.2.5** was taken from the
project's official release tarball on GitHub and installed
with its own `install` script, `--prefix` pointed at a
scratch folder; nothing was installed system-wide. All
transcripts live in `distrobox-lab/transcripts/`.

A container backend was stood up for real: **Docker
28.3.3** from Docker's official static binary tarball,
run with its data root and socket inside the lab's
scratch folder, the `vfs` storage driver, and bridge
networking switched off. (The Podman route was attempted
first and honestly blocked: the sandbox's apt package
lists were locked by another process for the whole
session, so the Ubuntu `podman` package could not be
installed, and the lab used Docker instead.)

Run for real, with transcripts as evidence:

- Install and version (t00); the `--help` surface of the
  main commands — create (t01), enter (t02), export
  (t13), assemble (t14), and in the lab's notes also
  upgrade, rm, stop, ephemeral, generate-entry and
  host-exec.
- `distrobox list` with no container manager installed:
  the plain "Missing dependency" error and exit 127
  (t03).
- `distrobox list` against the running backend, before
  any box existed: the empty table, exit 0 (t04).
- Configuration handling, proven by experiment: a
  `~/.config/distrobox/distrobox.conf` pinning
  `container_manager="podman"` made distrobox refuse
  the working Docker backend and report the missing
  dependency; removing the file restored the empty
  list (t05).
- `distrobox create --compatibility`: the tested image
  list, 122 entries (t10, excerpted with the count
  shown).
- The Alpine 3.20 image pulled for real, digest and
  all (t06), and `distrobox create` producing a real
  box object, which `distrobox list` then showed with
  status `Created` (t07, t08).
- `distrobox create --dry-run`: the complete container
  command distrobox generates, kept whole — the
  `--privileged`, host-namespace and home-mount lines
  that chapters 1, 2 and 13 rest on (t11).
- `distrobox assemble create --dry-run` on the
  two-box manifest kept as `examples/distrobox.ini`:
  the file parsed, one generated create command per
  box (t12).
- `distrobox rm --force` removing the box — including
  its "Removing exported binaries..." step — and the
  list returning to empty (t09). Afterwards the pulled
  image was deleted and the daemon stopped; no
  containers, images or processes were left behind.

Documented only — described from the project's
documentation, not run, and the text does not claim
otherwise:

- **Starting and entering a box, and everything
  inside one.** The sandbox's kernel policy refuses
  the `setns` system call, so no container can start
  here at all: a plain `docker run` of the pulled
  image died inside the runtime with "join container
  mntns: setns: operation not permitted" (t06), and
  `distrobox enter` failed when the daemon tried to
  start the box — "failed to set into network
  namespace ... operation not permitted" (t08). The
  failure is the sandbox's, not the tool's (the same
  refusal met the Podman guide's lab), but it is a
  complete one: the first-entry setup finishing, a
  shell inside, package installs inside, sudo inside,
  host-exec from inside, exports created from
  inside, graphical apps, an `--init` box's systemd,
  `distrobox-upgrade` and `distrobox ephemeral`
  actually running, and `distrobox-generate-entry`
  are all taught from the project's documentation.
  The commands around them that could be run —
  create, list, rm, the dry runs, the help surface —
  were run.
- Podman and Lilipod as the manager (the lab's
  backend was Docker; the manager choice is a
  configuration line, proven readable in t05, not a
  code path that changes the chapters).
- Rootful (`--root`) boxes end to end; the flag and
  its meaning are documented, and nothing in the lab
  needed root it did not already have.

Fixtures are this guide's own and deliberately
generic: a small Alpine box that was created, listed
and removed without ever starting; a Debian "writing"
box and an Alpine "small" box that exist only as a
manifest and its dry runs; package names any reader
can install. The dry-run transcripts show the lab
machine's own hostname and scratch paths, because
that is what the tool printed; no example is modelled
on any real person, network, service or organisation.
