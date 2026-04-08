# Agent Notes

## SQL access

- Prefer the VS Code SQL connection workflow in this repo over `sqlcmd`, `Invoke-Sqlcmd`, or ad hoc `.NET SqlClient` connections.
- On this machine, direct shell-based SQL access can fail with SSPI / target principal errors even when the SQL Server extension in VS Code is already connected.
- For read/query work, use `.\scripts\Invoke-VsCodeSqlQuery.ps1` first.
- To discover available saved connections on a machine, use `.\scripts\Get-VsCodeSqlProfiles.ps1`.
- Do not hardcode connection UUIDs or assume a profile named `Local` exists.
- The helper script can target any saved SQL profile and any database reachable through that profile.

## Bridge requirements

- The VS Code bridge must answer at `http://127.0.0.1:32173/health`.
- If the bridge is down, follow the recovery steps in [docs/vscode-sql-workflow.md](d:\PROJECTS\BI\docs\vscode-sql-workflow.md).
- The installed bridge extension id used for SQL connection sharing is `local.vscode-bridge-extension`.
- The SQL connection must be saved in VS Code settings. A purely transient connection is not enough for the helper scripts.

## Preferred workflow

1. Check bridge health.
2. List saved profiles with `.\scripts\Get-VsCodeSqlProfiles.ps1` if the machine is unfamiliar.
3. Use `.\scripts\Invoke-VsCodeSqlQuery.ps1 -ProfileName "<profile>" -Database "<database>" -Query "<sql>"` for normal queries.
4. If the user wants results visibly inside VS Code, use `.\scripts\Invoke-VsCodeSqlWorkspaceQuery.ps1 -Query "<sql>"`.
5. Reuse `workspace-queries.sql` for all visible query runs so prior queries stay intact and only the selected block is executed.

## Useful commands

- List bridge-exposed VS Code commands:
  `Invoke-RestMethod 'http://127.0.0.1:32173/commands?filter=mssql&includeInternal=true&limit=200'`
- List saved SQL profiles:
  `.\scripts\Get-VsCodeSqlProfiles.ps1`
- Example query:
  `.\scripts\Invoke-VsCodeSqlQuery.ps1 -ProfileName "<profile>" -Database "<database>" -Query "SELECT TOP 10 * FROM dbo.Customers;"`
- Example visible query:
  `.\scripts\Invoke-VsCodeSqlWorkspaceQuery.ps1 -Query "SELECT TOP 20 * FROM dbo.Products;"`

## Reference

- Detailed guide: [docs/vscode-sql-workflow.md](d:\PROJECTS\BI\docs\vscode-sql-workflow.md)
