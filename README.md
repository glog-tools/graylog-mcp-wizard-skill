# graylog-mcp-wizard-skill

A Claude skill that acts as an interactive setup wizard, guiding users through connecting **Claude Code** or **Claude Desktop** to their Graylog MCP server.

## What It Does

When installed, this skill enables Claude to walk any user through the full Graylog MCP setup process — collecting their environment details, generating auth tokens, and producing **exact, copy-paste-ready configuration commands** tailored to their setup.

### Supported Configurations
- **Claude Code** (CLI) → `claude mcp add` command with HTTP transport
- **Claude Desktop** (macOS, Windows, Linux) → `claude_desktop_config.json` block with `uvx`/`fastmcp` proxy

### What the Skill Covers
1. Prerequisite checks (Graylog 7.0+, MCP enabled, API token created)
2. Guided setup of assigning the MCP Server role to an existing user and generating an API token
3. Base64 auth header generation
4. Client-specific configuration output
5. Verification steps and first prompts to test the connection
6. Security best practices and beta caveats

---

## Requirements

- **Graylog 7.0 or later** with MCP enabled under *System > Configurations > MCP*
- **Claude Code** or **Claude Desktop**
- For Claude Desktop: `uvx` installed (`pip install uv` or `brew install uv`)

---

## Installation

### Option 1: Install via `.skill` file

1. Download [`graylog-mcp-setup.skill`](./graylog-mcp-setup.skill) from the [Releases](../../releases) page
2. In Claude, go to **Settings > Skills**
3. Click **Install Skill** and select the downloaded `.skill` file

### Option 2: Manual install

Copy the `graylog-mcp-setup/` folder into your Claude skills directory:

- **macOS**: `~/Library/Application Support/Claude/skills/`
- **Windows**: `%APPDATA%\Claude\skills\`
- **Linux**: `~/.config/claude-desktop/skills/`

---

## Usage

Once installed, trigger the skill by asking Claude something like:

- *"Help me connect Claude to my Graylog MCP server"*
- *"How do I set up the Graylog MCP integration?"*
- *"Configure Claude Desktop to use Graylog"*
- *"Add Graylog as an MCP server in Claude Code"*

Claude will ask a few questions about your environment and produce step-by-step instructions specific to your setup.

---

## Repository Structure

```
graylog-mcp-setup/
├── SKILL.md                  # Main skill instructions
└── references/
    └── details.md            # MCP tools reference, troubleshooting, example prompts
graylog-mcp-setup.skill       # Packaged skill file for direct installation
README.md                     # This file
```

---

## Security Notes

- Always use a **limited-access user with the MCP Server role** for MCP — never use admin credentials
- Graylog MCP is currently in **beta** and not recommended for production environments
- MCP respects Graylog's existing user roles — Claude can only access what the token's user is permitted to see
- Do not connect more than one Graylog instance at a time

---

## Related Resources

- [Graylog MCP Documentation](https://go2docs.graylog.org/current/setting_up_graylog/model_context_protocol__mcp__tools.htm)
- [Graylog REST API Tokens](https://go2docs.graylog.org/current/setting_up_graylog/rest_api_access_tokens.htm)
- [Claude Code Setup](https://docs.claude.com/en/docs/claude-code/setup)
- [Model Context Protocol](https://modelcontextprotocol.io)

---

## Contributing

Issues and pull requests welcome. If you run into a setup scenario not covered by this skill, please open an issue with your environment details.

---

*Built for Graylog customers connecting Claude to their SIEM environment.*
