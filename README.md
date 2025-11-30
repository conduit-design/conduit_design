# conduit.design

![macOS Intel](https://img.shields.io/badge/macOS-Apple%20Silicon_/_Intel-000000?logo=apple&logoColor=white) ![Linux x64 / ARM](https://img.shields.io/badge/Linux-x64_/_ARM64-FCC624?logo=linux&logoColor=black) ![Windows ARM](https://img.shields.io/badge/Windows-X64_/_ARM64-0078D6?logo=windows&logoColor=white) 

[![Downloads (7d)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/conduit-design/conduit_design/main/meta/github-release-downloads-weekly.json?v=1)](https://github.com/conduit-design/conduit_design/releases)
[![Downloads (30d)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/conduit-design/conduit_design/main/meta/github-release-downloads-30d.json?v=1)](https://github.com/conduit-design/conduit_design/releases)
[![Total downloads](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/conduit-design/conduit_design/main/meta/github-release-downloads.json)](https://github.com/conduit-design/conduit_design/releases) 
[![Latest release](https://img.shields.io/github/v/release/conduit-design/conduit_design?sort=semver)](https://github.com/conduit-design/conduit_design/releases)


Turn any LLM-powered agent into a first-class Figma collaborator. No more clicking through menus or manual adjustments. Just describe what you want, and watch it happen. 

**[Watch the Demo →](https://www.linkedin.com/posts/andr%C3%A9-j-1676a2169_launch-video-the-landing-page-goes-live-activity-7390030243383730176-WNnL)**
## Features

- **Full Figma API access** — Batch transform complex designs with simple prompts  
- **Instant AI editing** — Transform complex design hierarchies in seconds
- **Code Export** — Generate production-ready code from design instantly  

## Prerequisites

Before installation, ensure you have:

- **Figma Desktop App** (Free version or Paid, browser version of Figma not supported) 
- **MCP-compatible application** (Cursor, VSCode, AntiGravity, or another MCP-enabled editor.)

## Quick Start

### Step 1: Configure Your MCP Application

Add to your MCP configuration file (`~/.cursor/mcp_settings.json` for Cursor, `.vscode/mcp.json` for VSCode, or platform-specific location for others):   

```json
{
  "mcpServers": {
    "conduit": {
      "command": "/bin/bash",
      "args": ["-c", "curl -sSL https://conduit.design/install.sh | bash -s -- --run"]
    }
  }
}
```

### Step 2: Install and Import the Figma Plugin

1. **Start your MCP application** (e.g., Cursor or VSCode) — this automatically downloads and unpacks the Conduit Figma plugin to `~/.conduit/figma-plugin/`
2. **Open Figma Desktop** → Navigate to `Plugins` → `Development` → `Import plugin from manifest…`
3. **Select the manifest file**: `~/.conduit/figma-plugin/manifest.json`
4. **Copy the channel ID** displayed in the Conduit plugin UI (e.g., `"purple-owl-27"`)

### Step 3: Connect & Create

In your AI assistant (e.g., Cursor chat):

```text
User: Talk to Figma on channel "purple-owl-27"
Agent: ✅ Connected to Figma on channel "purple-owl-27"

User: Create a modern navigation bar with our brand color #3366FF
Agent: ✅ Created navigation bar with brand color #3366FF
```

You're good to go! The AI can now read, modify, and create Figma designs.

## Configuration

> [!WARNING]
> **Security Notice**: Conduit requires file system access. Only add trusted directories to `ALLOWED_ROOTS`. Always back up your projects before granting write permissions.

### Basic Configuration

To customize Conduit's behavior, add environment variables to your MCP configuration:

```json
{
  "mcpServers": {
    "conduit": {
      "command": "/bin/bash",
      "args": [
        "-c",
        "curl -sSL https://conduit.design/install.sh | bash -s -- --run"
      ],
      "env": {
        "CHANNEL_KEY": "purple-owl-26",
        "PORT": "3055",
        "PROJECT_ROOT": "/Users/John/my-project",
        "ALLOWED_ROOTS": "/Users/John/Desktop/experiments|/Users/John/Downloads/project-xyz"
      }
    }
  }
}
```

### Environment Variables Reference

| Variable | Description | Default | Example |
|----------|-------------|---------|---------|
| `CHANNEL_KEY` | Permanent channel ID to pair the MCP server with the Figma plugin. Copy from the Conduit plugin UI. | Auto-generated | `purple-owl-26` |
| `PORT` | WebSocket port the plugin connects to. | `3055` | `3055` |
| `PROJECT_ROOT` | Directory for Conduit's logs and temp files. Many MCP hosts default this to your workspace; some require explicit configuration. | Workspace root | `/Users/John/my-project` |
| `ALLOWED_ROOTS` | Pipe-delimited (`|`) list of absolute directories where Conduit can overwrite existing files. Be cautious with broad paths like `~/` or `/`. | None | `/Users/John/my-project\|/Users/John/experiments` |

**Important Notes:**
- `PROJECT_ROOT` does **not** limit where `ALLOWED_ROOTS` can point—they are independent.
- Only directories listed in `ALLOWED_ROOTS` allow file overwrites. All other locations are read-only or create-only.

### Windows Configuration

On Windows, use Windows-style paths and PowerShell syntax. Example:

```json
{
  "mcpServers": {
    "conduit": {
      "command": "powershell.exe",
      "args": [
        "-NoProfile",
        "-Command",
        "iwr https://conduit.design/install.ps1 -UseBasicParsing | iex"
      ],
      "env": {
        "CHANNEL_KEY": "purple-owl-26",
        "PORT": "3055",
        "PROJECT_ROOT": "C:\\Users\\John\\my-project",
        "ALLOWED_ROOTS": "C:\\Users\\John\\my-project|C:\\Users\\John\\Desktop\\experiments"
      }
    }
  }
}
```

> [!NOTE]
> Use double backslashes (`\\`) in JSON for Windows paths. At runtime, these become standard Windows paths like `C:\Users\John\...`.

### Instant AI edit Integration (Optional)

To enable AI-powered instant edits, add your provider API keys and model preferences:

```json
{
  "mcpServers": {
    "conduit": {
      "command": "/bin/bash",
      "args": ["-c", "curl -sSL https://conduit.design/install.sh | bash -s -- --run"],
      "env": {
        "CHANNEL_KEY": "purple-owl-26",
        "PORT": "3055",
        "PROJECT_ROOT": "/Users/John/my-project",
        "ALLOWED_ROOTS": "/Users/John/my-project",
        "AI_DEFAULT_PROVIDER": "openai",
        "AI_DEFAULT_MODEL": "gpt-5-1",
        "OPENAI_API_KEY": "sk-proj-...",
        "AI_TEMPERATURE": "0.7",
        "AI_MAX_TOKENS": "4096"
      }
    }
  }
}
```

#### AI Provider Options

| Provider | Supported Models | Environment Key |
|----------|-----------------|-----------------|
| **OpenAI** | `gpt-5-1`, `gpt-5-1-mini` | `OPENAI_API_KEY` |
| **Anthropic** | `claude-sonnet-4-5`, `claude-opus-4-5` | `ANTHROPIC_API_KEY` |
| **Google** | `gemini-3.0-pro`, `gemini-3.0-flash` | `GEMINI_API_KEY` |

#### AI-Specific Environment Variables

| Variable | Description | Default | Example |
|----------|-------------|---------|---------|
| `AI_DEFAULT_PROVIDER` | AI provider to use for instant edits. | None | `openai` |
| `AI_DEFAULT_MODEL` | Model to use for instant edits. | None | `gpt-5-1` |
| `AI_TEMPERATURE` | Creativity/randomness (0.0-1.0). | `0.7` | `0.7` |
| `AI_MAX_TOKENS` | Maximum tokens for AI responses. | `4096` | `4096` |

## Export Capabilities

Conduit can export production-ready websites from Figma frames using the `{page-breakpoint-theme}` naming convention:

### Export Features
- **Responsive Design** — Automatic mobile, tablet, and desktop breakpoints  
- **Theming** — Dark and light mode support  
- **Asset Export** — Optimized SVG and image exports  
- **CSS Tokens** — Design system variables extracted automatically  
- **Complete Files** — HTML, CSS, and all assets bundled together  
- **Google Fonts** — Automatic imports and optimization  
- **Multi-Page** — Export entire websites with multiple pages  
- **Hyperlinks** — Navigation and interaction support

### Export Workflow
1. Name your Figma frames using the pattern: `{page-breakpoint-theme}`
   - Example: `home-desktop-light`, `about-mobile-dark`
2. Use the `export` tool via your AI assistant
3. Conduit generates complete HTML/CSS/JSX with all assets

## Supported Applications

| Application | Configuration Location | Status |
|-------------|----------------------|--------|
| [Cursor](https://cursor.com) | `~/.cursor/mcp_settings.json` | ✅ Stable |
| [Google Antigravity](https://antigravity.google.com/) | Extension settings | ✅ Stable |
| [VSCode](https://code.visualstudio.com) | `.vscode/mcp.json` | ✅ Stable |

## Supported Platforms

Conduit supports the following operating systems with one-line installers and prebuilt binaries:

- **macOS (Apple Silicon / ARM64)**
- **macOS (Intel / x64)**
- **Linux (x64)**
- **Linux (ARM64)**
- **Windows (x64)**
- **Windows (ARM64)** 

## Tool API

Conduit exposes the following tools to LLM agents:

### Design Operation Tools

Most tools have read/write and single/batch mode build in.

| Tool | Description |
|------|-------------|
| `annotation` | Read and write Figma annotations and comments. |
| `asset` | Get and set assets (images, icons) within Figma files. |
| `autolayout` | Configure Auto Layout properties on frames and components. |
| `boolean` | Perform boolean operations (union, subtract, intersect) on shapes. |
| `component` | Create, modify, and manage Figma components and instances. |
| `constraint` | Set layout constraints for responsive design. |
| `effect` | Apply visual effects (shadows, blurs) to nodes. |
| `grid` | Configure layout grids and guides. |
| `guide` | Manage ruler guides on frames and pages. |
| `group` | Group and ungroup nodes. |
| `node` | Core node operations (create, update, delete, move, duplicate). |
| `page` | Manage Figma pages (create, rename, delete, navigate). |
| `property` | Read and write custom properties on nodes. |
| `selection` | Get and set the current selection in Figma. |
| `shape` | Create and modify shape nodes (rectangles, ellipses, polygons, etc.). |
| `style` | Manage text, fill, stroke, and effect styles. |
| `text` | Create and modify text nodes with full typography control. |
| `transform` | Apply transformations (rotation, scale, position). |
| `variant` | Manage component variants and variant properties. |
| `variable` | Read and write Figma variables (colors, numbers, strings, etc.). |


### Helper Tools

| Tool | Description |
|------|-------------|
| `bulk` | Execute multiple operations in a single batch for performance. |
| `compare` | Compare two nodes or designs and report differences. |
| `describe` | Generate natural language descriptions of selected nodes or entire designs. |
| `export` | Export Figma frames as HTML/CSS/JSX with assets and responsive code. |
| `info` | Get metadata and information about the current Figma file, pages, and nodes. |
| `join` | Combine multiple nodes or paths using various join strategies. |
| `jsx` | Get, set, or edit JSX representations of Figma nodes. |


## File sandboxing:

"Working directory" is what your AI agent app (Cursor / VSCode) advertise as allowed roots. Please avoid adding low-level directories to config, AI's might in rare cases overwrite files unintentionally. Ensure you backup your project files frequently. 

1. **Read Access** — Conduit can read files from your entire system.
2. **Soft-Write Access** — Conduit can create new files and folders anywhere on your system but **cannot overwrite** existing files.
3. **Safe-Write Access** — Conduit can overwrite existing files **only** within directories listed in `ALLOWED_ROOTS`.

### Security Best Practices

> [!WARNING]
> Only add trusted directories to `ALLOWED_ROOTS`. Avoid broad paths like `~/` or `/` that grant write access to sensitive system or user files.

- **Limit `ALLOWED_ROOTS`** to project-specific directories (e.g., `/Users/John/my-project`).
- **Back up your projects** regularly before granting write permissions.
- **Review directories** in `ALLOWED_ROOTS` periodically to ensure they're still appropriate.

## Privacy & Data Usage

Only data you activly work on is exposed to external services. See bellow regarding what gets exposed

1. Conduit is an entirly local application and has no analytics and does not call any external service or server.
2. Conduit uses (Cursor / VSCode) as your daily driver. If you trust them your good to go. Their EULA applies.
3. Conduit connects to google-gemini, antrhropic, openAI api's if you use the "instant edit" feature. Their EULA applies.

## Auto-Updates

Conduit automatically updates both the **MCP server** and the **Figma plugin** when you restart your MCP application or reload your MCP configuration.

### Update Process

1. **On MCP Restart**: Conduit checks for updates to the server binary and Figma plugin.
2. **Auto-Download**: If updates are available, they are downloaded and applied automatically.
3. **Plugin Sync**: The Figma plugin is updated in `~/.conduit/figma-plugin/` without requiring manual re-import.


## Troubleshooting

### Figma Plugin Not Connecting

**Issue**: The Figma plugin shows "Disconnected" or fails to pair with the MCP server.

**Solution**:
1. Verify the `CHANNEL_KEY` in your MCP configuration matches the channel ID shown in the Figma plugin UI. (This step is otional, you can also just give channel_key to the AI when joining)
2. Ensure the `PORT` is not blocked by a firewall or already in use.
3. Restart both your MCP application and the Figma desktop app.

### File Write Errors

**Issue**: Conduit fails to write or overwrite files.

**Solution**:
1. Check that the target directory is listed in `ALLOWED_ROOTS`.
2. Verify the path uses absolute paths (e.g., `/Users/John/my-project`, not `~/my-project`).
3. Ensure you have file system permissions for the target directory.

### Plugin Import Issues

**Issue**: Cannot import the Figma plugin via `Plugins` → `Development` → `Import plugin from manifest…`.

**Solution**:
1. **Ensure you're using Figma Desktop** — Plugin import is not available in the browser version.
2. Verify the manifest file exists at `~/.conduit/figma-plugin/manifest.json`.
3. If the file is missing, restart your MCP application to trigger the auto-download.

### AI Instant Edit Not Working

**Issue**: AI-powered instant edits fail or are unavailable.

**Solution**:
1. Verify you've added the appropriate API key (e.g., `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, `GEMINI_API_KEY`) to your MCP configuration.
2. Check that `AI_DEFAULT_PROVIDER` and `AI_DEFAULT_MODEL` are set correctly.
3. Ensure your API key is valid and has sufficient quota.

## Limitations

> [!NOTE]
> **Early Access Notice**: Conduit is under active development. The following limitations are being actively addressed:

- **Instant AI Edit**: Batch-mode not yet available (workaround: target parent nodes directly).
- **Node Type Coverage**: Instant AI Edit supports ~95% of node types; some complex types require turn-by-turn mode.
- **Multi-level Styles**: Instant AI Edit doesn't support multi-level styles yet; use turn-by-turn mode for these cases.
- **Export Formats**: Currently limited to HTML, CSS, and JSX. Tailwind, Vue, and other frameworks coming in future releases.
- **Bulk Operations**: Chaining mixed tool calls in bulk mode is experimental; only select tools currently support this.


---
  
© 2025 [conduit.design](https://conduit.design/) - All rights reserved.