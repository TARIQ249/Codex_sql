# VS Code MCP Bridge

This workspace contains two pieces that work together:

- `packages/mcp-server`: a local MCP server that exposes VS Code-focused tools over `stdio`
- `packages/vscode-bridge-extension`: a VS Code extension that hosts a local HTTP bridge and forwards requests into the real VS Code extension API

## Why this split exists

An MCP server running as a normal Node.js process does not have direct access to the VS Code extension host. The extension bridge solves that by listening on `127.0.0.1` and translating MCP tool calls into VS Code API calls such as:

- opening editors
- reading the active document
- listing workspace folders
- finding files with the VS Code workspace API
- listing or executing VS Code commands

## Tools exposed by the MCP server

- `vscode_bridge_health`
- `vscode_get_workspace_state`
- `vscode_get_active_document`
- `vscode_read_document`
- `vscode_find_files`
- `vscode_open_file`
- `vscode_list_commands`
- `vscode_execute_command`
- `vscode_show_message`

## Setup

1. Install dependencies:

   ```powershell
   npm install
   ```

2. Build the server and extension:

   ```powershell
   npm run build
   ```

3. Start the VS Code bridge extension in an Extension Development Host:

   - Open this folder in VS Code
   - Run the `Run VS Code Bridge Extension` launch configuration

4. Register the MCP server in VS Code:

   - Use the sample configuration in [.vscode/mcp.json](D:\PROJECTS\BI\.vscode\mcp.json)
   - The server expects the extension bridge at `http://127.0.0.1:32173`

## Notes

- The bridge only listens on `127.0.0.1`
- This scaffold intentionally keeps auth simple for local development
- If you want, the next step can be adding an auth token, richer remote-workspace APIs, or packaging the extension as a `.vsix`

## SQL workflow

This repo also includes a documented path for working with the local `Tariq` SQL Server database through the already-connected VS Code SQL extension.

- Start with [docs/vscode-sql-workflow.md](d:\PROJECTS\BI\docs\vscode-sql-workflow.md)
- For routine queries, use `.\scripts\Invoke-VsCodeSqlQuery.ps1`
- For queries that should run visibly in the VS Code MSSQL results pane, use `.\scripts\Invoke-VsCodeSqlWorkspaceQuery.ps1`
- For future agent runs, see [AGENTS.md](d:\PROJECTS\BI\AGENTS.md)

Example:

```powershell
.\scripts\Get-VsCodeSqlProfiles.ps1
.\scripts\Invoke-VsCodeSqlQuery.ps1 -ProfileName "<profile>" -Database "<database>" -Query "SELECT TOP 10 * FROM dbo.Customers;"
```
