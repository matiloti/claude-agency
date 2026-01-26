# iOS Simulator Skill (XcodeBuildMCP)

This skill enables interaction with iOS Simulators for building, running, and testing React Native apps.

## Available Tools

### Simulator Management

| Tool | Purpose |
|------|---------|
| `mcp__xcodebuildmcp__list_simulators` | List all available iOS simulators |
| `mcp__xcodebuildmcp__boot_simulator` | Boot a simulator by UUID |
| `mcp__xcodebuildmcp__open_simulator` | Open and focus a running simulator |

### Build & Run

| Tool | Purpose |
|------|---------|
| `mcp__xcodebuildmcp__build_sim_name_proj` | Build for simulator |
| `mcp__xcodebuildmcp__install_app` | Install app on simulator |
| `mcp__xcodebuildmcp__launch_app` | Launch app by bundle ID |
| `mcp__xcodebuildmcp__stop_app` | Stop a running app |

### UI Interaction (requires AXe)

| Tool | Purpose |
|------|---------|
| `mcp__xcodebuildmcp__ui_tap` | Tap on screen coordinates or element |
| `mcp__xcodebuildmcp__ui_swipe` | Swipe gestures |
| `mcp__xcodebuildmcp__describe_ui` | Get accessibility tree of current screen |

### Capture & Debug

| Tool | Purpose |
|------|---------|
| `mcp__xcodebuildmcp__screenshot` | Capture simulator screenshot |
| `mcp__xcodebuildmcp__record_video` | Record simulator video |
| `mcp__xcodebuildmcp__capture_logs` | Capture runtime logs |

## Workflows

### Build and Run App

1. List available simulators
2. Boot desired simulator (e.g., iPhone 15 Pro)
3. Build the app for simulator
4. Install the app
5. Launch the app

### Test UI Flow

1. Launch the app
2. Take screenshot to see current state
3. Use `describe_ui` to get element information
4. Tap on elements to navigate
5. Take screenshots to verify results

### Debug Issues

1. Launch the app
2. Capture logs to see errors
3. Take screenshot if UI issue
4. Use `describe_ui` to inspect element states

## Common Commands

### Quick Launch
```
Build and run my app on iPhone 15 Pro simulator
```

### Screenshot Verification
```
Take a screenshot of the simulator and describe what you see
```

### UI Interaction
```
Tap the "Login" button on the screen
```

### Navigation Testing
```
Navigate to the Settings screen and take a screenshot
```

### Debug Mode
```
Show me the simulator logs and identify any errors
```

## Tips

- Always boot the simulator before trying to install/launch
- Use `describe_ui` before tapping to find correct element coordinates
- Take screenshots after each action to verify state
- Check logs when app crashes or behaves unexpectedly
- Use specific simulator names (e.g., "iPhone 15 Pro") not just "iPhone"

## Prerequisites

- Xcode installed and configured
- XcodeBuildMCP server added: `claude mcp add XcodeBuildMCP -- npx -y xcodebuildmcp@latest`
- AXe installed for UI automation: `brew install cameroncooke/axe/axe`
