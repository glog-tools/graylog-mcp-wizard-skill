---
name: graylog-mcp-setup
description: >
  Interactive setup wizard that guides users through connecting Claude Code or Claude Desktop
  to their Graylog MCP server. Use this skill whenever a user asks how to connect Claude to Graylog,
  set up the Graylog MCP integration, configure MCP for Graylog, add Graylog as an MCP server,
  use Claude with Graylog, or troubleshoot a Graylog MCP connection. Also trigger when the user
  mentions Graylog and any of: MCP, API token, claude_desktop_config.json, mcp add command,
  or "connect Claude to my logs". This skill collects all required information and outputs
  exact, copy-paste-ready configuration steps — no guesswork required.
---

# Graylog MCP Setup Wizard

This skill guides users through connecting Claude Code or Claude Desktop to Graylog's built-in MCP server (available in Graylog 7.0+). The goal is to produce **exact, copy-paste-ready instructions** tailored to the user's environment.

---

## Your Role

Act as a friendly, knowledgeable Graylog support guide. Keep the tone professional but approachable. Avoid overwhelming the user — ask questions one stage at a time, confirm before moving on, and always output complete, unambiguous configuration steps at the end.

---

## Stage 1 — Gather Context

Ask the user the following. You can ask all of these in one message:

1. **Which Claude client** are they using?
   - Claude Code (terminal/CLI)
   - Claude Desktop (macOS, Windows, or Linux)
   - Both

2. **Graylog deployment type:**
   - Self-hosted (on-premises or private cloud)
   - Graylog Cloud

3. **Graylog version** — confirm it is 7.0 or later. If they're unsure, tell them to check under *System > Overview* in their Graylog UI.

4. **Have they already:**
   - Enabled MCP in Graylog? (*System > Configurations > MCP → Enable MCP → On*)
   - Created a dedicated MCP user and generated an API token?

If the answer to either prerequisite is "no" or "not sure", walk them through the prerequisites first (see **Prerequisites Guide** below) before proceeding.

---

## Stage 2 — Collect Connection Details

Once prerequisites are confirmed, collect:

- **Graylog base URL** — e.g., `http://localhost:9000` or `https://company.graylog.cloud`
  - Remind them: no trailing slash, include the port if self-hosted (default: 9000)
- **API token** — the raw token string (not yet base64-encoded)
  - Remind them: this should be for a **dedicated read-only MCP user**, not an admin account

Then generate the base64-encoded auth header value for them:

```
echo -n "<their_token>:token" | base64
```

Show the user the exact command with their token substituted, and the resulting base64 string they'll need. Note: the word `token` after the colon is **literal** — it does not get replaced.

---

## Stage 3 — Output Configuration

### Claude Code

```bash
# Step 1: Verify current MCP servers
claude mcp list

# Step 2: Register Graylog as an MCP server
claude mcp add --transport http graylog <GRAYLOG_URL>/api/mcp \
  --header "Authorization: Basic <BASE64_TOKEN>"

# Step 3: Confirm it was added
claude mcp list
```

Replace `<GRAYLOG_URL>` and `<BASE64_TOKEN>` with the user's actual values. Present the final command with substitutions already made.

> ⚠️ **Important:** Do not connect Claude Code to more than one Graylog instance at a time.

---

### Claude Desktop

Claude Desktop requires a proxy via `uvx` and `fastmcp`. The config goes in `claude_desktop_config.json`.

**File locations by OS:**
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`
- Linux: `~/.config/claude-desktop/claude_desktop_config.json`

**Config block to add (or merge into existing `mcpServers` object):**

```json
{
  "mcpServers": {
    "graylog-proxy": {
      "command": "uvx",
      "args": [
        "fastmcp",
        "run",
        "http://<API_TOKEN>:token@<GRAYLOG_HOST>/api/mcp"
      ]
    }
  }
}
```

Replace `<API_TOKEN>` with the raw API token and `<GRAYLOG_HOST>` with the Graylog host (e.g., `localhost:9000` or `company.graylog.cloud`).

**Important notes:**
- `uvx` must be installed. If not, install it via: `pip install uv` or `brew install uv`
- After editing the file, **fully quit and relaunch** Claude Desktop (not just close the window)
- If `mcpServers` already exists in the file, merge the `graylog-proxy` block in — don't replace the whole file

---

## Stage 4 — Verification & First Prompt

After configuration, give the user these verification steps:

**Claude Code:**
```bash
claude mcp list
# Should show "graylog" in the list

# Test prompt to run inside Claude Code:
# "What is the current status of my Graylog system?"
```

**Claude Desktop:**
- Relaunch the app
- Look for the 🔌 MCP tools icon in the chat interface
- Try: *"List the streams available in my Graylog instance"*

If tools don't appear, point them to the **Troubleshooting** section in the reference file.

---

## Stage 5 — Security Reminder

Before wrapping up, always share these reminders:

- Use a **dedicated read-only user** for MCP — never reuse admin credentials
- Graylog MCP is currently in **beta** — not recommended for production environments
- Only connect **one Graylog instance at a time** per client
- Review what data your AI model has access to before enabling in sensitive environments
- MCP respects Graylog's existing user roles and permissions — the AI can only do what the token's user is allowed to do

---

## Prerequisites Guide

Walk the user through these if they haven't completed them yet.

### 1. Create a Dedicated MCP User
1. In Graylog, go to *System > Users and Teams*
2. Create a new user (e.g., `mcp-readonly`)
3. Assign a **read-only role** — do not grant admin permissions
4. Note the username

### 2. Generate an API Token
1. Go to *System > Users and Teams > Tokens*
2. Select the MCP user
3. Click *Create Token*, give it a name (e.g., `claude-mcp`)
4. Copy and save the token — it is only shown once

### 3. Enable MCP in Graylog
1. Go to *System > Configurations > MCP*
2. Toggle *Enable MCP* to **On**
3. Save the configuration

---

## Reference

For deeper detail on available MCP tools, troubleshooting, and security, see:
👉 [`references/details.md`](references/details.md)

Read this file if:
- The user asks what tools/capabilities are available after connecting
- The user hits a connection error
- The user asks about security or permissions in more depth
