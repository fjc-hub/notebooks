`tmux` (terminal multiplexer) lets you switch between multiple programs in one terminal, create split panes, and keep sessions running in the background even if you disconnect.

---

### Core Concept: The Prefix Key

Most `tmux` shortcuts require pressing the **Prefix key** first, followed by a command key.

> **Default Prefix:** `Ctrl + b`
> *(Press and release `Ctrl + b`, then press the shortcut key).*

---

### 1. Essential Terminal Commands

Run these directly in your Linux terminal (outside of tmux):

| Action | Command |
| --- | --- |
| **Start a new session** | `tmux` |
| **Start a named session** | `tmux new -s my-session` |
| **List running sessions** | `tmux ls` |
| **Attach to last session** | `tmux a` (or `tmux attach`) |
| **Attach to specific session** | `tmux a -t my-session` |
| **Kill a session** | `tmux kill-session -t my-session` |

---

### 2. Key Shortcuts (Inside tmux)

All shortcuts below assume you press `Ctrl + b` **first**.

#### Session Management

* `Ctrl + b`, then `d` — **Detach** from session (keeps processes running in background)
* `Ctrl + b`, then `$` — **Rename** current session

#### Panes (Splitting the screen)

* `Ctrl + b`, then `%` — Split screen **vertically** (left / right)
* `Ctrl + b`, then `"` — Split screen **horizontally** (top / bottom)
* `Ctrl + b`, then `Arrow Key` — Move focus to adjacent pane
* `Ctrl + b`, then `z` — **Toggle fullscreen** (zoom) for current pane
* `Ctrl + b`, then `x` — Close current pane (or press `Ctrl + d`)

#### Windows (Tabs)

* `Ctrl + b`, then `c` — Create a **new window** (tab)
* `Ctrl + b`, then `n` — Go to **next** window
* `Ctrl + b`, then `p` — Go to **previous** window
* `Ctrl + b`, then `0`..`9` — Switch directly to window by number
* `Ctrl + b`, then `,` — **Rename** current window

---

### 3. Basic Workflow Walkthrough

1. Start a named session:
```bash
tmux new -s work

```


2. Split the window vertically with `Ctrl + b`, then `%`.
3. Switch back to the left pane using `Ctrl + b`, then `Left Arrow`.
4. Detach from the session to leave it running: press `Ctrl + b`, then `d`.
5. Re-attach whenever you are ready:
```bash
tmux attach -t work

```