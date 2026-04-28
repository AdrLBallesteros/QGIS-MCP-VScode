# QGIS-MCP-VScode from QgisPortAgent

A VS Code GitHub Copilot agent ecosystem for **QGIS 3.44 LTR** to control QGIS3 remotely via MCP, all without leaving your editor.

---

## Getting Started

### Prerequisites

| Requirement | Version | Download | Notes |
|---|---|---|---|
| **QGIS** | 3.44 LTR | [qgis.org/download](https://qgis.org/download/) | OSGeo4W or Standalone both work |
| **VS Code** | 1.101+ | [code.visualstudio.com](https://code.visualstudio.com/) | 1.101 minimum for MCP support |
| **GitHub Copilot** | Latest | [Copilot extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) | Requires active subscription |
| **Python** | 3.12+ | Bundled with QGIS | No separate install needed |

### Setup (one command)

Windows Command Prompt:

```cmd
git clone https://github.com/AdrLBallesteros/QGIS-MCP-VScode.git
cd QGIS-MCP-VScode
setup.cmd
```

`setup.ps1`, `setup.cmd`, and `setup.sh` will:

1. Auto-detect your QGIS Python interpreter and update VS Code settings
2. Install [`uv`](https://docs.astral.sh/uv/) (Python package runner) if missing
3. Optionally download the [GitHub MCP Server](https://github.com/github/github-mcp-server) binary for manual workspace registration
4. Link source plugins into your QGIS profile via the platform-specific `scripts/setup_plugins` helper

### Enable Plugins in QGIS

1. Open **QGIS**
2. Go to **Plugins → Manage and Install Plugins**
3. Check: **QGIS MCP**
4. Click **QGIS MCP** in the Plugins menu → **Start Server**

### Start the Workspace MCP Server in VS Code

1. Open the Command Palette (`Ctrl+Shift+P`) and run **Developer: Reload Window** first.
2. Open the Command Palette (`Ctrl+Shift+P`) and run **MCP: List Servers**.
3. Find the `qgis` workspace server from `.vscode/mcp.json`.
4. If prompted, confirm that you trust the server.
5. If the server is disabled, enable it.
6. If the server is stopped, start it.

### Switch to Agent Mode in VS Code

1. Open Copilot Chat (`Ctrl+Alt+I`)
2. Click the mode selector dropdown (bottom of chat input)
3. Select **Agent**

### First Test

```text
Ping the QGIS server
```

You should see `{"pong": true}`. If the agent appears to stall, run **MCP: List Servers** and confirm the `qgis` server is trusted and running.

### Recommended First Run (Intuitive Walkthrough)

1. Validate setup (run one setup script, start the QGIS MCP plugin, trust and start the `qgis` workspace MCP server)
2. Achieve first success (`Ping the QGIS server`)
3. Continue with an exact next prompt (Example: `Add a continent.shp layer to QGIS`)


## Requirements for Copilot Agent Workflow
---

To use the agents in this repository effectively, users should have the following installed and configured:

- **QGIS 3.44**
- **VS Code 1.112**
- **GitHub Copilot extension**
- **GitHub Copilot account** with active access

### VS Code Recommended Extensions

When you open this workspace in VS Code, you will automatically be prompted to install all recommended extensions.
They are defined in `.vscode/extensions.json`. The key ones are listed below.

| Extension | Purpose | Required |
| --- | --- | --- |
| GitHub Copilot Chat| AI engine that drives all agents | **Required** |
| Python | Syntax, linting, virtual-env picker | **Required** |
| Pylance | Fast type-checking and IntelliSense | **Required** |
| YAML | Validates brief templates in `examples/briefs/templates/*.yml` | Recommended |
| markdownlint | Catches formatting issues in docs | Recommended |
| Markdown All in One | TOC, preview shortcuts, keybindings | Optional |
| Mermaid Markdown support | Renders Mermaid diagrams in preview | Optional |

### Additional Requirements
| Tool | Purpose | Install |
|---|---|---|
| **`uv`** | Python package runner — launches the MCP server | Auto-installed by `setup.ps1`, `setup.cmd`, or `setup.sh`, or [install manually](https://docs.astral.sh/uv/) |
| **`github-mcp-server`** | Optional manual workspace server for repo/issue/PR tools. Copilot CLI already includes a built-in GitHub MCP server. | Auto-downloaded by `setup.ps1`, `setup.cmd`, or `setup.sh` if you want the manual workspace binary path |

---

## Controlling QGIS Remotely via MCP

### What it does

The GitHub Copilot agent lets you control a running QGIS instance directly from Copilot Chat — load projects, manage layers, apply styles, run processing algorithms, and export maps.

### Architecture

```text
VS Code (Copilot Agent Mode)
  └─ .vscode/mcp.json
  └─ spawns QGIS MCP server (uv run qgis_mcp_server.py)
    └─ TCP socket → localhost:9876
      └─ QGIS plugin (qgis_mcp) listens → executes PyQGIS
```

The default `qgis` MCP flow is fully local. VS Code launches the workspace MCP server, and the MCP server connects to the QGIS plugin over `localhost:9876`.

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `Connection refused` on port 9876 | QGIS MCP plugin not started | In QGIS: **Plugins → QGIS MCP → Start Server** |
| `uv: command not found` | `uv` not installed | Run your platform setup script (`setup.ps1`, `setup.cmd`, or `setup.sh`) or install via [docs.astral.sh/uv](https://docs.astral.sh/uv/) |
| Agent stalls or never connects | VS Code `qgis` MCP server was not trusted, enabled, or started | Run **MCP: List Servers**, trust `qgis`, then enable/start it |
| Agent says "no tools available" | VS Code < 1.101 | Upgrade VS Code (1.101+ required for MCP) |
| `QGIS_MCP_PORT` ignored | Plugin reads env at start time | Restart QGIS after changing `.env` |
| GitHub MCP returns 401 | Invalid or expired PAT | Re-create [fine-grained PAT](https://github.com/settings/personal-access-tokens/new) with correct scopes |
| `uv` was installed but MCP still fails to start | VS Code has stale PATH state | Run **Developer: Reload Window** and start `qgis` again from **MCP: List Servers** |
| Plugin not listed in QGIS | Symlink missing or broken | Re-run `scripts/setup_plugins.ps1` (Windows) or `scripts/setup_plugins.sh` (Linux/macOS) |
| `SRE module mismatch` on launch | Google Cloud SDK pollutes `PYTHONPATH` | Clear `PYTHONPATH` and `PYTHONHOME` before launching QGIS |

---

## License

MIT — see [LICENSE](LICENSE) for details.
