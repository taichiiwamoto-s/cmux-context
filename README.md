# cmux-context

A terminal dashboard that visualizes Claude Code context usage across all [cmux](https://cmux.co) workspaces.

![Screenshot](screenshot.png)

## Features

- Shows context usage (%) for every active Claude Code workspace
- Color-coded progress bars (green < 50%, yellow 50-80%, red >= 80%)
- Displays model name (Opus / Sonnet / Haiku) and line counts
- Shows shared rate limits (5h / 7d)

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

```bash
cmux-context
```

### Example Output

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
4. Renders a color-coded progress bar per workspace

## License

[MIT](LICENSE)
