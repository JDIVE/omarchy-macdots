# Omarchy-Mac Dotfiles

Dotfiles for Jamie's Omarchy-Mac setup on Apple Silicon. Managed with GNU stow.

## Installation

### Prerequisites
- GNU stow installed (`sudo pacman -S stow`)
- Omarchy-Mac environment set up

### Quick Start

```bash
cd ~/projects/omarchy-macdots

# Backup existing configs (recommended)
cp -r ~/.config ~/.config.backup

# Pre-create directories to control tree folding
mkdir -p ~/.config/hypr/conf
mkdir -p ~/.config/uwsm
mkdir -p ~/.config/waybar
mkdir -p ~/.config/alacritty
mkdir -p ~/.config/fcitx5
mkdir -p ~/.config/zed
mkdir -p ~/.config/systemd/user

# Dry-run to see what will be created
stow --simulate --verbose -t ~ .

# Actually stow all packages
stow -t ~ .
```

### Package Structure

Each directory is a stow package. You can selectively install:

```bash
# Install just Hyprland and zsh
stow -t ~ hypr zsh

# Install everything
stow -t ~ .
```

Available packages:
- `zsh/` - Zsh shell configuration (primary shell)
- `bash/` - Bash shell configuration (fallback)
- `git/` - Git configuration
- `nvim/` - Neovim configuration with LazyVim, vim-tmux-navigator, and transparent theme
- `tmux/` - Tmux terminal multiplexer with vim-tmux-navigator and plugin manager
- `hypr/` - Hyprland window manager
- `uwsm/` - Wayland session environment
- `waybar/` - Status bar
- `alacritty/` - Terminal emulator
- `ghostty/` - Terminal emulator
- `kitty/` - Terminal emulator
- `fuzzel/` - Application launcher
- `walker/` - Alternative launcher
- `starship/` - Prompt configuration
- `eza/` - ls replacement (preserves Omarchy theme symlinks)
- And more...

## Extra Packages

Beyond the base Omarchy-Mac installation, you've installed additional packages tracked in `packages.txt`:

```bash
# Install all extra packages during reinstall
pacman -S $(cat ~/projects/omarchy-macdots/packages.txt)
```

The `packages.txt` file documents 40+ explicitly installed packages beyond base Omarchy-Mac, including:
- Shells & terminal tools (zsh, tmux, cliphist)
- Editors (zed, vim, nano)
- Development tools (openssh, uv)
- System utilities (net-tools, networkmanager)
- Alternative package managers (paru)

See `packages.txt` for the complete annotated list.

### Notes

- **Theme switching**: Eza and other configs maintain symlinks to Omarchy's theme system, so theme switching works seamlessly
- **Auto-generated files**: Files like `lazy-lock.json` are gitignored to prevent tracking auto-generated content
- **UWSM environment**: Environment variables are properly sourced for Wayland session
- **Tmux special case**: Tmux uses individual symlinks instead of full stow (see [CHANGELOG.md](./CHANGELOG.md) for why). Config files point to dotfiles repo, but `~/.config/tmux/plugins/` is a real directory managed by tpm
- **Vim-Tmux integration**: Neovim and Tmux both include `vim-tmux-navigator` plugin, enabling seamless `Ctrl+hjkl` navigation between editor splits and terminal panes

### Verification After Installation

```bash
# Verify symlinks point to dotfiles repo
ls -la ~/.config/nvim/init.lua

# Check UWSM reads environment variables
systemctl --user show-environment | grep HYPR

# Test Hyprland config loads
hyprctl version
```

### Updating

Just edit files in the repo—changes take effect immediately due to symlinks.

Commit changes to git to track your customizations.

## Reinstall from Scratch

If you need to replicate this setup on a new machine:

1. **Install Asahi Linux** and **Omarchy-Mac** following standard procedures
2. **Clone this dotfiles repo:**
   ```bash
   git clone https://github.com/jamie/omarchy-macdots ~/projects/omarchy-macdots
   ```
3. **Install extra packages:**
   ```bash
   pacman -S $(cat ~/projects/omarchy-macdots/packages.txt)
   ```
4. **Apply dotfile configurations:**
   ```bash
   cd ~/projects/omarchy-macdots
   stow -t ~ .
   ```
5. **Verify everything:**
   ```bash
   # Check symlinks are created
   ls -la ~/.config/nvim/init.lua

   # Test Hyprland
   hyprctl version

   # Verify shell
   echo $SHELL
   ```

You're now at feature parity with your previous installation. All customizations, keybindings, and environment are restored from git.
