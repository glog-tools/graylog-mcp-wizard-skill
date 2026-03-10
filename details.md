# Graylog MCP — Reference Details

## Available MCP Tools

Once connected, Claude has access to the following Graylog tools. Some require specific Graylog editions (Security or Illuminate).

### System Information
- `mcp_preview_get_current_time`
- `mcp_preview_get_system_status`
- `mcp_preview_get_formatted_system_status`

### Log Management
- `mcp_preview_list_streams`
- `mcp_preview_list_inputs`
- `mcp_preview_list_indices`
- `mcp_preview_list_index_sets`
- `mcp_preview_list_fields`

### Search and Analysis
- `mcp_preview_search_messages`
- `mcp_preview_aggregate_messages`

### Security Operations *(Graylog Security edition)*
- `mcp_preview_list_assets`
- `mcp_preview_describe_asset`
- `mcp_preview_list_new_security_events`
- `mcp_preview_list_security_events_by_asset`
- `mcp_preview_update_security_events`

### Investigations *(Graylog Security edition)*
- `mcp_preview_list_investigations`
- `mcp_preview_create_investigation`
- `mcp_preview_update_investigation`
- `mcp_preview_get_possible_investigation_values`

### Threat Intelligence *(Illuminate required)*
- `mcp_preview_get_mitre_threat_coverage`
- `mcp_preview_list_illuminate_content_packs`

### Resources
- `mcp_preview_list_resource`
- `mcp_preview_describe_resource`

---

## Troubleshooting

### Connection fails when registering MCP server
- Verify Graylog is running and accessible at the URL you provided
- Confirm MCP is enabled: *System > Configurations > MCP*
- Check that the base64 token was generated correctly:
  ```
  echo -n "<your_token>:token" | base64
  ```
  Remember: the word `token` after the colon is literal — do not replace it
- Check Graylog server logs for MCP-related errors

### Claude Desktop: tools don't appear after restart
- Confirm `uvx` is installed: run `uvx --version` in terminal
- If not installed: `pip install uv` or `brew install uv` (macOS)
- Double-check the JSON syntax in `claude_desktop_config.json` — a missing comma or bracket will silently break it
- Make sure you fully quit Claude Desktop (not just closed the window) before relaunching
- On macOS, check Claude menu bar → Quit Claude, then relaunch

### Claude Code: "graylog" not appearing in mcp list
- Re-run the `claude mcp add` command
- Ensure you're running Claude Code v1.0+ which supports `--transport http`
- Try: `claude mcp remove graylog` then re-add

### Authentication errors (401/403)
- Token may have expired or been revoked — generate a new one in *System > Users and Teams > Tokens*
- Ensure the user account is active and not locked
- Verify the base64 encoding includes the `:token` suffix before encoding

### Self-hosted: can't reach Graylog from Claude Desktop
- If Graylog is on a remote server, replace `localhost` with the actual hostname or IP
- Ensure port 9000 (or your configured port) is open/reachable
- If using HTTPS, ensure the certificate is valid or configure trust appropriately

---

## Example Prompts to Try

After connecting, these are useful first prompts:

- *"What is the current status of my Graylog system?"*
- *"How much disk space is my Graylog server using, and which index set is largest?"*
- *"List all available streams in my Graylog instance"*
- *"Show me the most recent error logs from the last 15 minutes"*
- *"What security events have triggered in the last hour?"* *(Security edition)*
- *"Which MITRE ATT&CK techniques am I currently detecting?"* *(Illuminate required)*

---

## Official Documentation
- MCP Tools overview: https://go2docs.graylog.org/current/setting_up_graylog/model_context_protocol__mcp__tools.htm
- REST API tokens: https://go2docs.graylog.org/current/setting_up_graylog/rest_api_access_tokens.htm
