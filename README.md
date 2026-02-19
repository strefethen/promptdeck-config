# PromptDeck Configs

Ready-to-use button configurations for [PromptDeck](https://github.com/strefethen/promptdeck) — the iPhone remote control for your Mac.

## Quick Start

1. Install [PromptDeckHost](https://promptdeck.app/download) on your Mac
2. Copy a config to `~/.promptdeck/buttons.yaml`:

```bash
# Use the starter config
curl -sL https://raw.githubusercontent.com/strefethen/promptdeck-config/main/starter/buttons.yaml \
  -o ~/.promptdeck/buttons.yaml
```

3. Install the PromptDeck iOS app via TestFlight
4. The host app reloads automatically when `buttons.yaml` changes

## Configs

| Config | Description |
|--------|-------------|
| [starter/buttons.yaml](starter/buttons.yaml) | Getting started — common AI coding prompts |
| [templates/developer.yaml](templates/developer.yaml) | Software development — code review, debugging, git |
| [templates/writing.yaml](templates/writing.yaml) | Writing and editing — drafts, tone, grammar |
| [templates/productivity.yaml](templates/productivity.yaml) | Productivity — app launchers, shortcuts, workflows |

## Using a Config

**One-time download:**

```bash
curl -sL https://raw.githubusercontent.com/strefethen/promptdeck-config/main/templates/developer.yaml \
  -o ~/.promptdeck/buttons.yaml
```

**Git-managed (keeps in sync):**

```bash
git clone git@github.com:strefethen/promptdeck-config.git ~/.promptdeck/repo
ln -sf ~/.promptdeck/repo/starter/buttons.yaml ~/.promptdeck/buttons.yaml
# Pull updates anytime: cd ~/.promptdeck/repo && git pull
```

## Config Format

Configs are YAML files with this structure:

```yaml
version: 1

server:
  port: 9100

grid:
  columns: 3          # buttons per row

injection:
  mode: clipboard      # "clipboard" or "keystrokes"
  plainText: true
  appendNewline: false
  cooldownMs: 250

pages:
  - label: Page Name
    type: buttons
    buttons:
      - id: unique-id
        label: Button Label
        icon: sf-symbol-name    # SF Symbols icon
        color: "#hex"
        prompt: |
          Your prompt text here.

      - group:                  # group buttons visually
          label: Group Name
          color: "#hex"
          buttons:
            - id: btn1
              label: Button 1
              icon: star
              prompt: ...
```

### Button Properties

| Property | Required | Description |
|----------|----------|-------------|
| `id` | yes | Unique identifier |
| `label` | yes | Display text |
| `icon` | yes | [SF Symbols](https://developer.apple.com/sf-symbols/) name |
| `prompt` | yes* | Text to inject (* not needed for non-inject actions) |
| `color` | no | Hex color (default: blue) |
| `animation` | no | `pulse`, `bounce`, `shake`, `glow`, `spin` |
| `repeat` | no | `once` (default) or `continuous` |
| `shape` | no | `rounded` (default), `circle`, `hexagon`, `squircle` |
| `action` | no | `inject` (default), `switchTab`, `launchApp`, `runShortcut`, `systemAction` |

### Actions

```yaml
# Default: inject prompt text at cursor
- id: review
  label: Review
  icon: eye
  prompt: Review this code...

# Open a URL/tab in the frontmost browser
- id: github
  label: GitHub
  icon: globe
  action: switchTab
  url: "https://github.com"

# Launch a Mac app
- id: terminal
  label: Terminal
  icon: terminal
  action: launchApp
  bundleId: com.apple.Terminal

# Run a Shortcuts automation
- id: focus
  label: Focus
  icon: moon
  action: runShortcut
  shortcut: "Do Not Disturb"
  target: phone    # "mac", "phone", or "both"
```

### Multiple Pages

```yaml
pages:
  - label: Coding
    type: buttons
    buttons: [...]

  - label: Writing
    type: buttons
    buttons: [...]

  - label: Scan
    type: barcode      # barcode scanner page
```

## License

MIT
