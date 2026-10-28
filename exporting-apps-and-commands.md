# Exporting Apps and Commands

Outcome: a program that lives in a box
appears on your host as if it were
installed there — in your application
menu, or as a command in `~/.local/bin` —
while it still runs, entirely, inside
the box.

Exporting is done from *inside* the box,
with `distrobox-export`. The lab could
not start a box, so this chapter is
taught from the project's documentation;
the command's real help text, captured
live, is transcript `t13-export-help.txt`,
and the removal behaviour at the end was
run for real.

## Exporting a command

Enter the box, then name the program by
its full path inside the box:

```bash
distrobox enter writing
distrobox-export --bin /usr/bin/pandoc
```

This writes a small wrapper script named
`pandoc` into `~/.local/bin` on the host
(make sure that folder is in your host
`PATH` — most distributions put it
there). From then on, on the *host*:

```bash
pandoc --version
```

runs pandoc inside the `writing` box —
starting the box first if it was stopped
— with the box's libraries and the box's
version. The wrapper is plain text; read
it once and the trick is demystified: it
is `distrobox enter writing -- pandoc`
with your arguments forwarded. Options
worth knowing: `--export-path` chooses a
different wrapper folder, `--extra-flags`
adds flags the exported command always
receives, and `--enter-flags` adjusts the
enter call itself.

## Exporting a graphical app

For a desktop application, export by its
name (or its `.desktop` file):

```bash
distrobox-export --app gimp
```

Distrobox copies the app's desktop file
and icon into your host's
`~/.local/share/applications`, rewritten
to launch through the box, and labelled
with the box's name — the menu entry
reads like *GIMP (on writing)*, so two
boxes exporting the same app stay
distinguishable (the label is adjustable:
`--export-label none` turns it off). The
app then appears in your normal
application menu and, when launched,
displays on your desktop like a local
program — while every byte of it runs in
the box. This is the feature that earns
distrobox its keep on a desktop: another
distribution's version of an app, or an
app your distribution does not package
at all, as a first-class citizen.

Graphical exporting leans on the session
sharing of chapter 6 — same display,
same audio — and inherits its limits
(chapter 13): it is a same-machine,
same-session trick.

## The box itself in the menu

`distrobox-generate-entry <boxname>` adds
the *box* to your application list: an
entry that opens a terminal already
entered into the box. On creation,
distrobox does this automatically unless
told `--no-entry`; it is the front door
for people who live in one box most of
the day.

## Taking exports back

Exports are bookkeeping, and they unwind
cleanly:

```bash
distrobox-export --app gimp --delete
distrobox-export --bin /usr/bin/pandoc --delete
distrobox-export --list-apps        # what this box has exported
distrobox-export --list-binaries
```

And removing the whole box removes its
exports as part of the job — the lab ran
this for real (`t09-rm.txt`); watch the
middle line:

```text
$ distrobox rm --force alpinebox
Removing container...
alpinebox
Removing exported binaries...
```

Check: export one command from a box and
run it on the host with a version flag.
The version it reports is the box's
package's version, which you can confirm
against the box's package manager
(`distrobox enter writing -- apt list
--installed | grep pandoc`). Two views of
one truth — that is an export working.
