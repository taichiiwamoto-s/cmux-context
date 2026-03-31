# cmux-context

A terminal dashboard that visualizes Claude Code context usage across all [cmux](https://cmux.co) workspaces — with optional sidebar integration.

![Screenshot](screenshot.png)

## Features

- Shows context usage (%) for every active Claude Code workspace
- Color-coded progress bars (green < 50%, yellow 50-80%, red >= 80%)
- Displays model name (Opus / Sonnet / Haiku) and line counts
- Shows shared rate limits (5h / 7d)
- **Sidebar mode**: continuously updates each workspace's sidebar with context usage (status pill + progress bar)

## Requirements

- [cmux](https://cmux.co) (macOS)
- Bash 4+
- Claude Code status line enabled in each workspace

## Installation

### Manual

```bash
curl -fsSL https://raw.githubusercontent.com/iwamototaichi/cmux-context/main/cmux-context -o /usr/local/bin/cmux-context
chmod +x /usr/local/bin/cmux-context
```

Or clone and symlink:

```bash
git clone https://github.com/iwamototaichi/cmux-context.git
ln -s "$(pwd)/cmux-context/cmux-context" /usr/local/bin/cmux-context
```

## Usage

### One-shot (terminal output)

```bash
cmux-context
```

### Sidebar daemon

```bash
cmux-context start              # start (10s interval, default)
cmux-context start --interval 5 # start with 5s interval
cmux-context status             # check if daemon is running
cmux-context stop               # stop daemon, clear sidebar
```

The `start` command creates a dedicated cmux workspace (`ctx-monitor`) that runs a background update loop. Each workspace's sidebar displays:

- **Status pill**: `🧠 XX%` with color (green/yellow/red)
- **Progress bar**: visual bar with "Context XX%" label

The `stop` command kills the daemon, removes all sidebar indicators, and closes the workspace.

### Example Output (one-shot)

```
  cmux Context Monitor
  ────────────────────────────────────────────────────────

  Claude Code
  ████████░░░░░░░░░░░░  38%  Opus 4.6  +12/-3

  VI分析ツール
  ████████████████░░░░  78%  Sonnet 4.6  +5/-1

  ────────────────────────────────────────────────────────
  Rate Limits (shared)
  5h ▰▰▰▱▱▱▱▱▱▱
  7d ▰▱▱▱▱▱▱▱▱▱
```

## How It Works

1. Queries `cmux list-workspaces` to discover all workspaces
2. Reads the screen of each workspace via `cmux read-screen`
3. Parses the Claude Code status line for context percentage, model, and line info
4. **One-shot**: Renders a color-coded progress bar per workspace in the terminal
5. **Daemon**: Updates each workspace's sidebar via `cmux set-status` and `cmux set-progress`

### Color Thresholds

| Usage   | Color   | Hex       |
|---------|---------|-----------|
| < 50%   | Green   | `#97C9C3` |
| 50-79%  | Yellow  | `#E5C07B` |
| >= 80%  | Red     | `#E06C75` |

## License

[MIT](LICENSE)
