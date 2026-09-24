# ops

One entry point for the tools in this lab. `ops <command>` dispatches to a
script in `libexec/`, passing everything after the command straight through.

The point is discoverability: `ops help` lists what exists, so you do not have
to remember whether a tool was called `ssht-app.sh` or something else.

## Install

    git clone <this repo> ~/ops
    chmod +x ~/ops/ops
    ln -sf ~/ops/ops ~/bin/ops
    hash -r

`~/bin` is on the PATH by default on Ubuntu, but only if it existed when your
shell started. If `ops` is not found, open a new terminal.

`hash -r` clears bash's memory of where commands live. Needed whenever you add
something to a directory already on the PATH.

## Usage

    ops help                 list commands
    ops <command> -h         options for one command
    ops ports
    ops net -q
    ops connect app

## Commands

| Command      | Does                                              |
|--------------|---------------------------------------------------|
| `health`     | uptime, disk, memory, containers; local or by ssh |
| `net`        | host:port reachability from a list                |
| `ports`      | listening TCP ports and the owning process        |
| `connect`    | ssh by partial hostname match                     |
| `clean`      | remove exited containers and dangling images      |
| `gcp`        | start, stop or query a GCE instance               |
| `gcp-status` | list GCE instances                                |
| `ipwatch`    | report local IP, warn on change                   |

## Adding a tool

1. Put the script in `libexec/` and `chmod +x` it
2. Add a case to the dispatch in `ops`
3. Add a line to the usage block

The scripts in `libexec/` are copies, not links, so this repo works on its own
after a clone. When you change the original in `~/scripts` or `~/python`, copy
it across deliberately — that copy is the moment you decide the change is good
enough to ship.

## Notes

`exec` is used for dispatch, so the chosen script replaces this one rather than
running as a child. Signals and exit codes pass through unchanged.

`LIBEXEC` is derived with `readlink -f "$0"`, not `$0` alone, because `ops` is
reached through a symlink in `~/bin`. Without resolving the link it would look
for `~/bin/libexec`.
