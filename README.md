# Node.js Dev Container Template

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

A GitHub template repository providing a fully-configured development container for Node.js projects with AI-assisted development tools, Neovim, and modern development tooling.

## What This Template Provides

This template sets up a containerized development environment with:

- **Node.js 22** on Debian Trixie (for glibc 2.38+ compatibility)
- **Neovim 0.11.2** with LazyVim configuration and opencode.nvim plugin
- **opencode-ai CLI** for AI-assisted development
- **Python 3** with pip for polyglot projects
- **pnpm** package manager (activated via corepack)
- **VS Code integration** with pre-configured extensions
- **Search tools**: ripgrep and fd-find

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) or compatible container runtime
- [VS Code](https://code.visualstudio.com/) with the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)
- **ARM64 architecture** (Apple Silicon or ARM64 Linux host)
- opencode configuration files at `~/.config/opencode` and `~/.local/share/opencode` (optional, for AI features)

> **Note**: This template is built specifically for `linux/arm64` architecture. For x86_64 systems, modify the `build.options` in `devcontainer.json` and update the Neovim download URL in the Dockerfile.

## Usage

### Using as a GitHub Template

1. Click **"Use this template"** on the GitHub repository page
2. Create a new repository from the template
3. Clone your new repository locally
4. Open the folder in VS Code
5. When prompted, click **"Reopen in Container"** (or run the command `Dev Containers: Reopen in Container`)

### Manual Setup

```bash
# Clone the repository
git clone https://github.com/your-username/your-repo.git
cd your-repo

# Open in VS Code
code .

# Use the command palette (Cmd+Shift+P) and select:
# "Dev Containers: Reopen in Container"
```

## Included Tools and Configuration

### Development Environment

| Tool | Version | Purpose |
|------|---------|---------|
| Node.js | 22 | JavaScript runtime |
| pnpm | Latest | Fast, disk-efficient package manager |
| Python | 3.x | Python support for polyglot projects |
| Neovim | 0.11.2 | Terminal-based editor with IDE features |
| ripgrep | System | Fast code search |
| fd-find | System | Fast file finder |
| opencode-ai | Latest | AI-assisted development CLI |

### VS Code Extensions (Auto-installed)

- **ms-python.python** - Python language support
- **ms-python.vscode-pylance** - Python language server
- **esbenp.prettier-vscode** - Code formatter
- **dbaeumer.vscode-eslint** - JavaScript/TypeScript linting
- **bradlc.vscode-tailwindcss** - Tailwind CSS IntelliSense

### Neovim Configuration

The container includes LazyVim with the opencode.nvim plugin pre-configured.

**Keybindings:**

| Key | Mode | Action |
|-----|------|--------|
| `Ctrl+A` | Normal, Visual | Ask opencode about current context |
| `Ctrl+X` | Normal, Visual | Select opencode action |
| `Ctrl+.` | Normal, Terminal | Toggle opencode panel |
| `go` | Normal, Visual | Add range to opencode (operator) |
| `goo` | Normal | Add current line to opencode |
| `Shift+Ctrl+U` | Normal | Scroll opencode up |
| `Shift+Ctrl+D` | Normal | Scroll opencode down |
| `+` | Normal | Increment number (remapped from Ctrl+A) |
| `-` | Normal | Decrement number (remapped from Ctrl+X) |

### Forwarded Ports

| Port | Common Use |
|------|------------|
| 3000 | Next.js, Express, React dev server |
| 5173 | Vite dev server |
| 8000 | Python/FastAPI/Django |
| 8080 | General HTTP server |

## Customization

### Modify the Dev Container Name

Edit `.devcontainer/devcontainer.json`:

```json
{
  "name": "Your Project Name"
}
```

### Change the Workspace Folder

Edit `.devcontainer/devcontainer.json`:

```json
{
  "workspaceFolder": "/workspaces/your-project"
}
```

Also update the `WORKDIR` in `.devcontainer/Dockerfile`.

### Add VS Code Extensions

Add extension IDs to the `customizations.vscode.extensions` array in `devcontainer.json`:

```json
{
  "customizations": {
    "vscode": {
      "extensions": [
        "existing.extension",
        "new.extension-id"
      ]
    }
  }
}
```

### Add System Dependencies

Modify `.devcontainer/Dockerfile` to install additional packages:

```dockerfile
RUN apt-get update && apt-get install -y --no-install-recommends \
    your-package \
    && rm -rf /var/lib/apt/lists/*
```

### Add Additional Ports

Update the `forwardPorts` array in `devcontainer.json`:

```json
{
  "forwardPorts": [3000, 5173, 8000, 8080, 9000]
}
```

### Customize Neovim Plugins

Edit or add Lua files in the LazyVim plugins directory. The opencode configuration is at:
`.devcontainer/opencode.lua`

### Remove opencode Integration

If you do not need AI-assisted development:

1. Remove the `mounts` section from `devcontainer.json`
2. Remove the opencode installation line from the Dockerfile
3. Remove the opencode.lua COPY command from the Dockerfile
4. Remove or replace `.devcontainer/opencode.lua`

## Architecture Notes

This template targets **ARM64 (linux/arm64)** architecture, optimized for:

- Apple Silicon Macs (M1, M2, M3, M4)
- ARM64-based cloud instances (AWS Graviton, etc.)
- ARM64 Linux workstations

### Adapting for x86_64

To use this template on Intel/AMD systems:

1. In `devcontainer.json`, change or remove the platform option:
   ```json
   "build": {
     "options": ["--platform=linux/amd64"]
   }
   ```

2. In the Dockerfile, update the Neovim download:
   ```dockerfile
   RUN NVIM_VERSION="v0.11.2" \
       && curl -fLO "https://github.com/neovim/neovim/releases/download/${NVIM_VERSION}/nvim-linux-x86_64.tar.gz" \
       && tar -xzf nvim-linux-x86_64.tar.gz -C /opt \
       && ln -sf /opt/nvim-linux-x86_64/bin/nvim /usr/local/bin/nvim \
       && rm nvim-linux-x86_64.tar.gz
   ```

## Repository Structure

```
.
├── .devcontainer/
│   ├── devcontainer.json    # Dev container configuration
│   ├── Dockerfile           # Custom container image
│   └── opencode.lua         # Neovim opencode plugin config
├── LICENSE                  # Apache 2.0 License
└── README.md               # This file
```

## Post-Creation Behavior

When the container starts, it automatically:

1. Installs pnpm dependencies if `pnpm-lock.yaml` exists
2. Installs Python dependencies if `requirements.txt` exists

## Troubleshooting

### Container fails to build on x86_64

See the [Adapting for x86_64](#adapting-for-x86_64) section above.

### Neovim plugins not loading

Run inside the container:
```bash
nvim --headless "+Lazy! sync" "+qa!"
```

### opencode not working

Ensure your opencode configuration exists on the host:
- `~/.config/opencode/`
- `~/.local/share/opencode/`

### Permission issues with mounted volumes

The container runs as the `node` user (UID updated to match host). If you encounter permission issues, verify that your host directories are accessible.

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.
