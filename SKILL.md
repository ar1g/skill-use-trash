---
name: use-trash
description: >-
  Sets up the session to use `trash` (macOS) or `trash-put` via trash-cli
  (Linux) instead of `rm` for all file deletions — keeping files recoverable
  from the Trash rather than permanently gone. Checks if the tool is installed
  and offers to install it if not. Trigger this skill when the user says "use
  trash instead of rm", "safe delete", "don't use rm", wants to avoid accidental
  data loss, or asks about safer file deletion. Also trigger at the start of any
  session involving file cleanup or deletion where reversibility matters.
---

# Use Trash Instead of rm

Files deleted with `rm` are gone permanently — no undo, no recovery. `trash`
moves them to the macOS Trash instead, where they sit safely until you choose
to empty it. This skill sets up that safer default for the session.

## Step 1: Check if `trash` is installed

```bash
which trash 2>/dev/null
```

- **Found** — confirm it's available and move on.
- **Not found** — let the user know and offer to install:

  > "`trash` isn't installed. It's a small macOS utility that moves files to
  > the Trash instead of permanently deleting them. Want me to install it with
  > Homebrew?"

  Wait for confirmation, then run:

  ```bash
  brew install trash
  ```

  Verify with `which trash` afterward.

  **If the user declines installation** — do not delete anything. Instead, tell
  the user the exact `rm` command they can run themselves, and stop. Never run
  `rm` on the user's behalf as a fallback.

## Step 2: Use `trash` for all file deletions

For the rest of this session, substitute `trash` anywhere you'd reach for `rm`:

| Instead of | Use |
|---|---|
| `rm file.txt` | `trash file.txt` |
| `rm -rf build/` | `trash build/` |
| `rm -f config.bak` | `trash config.bak` |
| `rm *.log` | `trash *.log` |

`trash` handles files and directories alike — no `-r` flag needed. Everything
moves to the macOS Trash and can be restored from there if needed.

## Caveats

**Linux:** Use [`trash-cli`](https://github.com/andreafrancia/trash-cli) instead.
Check with `which trash-put`. If not installed, offer to install it:

```bash
# Debian/Ubuntu
sudo apt install trash-cli

# Fedora/RHEL
sudo dnf install trash-cli

# Arch
sudo pacman -S trash-cli

# Fallback
pip install trash-cli
```

Once installed, substitute `trash-put` for `rm`:
- `rm file.txt` → `trash-put file.txt`
- `rm -rf build/` → `trash-put build/`

Apply the same rule as macOS: if the user declines installation, provide the
`rm` command for them to run themselves rather than running it on their behalf.

**Headless/containers:** `trash-cli` and `trash` both require a writable home
directory and filesystem. In environments where neither is available (e.g., CI
runners, scratch containers), tell the user and let them decide how to proceed.

