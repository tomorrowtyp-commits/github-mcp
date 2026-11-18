# GitHub MCP connection

Connects DeepSeek Harness to a GitHub account through the official remote GitHub
MCP Server (`https://api.githubcopilot.com/mcp/`), mounted by the built-in
`@deepseek-ai/dsh-mcp-client` plugin. Every tool is exposed to the agent as
`mcp__github__<tool>`, independent of the model: any tool-calling model works.

## What you can do

- **Create repositories** — public or private (`create_repository`, `private`).
- **Upload content** — single file or multi-file commit (`create_or_update_file`, `push_files`).
- **Delete content** — a file (`delete_file`) or a whole repository (`delete_repository`).

## Configuration

Provide a GitHub fine-grained Personal Access Token as the credential
`GITHUB_MCP_PAT` (Settings → Credentials, or the launch environment). The patch
reads it at runtime via `Bearer ${credential:GITHUB_MCP_PAT}` with
`expandTemplates: true`; the token never appears in this package or in
diagnostics.

- Create / upload / delete files: `Contents: Read and write`, `Metadata: Read`.
- Delete a whole repository: also `Administration: Read and write` (`delete_repo`).
- Network: the harness must reach `https://api.githubcopilot.com/mcp/`.
- Toolset: only `repos` is requested (`X-MCP-Toolsets: repos`).

`failOnStartupError` is `false`, so before the credential is saved the entry
stays in `waiting-credentials` instead of failing activation; saving the
credential connects without a restart.

## Safety

- The token is read only from the credential reference / environment, never
  written to configuration or conversation text.
- Deleting files and repositories is irreversible. Confirm each deletion before
  running it.
