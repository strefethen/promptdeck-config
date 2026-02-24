# PromptDeck Configs

Ready-to-use button configurations for [PromptDeck](https://promptdeck-325.pages.dev/) — the iPhone remote control for your Mac.

## Installation

### Requirements

- macOS 15.5 or later (Apple Silicon)
- iOS 17.0 or later
- iPhone and Mac on the same local network, or reachable over the internet (see [Internet Connectivity & WSS/TLS](#internet-connectivity--wsstls))

### Quick Start

**1. Install from TestFlight and open the app**

**2. Visit the [download page](https://promptdeck-325.pages.dev/download) and follow the instructions.**

  - Open the downloaded DMG
  - Drag PromptDeck Host to your Applications folder
  - Launch from Applications
  - Grant Accessibility permission when prompted
  - Note the 6-digit PIN displayed in the menu bar

**3. Connect your iPhone to the host**

- Open PromptDeck on your iPhone
- Your Mac appears automatically via [Bonjour discovery](#bonjour-auto-discovery) (or tap "Enter Address Manually" for remote connections)
- Enter the PIN from your Mac's menu bar
- Start tapping buttons!

## Quick Start

1. Install [PromptDeckHost](https://promptdeck-325.pages.dev/download/PromptDeckHost.dmg) on your Mac
2. Copy a config to `~/.promptdeck/buttons.yaml`:

```bash
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

---

## Config Reference

### Minimal Config

The smallest valid config:

```yaml
version: 1

server:
  port: 9100

grid:
  columns: 3

pages:
  - label: Main
    buttons:
      - id: hello
        label: Hello
        icon: hand.wave
        prompt: Hello, world!
```

### Global Settings

```yaml
version: 1

server:
  port: 9100              # WebSocket port (must match host app)

grid:
  columns: 3              # Buttons per row (1-6)
  buttonShape: rounded    # Default shape for all buttons

injection:
  mode: clipboard          # "clipboard" (recommended) or "keystrokes"
  plainText: true          # Strip formatting when pasting
  appendNewline: false     # Press Enter after pasting
  cooldownMs: 250          # Minimum ms between button presses

ui:
  compact: false           # Smaller touch targets when true

# Optional animated background (iOS 18+, falls back to gradient on iOS 17)
background:
  style: animated          # "none", "gradient", or "animated"
```

### Pages

Pages are swipeable screens on the iPhone. Each page has a label and a list of buttons.

```yaml
pages:
  - label: Prompts
    type: buttons          # "buttons" (grid, default) or "list" (vertical list)
    sortOrder: alpha       # "alpha" = sort buttons A-Z, omit for YAML order
    chrome: true           # Show dock/tabs/spaces bar (default: true)
    buttons:
      - id: my-button
        label: My Button
        icon: star
        prompt: Do something...
```

### Page Types

```yaml
# Grid layout (default)
- label: Prompts
  type: buttons
  buttons: [...]

# Vertical list with section headers
- label: Shopping
  type: list
  buttons: [...]
```

### Groups

Groups visually cluster buttons together with a shared background tint. Buttons within a group render with connected edges.

```yaml
buttons:
  # Standalone button (no group)
  - id: solo
    label: Solo Button
    icon: star
    prompt: I stand alone.

  # Grouped buttons
  - group:
      label: "Code Review"
      color: "#3B82F6"
      sortOrder: alpha      # Sort group buttons alphabetically
      buttons:
        - id: review
          label: Review
          icon: eye
          prompt: Review this code...

        - id: explain
          label: Explain
          icon: questionmark.circle
          prompt: Explain this code...

        - id: refactor
          label: Refactor
          icon: arrow.triangle.2.circlepath
          prompt: Refactor this code...
```

Buttons inside a group inherit the group's `color` unless they specify their own.

---

## Button Types

### Inject Prompt (default)

The core action — taps inject text at the Mac's cursor position via clipboard paste.

```yaml
- id: review
  label: Review
  icon: eye
  color: "#3B82F6"
  prompt: |
    Review this code for bugs, security issues, and improvements.
    Be thorough and suggest specific fixes.
```

The `action` field defaults to `inject` — you don't need to specify it.

### Launch App

Open or switch to a macOS application by bundle ID. The button automatically displays the app's icon.

```yaml
- id: terminal
  label: Terminal
  icon: terminal
  action: launchApp
  bundleId: com.apple.Terminal

- id: xcode
  label: Xcode
  icon: hammer
  action: launchApp
  bundleId: com.apple.dt.Xcode

- id: safari
  label: Safari
  icon: safari
  action: launchApp
  bundleId: com.apple.Safari
```

> **Tip:** Find any app's bundle ID with: `osascript -e 'id of app "App Name"'`

### Switch Browser Tab

Switch to a browser tab matching a URL pattern. If no matching tab exists, opens the URL in a new tab.

```yaml
- id: github
  label: GitHub
  icon: globe
  action: switchTab
  url: "https://github.com"
  urlMatch: contains        # "contains" (default), "exact", or "startsWith"
```

The button automatically displays the site's favicon.

### Run Shortcut

Trigger an Apple Shortcuts automation on the Mac or iPhone.

```yaml
# Run on Mac
- id: dark-mode
  label: Dark Mode
  icon: moon
  action: runShortcut
  shortcut: "Toggle Dark Mode"
  target: host             # Run on Mac

# Run on iPhone
- id: focus
  label: Focus
  icon: moon.fill
  action: runShortcut
  shortcut: "Do Not Disturb"
  target: phone            # Run on iPhone (default)
```

### System Action

Trigger macOS system functions — window management, Mission Control, etc.

```yaml
- id: mission-control
  label: Spaces
  icon: rectangle.3.group
  action: systemAction
  systemAction: missionControl
```

**Available system actions:**

| Action | Description |
|--------|-------------|
| `missionControl` | Overview of all windows and spaces |
| `showDesktop` | Hide all windows, show desktop |
| `appExpose` | Show current app's windows only |
| `launchpad` | App launcher grid |
| `spotlight` | System search (Cmd+Space) |
| `lockScreen` | Lock the screen |
| `notificationCenter` | Toggle notification center |
| `minimizeWindow` | Minimize front window |
| `maximizeWindow` | Toggle fullscreen |
| `closeWindow` | Close front window |
| `nextWindow` | Cycle to next window |
| `previousWindow` | Cycle to previous window |

### Toggle (Checklist)

Buttons with persistent checked/unchecked state — useful for shopping lists, task tracking, etc. Toggles are typically created by the barcode scanner but can be defined manually.

```yaml
- id: milk
  label: Milk
  icon: bag.fill
  color: "#10B981"
  action: toggle
  checked: false

- id: eggs
  label: Eggs
  icon: bag.fill
  color: "#10B981"
  action: toggle
  checked: true            # Pre-checked
```

Use `toggleChecked` as a filter button to show only checked items:

```yaml
- id: show-checked
  label: Show Checked
  icon: line.3.horizontal.decrease.circle
  action: toggleChecked
```

### REST API

Make HTTP requests to external services. Supports secret references for API keys.

```yaml
- id: deploy
  label: Deploy
  icon: arrow.up.circle
  color: "#EF4444"
  action: restApi
  httpMethod: POST
  httpURL: "https://api.example.com/deploy"
  httpHeaders:
    Authorization: "Bearer $secret{deploy-token}"
    Content-Type: "application/json"
  httpBody: '{"environment": "production"}'
  timeoutSeconds: 30       # Default 30s, max 300s
```

> **Secrets:** Use `$secret{name}` in URLs, headers, or body. Manage secrets via the host app's menu bar (Cmd+Shift+S).

### Barcode Scanner

Opens the iPhone camera for scanning product barcodes (EAN-8, EAN-13, UPC-E, Code 128). Scanned products are automatically added as toggle buttons.

```yaml
- id: scan
  label: Scan Item
  icon: barcode.viewfinder
  action: scanBarcode
```

When you scan a barcode:

1. The host looks up the product name via [UPCitemdb](https://www.upcitemdb.com/)
2. A **toggle button** is created on the same page with the product name as its label
3. Scanning the same barcode again **toggles** the checked state (useful for shopping lists)
4. If the product isn't found, the raw barcode number is used as the label

**Product lookup works out of the box** — the trial API (100 requests/day, no key required) is used by default. For higher limits, add a [UPCitemdb API key](https://www.upcitemdb.com/wp/docs/main):

1. In PromptDeckHost menu bar → **Manage Secrets** (Cmd+Shift+S)
2. Add a secret named `upcitemdb-api-key`

### Forget PIN

Client-side action that clears the saved connection PIN and disconnects.

```yaml
- id: forget
  label: Forget PIN
  icon: key.slash
  color: "#EF4444"
  action: forgetPin
```

---

## Button Appearance

### Colors

Any hex color. Buttons in a group inherit the group color unless overridden.

```yaml
- id: danger
  label: Delete
  icon: trash
  color: "#EF4444"
  prompt: ...
```

### Shapes

Control individual button shapes, or set a global default in `grid.buttonShape`.

```yaml
# Per-button shape
- id: mic
  label: Record
  icon: mic
  shape: circle            # Icon only, no label

# Global default
grid:
  columns: 3
  buttonShape: squircle    # All buttons use this shape unless overridden
```

| Shape | Description |
|-------|-------------|
| `rounded` | Default, slight corner radius |
| `square` | Sharp corners |
| `pill` | Fully rounded ends |
| `circle` | 1:1 ratio, icon only |
| `hexagon` | 1:1 ratio, icon only |
| `octagon` | 1:1 ratio, icon only |
| `squircle` | 1:1 ratio, iOS app icon curve |

> Shapes with 1:1 ratio (`circle`, `hexagon`, `octagon`, `squircle`) hide the label and show only the icon.

### Animations

Add motion to buttons for visual emphasis.

```yaml
- id: alert
  label: Alert
  icon: exclamationmark.triangle
  animation: pulse
  repeat: continuous       # Keeps animating

- id: confirm
  label: Confirm
  icon: checkmark
  animation: bounce
  repeat: once             # Animates once per tap (default)
```

| Animation | Description |
|-----------|-------------|
| `pulse` | Scale up and down |
| `variableColor` | Color intensity shift |
| `wiggle` | Side-to-side shake (iOS 18+) |
| `breathe` | Slow fade in/out (iOS 18+) |
| `rotate` | Spin (iOS 18+) |

---

## Chrome Sections

Optional UI sections that appear above your buttons, showing live state from your Mac.

### Dock

Shows running Mac apps as tappable icons. Tap to switch to that app.

```yaml
dock:
  position: top            # "top" or "bottom"
  showLabels: false        # Show app names under icons
```

### Browser Tabs

Shows open browser tabs as tappable chips. Each browser gets its own labeled row. Only browsers you list will appear.

```yaml
# Both browsers, separate rows
tabs:
  safari:
    fullTitles: false
  chrome:
    fullTitles: true

# Safari only
tabs:
  safari: {}
```

### macOS Spaces

Shows virtual desktop spaces. Tap to switch spaces.

```yaml
spaces: {}                 # Empty object enables the feature
```

### VS Code Windows

Shows open VS Code project windows. Tap to switch.

```yaml
vscode: {}                 # Empty object enables the feature
```

> Set any chrome section to enable it. Omit it to disable. Use `chrome: false` on a page to hide all chrome sections on that page.

---

## Full Example

A complete config combining multiple features:

```yaml
version: 1

server:
  port: 9100

grid:
  columns: 3

injection:
  mode: clipboard
  plainText: true
  appendNewline: false
  cooldownMs: 250

dock:
  position: top
  showLabels: false

spaces: {}

pages:
  - label: Code
    buttons:
      - group:
          label: "AI Prompts"
          color: "#3B82F6"
          buttons:
            - id: review
              label: Review
              icon: eye
              prompt: |
                Review this code for bugs and improvements.

            - id: tests
              label: Tests
              icon: checkmark.circle
              prompt: |
                Write comprehensive tests for this code.

            - id: explain
              label: Explain
              icon: questionmark.circle
              prompt: |
                Explain what this code does step by step.

      - group:
          label: "Tools"
          color: "#10B981"
          buttons:
            - id: terminal
              label: Terminal
              icon: terminal
              action: launchApp
              bundleId: com.apple.Terminal

            - id: github
              label: GitHub
              icon: globe
              action: switchTab
              url: "https://github.com"

            - id: mission-control
              label: Spaces
              icon: rectangle.3.group
              action: systemAction
              systemAction: missionControl

  - label: Scan
    type: buttons
    buttons:
      - id: scan
        label: Scan Item
        icon: barcode.viewfinder
        action: scanBarcode
```

## Prompts
Check out [jeffreysprompts.com](https://jeffreysprompts.com) for great LLM prompts that will really get you thinking.

## License

MIT
