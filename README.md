# conduit.design

Turns any LLM-powered agent into a first-class Figma collaborator: No more clicking through menus or manual adjustments. Just describe what you want, and watch it happen. [Watch the video](https://www.linkedin.com/posts/andr%C3%A9-j-1676a2169_launch-video-the-landing-page-goes-live-activity-7390030243383730176-WNnL)

## Features

- **Full Figma API access** — Batch transform complex designs with simple prompts  
- **Instant AI editing** — Transform complex design hierarchies in seconds
- **Code Export** — Generate production-ready code from design instantly  

## Prerequisites

Before installation, ensure you have:

- **Figma Desktop App** (Free version or Paid)
- **MCP-compatible application** (Cursor, VSCode, AntiGravity, etc.)

## Quick Start

### Step 1: Configure Your MCP Application

Add to your MCP configuration file (`mcp_config.json` for Cursor/VSCode, platform-specific for others):   

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

### Step 2: Auto-Install Figma Plugin

1. **Start the MCP** once from your AI application (this auto-installs the plugin)
2. **Open Figma Desktop** → `Plugins` → `Development` → `Import plugin from manifest`
3. **Select**: `~/.conduit/figma-plugin/manifest.json`
4. **Copy the channel ID** shown in the Figma plugin i.e. `"purple-owl-27"`

### Step 3: Connect & Create

```text
User: Talk to Figma on channel "purple-owl-27"
Agent: ✅ Connected to Figma on channel "purple-owl-27"

User: Create a modern navigation bar with our brand color #3366FF
Agent: ✅ Created navigation bar with brand color #3366FF
```

## Configuration

Conduit is configured via environment variables in your MCP configuration. You do **not** need to pass ports or paths as CLI arguments to the install script – the one-line installer inherits whatever env you define here.

### Basic MCP config (recommended)

Minimal recommended setup:

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
        "PROJECT_ROOT": "/Users/you/.conduit-workspace",
        "ALLOWED_ROOTS": "/Users/you/.conduit-workspace|/Users/you/assets|/Users/you/Desktop/experiments"
      }
    }
  }
}
```

Key env vars:

- `CHANNEL_KEY` – permanent channel id used to pair the MCP server with the Figma plugin.
- `PORT` – WebSocket port the plugin connects to (default `3055`).
- `PROJECT_ROOT` – directory Conduit uses for its own logs and temp files (for example `/Users/you/.conduit-workspace`). This does **not** limit where `ALLOWED_ROOTS` can point.
- `ALLOWED_ROOTS` – `|`-delimited list of absolute directories Conduit is allowed to treat as safe-write roots (where overwrites are allowed).
  - You can point this at any directories on your system (for example `/Users/you/.conduit-workspace`, `/Users/you/assets`, `/Users/you/Desktop/experiments`).
  - Be cautious when adding broad paths (e.g. `~/` or `/`); anything listed here becomes writable by Conduit.

### Windows MCP config (PowerShell example)

On Windows, you can configure Conduit using the same env-only contract, but invoke it via PowerShell instead of `/bin/bash`. A conceptual MCP config looks like:

```json
{
  "mcpServers": {
    "conduit": {
      "command": "powershell",
      "args": [
        "-NoProfile",
        "-Command",
        "iwr https://conduit.design/install.ps1 -UseBasicParsing | iex"
      ],
      "env": {
        "CHANNEL_KEY": "purple-owl-26",
        "PORT": "3055",
        "PROJECT_ROOT": "C:\\Users\\you\\.conduit-workspace",
        "ALLOWED_ROOTS": "C:\\Users\\you\\.conduit-workspace|C:\\Users\\you\\assets|C:\\Users\\you\\Desktop\\experiments"
      }
    }
  }
}
```

This uses the same env keys described above; only the `command`/`args` change to a PowerShell-based installer/launcher. Your MCP host is responsible for passing the `env` block through to the PowerShell process, where it is available as `$env:CHANNEL_KEY`, `$env:PORT`, etc.

### Advanced: enable instant edit

To turn on AI-powered instant edits, add your provider keys and defaults to the same `env` block:

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
        "PROJECT_ROOT": "/path/to/project",
        "ALLOWED_ROOTS": "/path/to/project|/path/to/project/temp_files|/path/to/project/_logs",

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

### AI Provider Options

| Provider | Models | Environment Key |
|----------|--------|----------------|
| **OpenAI** | `gpt-5-1`, `gpt-5-1-mini` | `OPENAI_API_KEY` |
| **Anthropic** | `claude-sonnet-4-5`, `claude-opus-4-5` | `ANTHROPIC_API_KEY` |
| **Google** | `gemini-3.0-pro`, `gemini-3.0-flash` | `GEMINI_API_KEY` |

## Export Capabilities

Conduit can export deploy-ready websites from WYSIWYG `{page-breakpoint-theme}` Figma frames:

- **Responsive Design** (mobile, tablet, desktop breakpoints)  
- **Automatic Theming** (dark & light mode themes)  
- **Asset Export** (SVG, images with optimization)  
- **CSS Tokens** (design system variables)  
- **Complete Files** (HTML, CSS, assets)  
- **Google Fonts** (automatic imports & optimization)  
- **Multi-page** (full website exports)  
- **Hyperlinks** (navigation & interactions)

## Supported Applications

| Application | Configuration Location | Status |
|-------------|----------------------|--------|
| [Cursor](https://cursor.com) | `~/.cursor/mcp_settings.json` | ✅ Stable |
| [Google Antigravity](https://antigravity.google.com/) | Extension settings | ✅ Stable |
| [VSCode](https://code.visualstudio.com) | `.vscode/mcp.json` | ✅ Stable |

## Supported Platforms

Conduit currently supports the following operating systems via the one-line installer:

- **macOS (Apple Silicon / arm64)**
- **macOS (Intel / x64)**
- **Linux (x64)**
- **Linux (arm64)**

> Note: Windows support is planned for a future update. For now, the official installer targets macOS and Linux only.

## Tool API:
- Read and Write for Figma API (Single / Batch): `annotation, asset, autolayout, boolean, component, constraint, effect, grid, guide, group, node, page, property, selection, shape, style, text, transform, variant, variable`
- Helper tools: `bulk, compare, describe, export, info, join, jsx`

## File sandboxing:

"Working directory" is what your AI agent app (Cursor / VSCode) advertise as allowed roots. Please avoid adding low-level directories to config, AI's might in rare cases overwrite files unintentionally. Ensure you backup your project files frequently. 

1. Conduit can read files from your entire system
2. Conduit can soft-write to your entire system (only create new files/folders)
3. Conduit can safe-write to working directory / paths you set in config (overwrite existing files and everything soft-write can do)

## Privacy acknowledgment

Only data you activly work on is exposed to external services. See bellow how they are effected. 

1. Conduit is an entirly local application and has no analytics and does not call any external service or server.
2. Conduit uses (Cursor / VSCode) as your daily driver. If you trust them your good to go. Their EULA applies.
3. Conduit connects to google-gemini, antrhropic, openAI api's if you use the "instant edit" feature. Their EULA applies.

## Minor early access limitations:

- Batch-mode for Instant AI edit is not ready, (workaround is to target parent) (fixing soon)
- Instant AI edit works on most node types, but some complex node types are not supported (Instant AI edit will work in 95% of use cases, this will be resolved in the medium term) 
- Instant AI edit does not work with multi level styles yet, use Turn by turn mode to use multi-style (fixing very soon)
- Exports only html, css, jsx. (Tailwind, Vue, etc coming later, medium term)
- Chaining mixed tool calls together (bulk) in one prompt is experimental, only some tools has support (this will be resolved in the medium term)

---
  
© 2025 [conduit.design](https://conduit.design/)  - All rights reserved.
