# WebMCP Smart Home

An interactive demo showcasing [Chrome's WebMCP Early Preview](https://developer.chrome.com/blog/webmcp-epp) — a proposed web standard that lets websites expose structured tools for AI agents.

Instead of screen-scraping and guessing which button to click, agents call typed functions directly and get structured responses back.

## What it does

A single-file smart home dashboard that registers WebMCP tools via both APIs:

**Imperative tools** (JavaScript `navigator.modelContext.registerTool`):
- `set_lights` — control room lighting (brightness, color)
- `adjust_thermostat` — set temperature and HVAC mode
- `play_music` — play, pause, skip, set volume/genre
- `lock_doors` — lock or unlock front, back, garage
- `set_scene` — composable presets (good_morning, movie_night, party, bedtime)

**Declarative tool** (HTML form attributes `toolname`, `tooldescription`):
- `send_intercom_message` — broadcast a message to a room's speaker

The **WebMCP Protocol Inspector** sidebar visualizes the full lifecycle in real-time: tool registration → agent discovery → structured request → response.

## Requirements

- **Chrome 146+** (Canary) — version 146.0.7672.0 or higher
- **WebMCP flag enabled** — navigate to `chrome://flags/#enable-webmcp-testing` and set to Enabled, then relaunch

## Setup

### 1. Launch Chrome Canary with remote debugging

```bash
'/Applications/Google Chrome Canary.app/Contents/MacOS/Google Chrome Canary' \
  --remote-debugging-port=9333 \
  --user-data-dir=/tmp/chrome-canary-webmcp \
  --enable-features=WebMCPTesting
```

### 2. Open the demo

Navigate to `webmcp-smart-home.html` in Chrome Canary. The badge in the top-right should show **WebMCP Active**.

### 3. Connect an agent

We used [Claude Code](https://docs.anthropic.com/en/docs/claude-code) with the [agent-browser](https://github.com/AgenTool-AI/agent-browser) skill to connect via CDP and make tool calls:

```bash
agent-browser connect 9333
agent-browser open file:///path/to/webmcp-smart-home.html
```

Then tools can be called directly through the WebMCP testing API:

```javascript
navigator.modelContextTesting.executeTool('set_lights', JSON.stringify({
  room: 'living_room',
  brightness: 50,
  color: '#ff6b35'
}))
```

Or with natural language through Claude Code — tell it "set the scene for a romantic evening" and watch the Protocol Inspector light up with structured tool calls.

### 4. Send to Claude (optional)

The "Send to Claude" button uses [playground-sync](https://github.com/stevysmith/playground-sync), a Claude Code plugin that bridges browser playgrounds to Claude via a local HTTP server. Install it as a Claude Code plugin to enable the button.

## How it was built

This demo was built in a single session using Claude Code with:

- **[agent-browser](https://github.com/AgenTool-AI/agent-browser)** skill — to launch Chrome Canary, connect via CDP, and make WebMCP tool calls
- **[playground-sync](https://github.com/stevysmith/playground-sync)** plugin — for the "Send to Claude" bridge between browser and CLI

The entire playground is a single HTML file with no external dependencies.

## Resources

- [WebMCP Early Preview — Chrome for Developers](https://developer.chrome.com/blog/webmcp-epp)
- [WebMCP spec on GitHub](https://github.com/webmachinelearning/webmcp)
- [Model Context Tool Inspector Extension](https://chromewebstore.google.com/detail/model-context-tool-inspec/gbpdfapgefenggkahomfgkhfehlcenpd)
- [Google's flight search demo (imperative)](https://googlechromelabs.github.io/webmcp-tools/demos/react-flightsearch/)
- [Google's Le Petit Bistro demo (declarative)](https://googlechromelabs.github.io/webmcp-tools/demos/french-bistro/)
