# Base image: Official devcontainer typescript-node on Debian Trixie (glibc 2.38+ for Neovim)
FROM mcr.microsoft.com/devcontainers/typescript-node:22-trixie

# Avoid prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Install additional system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3 \
    python3-pip \
    python3-venv \
    ripgrep \
    fd-find \
    && rm -rf /var/lib/apt/lists/* \
    && ln -sf /usr/bin/python3 /usr/bin/python \
    && ln -sf /usr/bin/pip3 /usr/bin/pip

# Install Neovim from GitHub releases (stable release with ARM64 support)
# Note: LazyVim requires Neovim >= 0.11.2 (see https://github.com/LazyVim/LazyVim/issues/6421)
RUN NVIM_VERSION="v0.11.2" \
    && curl -fLO "https://github.com/neovim/neovim/releases/download/${NVIM_VERSION}/nvim-linux-arm64.tar.gz" \
    && tar -xzf nvim-linux-arm64.tar.gz -C /opt \
    && ln -sf /opt/nvim-linux-arm64/bin/nvim /usr/local/bin/nvim \
    && rm nvim-linux-arm64.tar.gz

# Install pnpm globally (corepack already enabled in base image)
RUN corepack prepare pnpm@latest --activate

# Switch to non-root user (node user already configured in devcontainer base image)
USER node
WORKDIR /home/node

# Setup pnpm global bin and add to PATH
ENV PNPM_HOME="/home/node/.local/share/pnpm"
ENV PATH="/home/node/.local/bin:${PNPM_HOME}:${PATH}"

# Create required directories
RUN mkdir -p /home/node/.config /home/node/.local/share /home/node/.cache

# Install opencode CLI globally
RUN pnpm install -g opencode-ai

# Install LazyVim configuration
RUN git clone https://github.com/LazyVim/starter /home/node/.config/nvim \
    && rm -rf /home/node/.config/nvim/.git

# Add opencode.nvim plugin
COPY --chown=node:node opencode.lua /home/node/.config/nvim/lua/plugins/opencode.lua

# Pre-install LazyVim plugins (headless mode)
# Note: The mason/treesitter race condition warning is cosmetic and doesn't affect the build.
# Using Lazy! (with !) for synchronous execution, then a brief sleep for async cleanup.
RUN nvim --headless "+Lazy! sync" "+sleep 2" "+qa!" 2>&1 || true

# Set working directory for the project
WORKDIR /workspaces/ai-search

# Default command
CMD ["tail", "-f", "/dev/null"]
