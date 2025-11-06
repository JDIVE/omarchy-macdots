# Changelog

Complete record of all modifications and additions to the omarchy-macdots configuration.

---

## 2025-11-06: Migrate from Bash to Zsh Shell

**Problem**: Currently using Bash as the default shell. Zsh offers better interactive features, superior tab completion, history sharing across sessions, and powerful shell expansion features that are commonly used in modern development workflows.

**Solution**: Complete migration to Zsh with feature parity to macOS setup

### Changes Made:

**1. Created Zsh Configuration Package**
- **Location**: `zsh/` stow package
- **File**: `zsh/.zshrc` (~12KB comprehensive shell configuration)
- Adapted from macOS dotfiles (`~/projects/macdots/home/.zshrc`)

**2. Key Features of Zsh Config**:
- **Environment Variables**: EDITOR, VISUAL, PAI_HOME, FZF configuration
- **Shell Options**: History sharing, ignore duplicates, proper history management
- **Aliases**:
  - Navigation (cd shortcuts)
  - File listing (eza aliases with git status and icons)
  - Git shortcuts (gs, ga, gc, gp, gl, gd, etc.)
  - System management (pacman, systemd shortcuts)
  - Docker shortcuts (dc, dps, dexec, dlogs, etc.)
  - Enhanced tools (cat→bat, top→btop, find→fd)
  - Wayland clipboard support (wl-copy/wl-paste with xclip fallback)
- **Functions**:
  - `mkcd()` - Create directory and enter it
  - `extract()` - Universal archive extractor
  - `assist()` - Launch Claude Code in ~/assistant
  - `lyra()` - Launch Claude Code in ~/lyra
- **Plugins**:
  - zsh-autosuggestions (from pacman)
  - zsh-syntax-highlighting (from pacman)
  - FZF integration (file fuzzing and completion)
  - Starship prompt initialization
- **Key Bindings**: Tab and Right Arrow for accepting autosuggestions
- **Mise Integration**: Automatic activation for version management

**3. Linux-Specific Adaptations**:
- Replaced `pbcopy` (macOS) with `wl-copy` (Wayland) and `xclip` (X11 fallback)
- Replaced `ipconfig getifaddr` (macOS) with `hostname -I` (Linux)
- Replaced `lsof` (macOS ports command) with `ss -ltnp` (Linux)
- Removed `showfiles`/`hidefiles` (Finder-specific)
- Removed `flushdns` (macOS DNS-specific)
- **Added Arch/Linux-specific aliases**:
  - `sysupdate` - System update with pacman
  - `pacin`, `pacrem`, `pacsearch`, `pacinfo`, `paclist` - Pacman commands
  - `systemstatus`, `servicestart`, `servicestop`, etc. - Systemd management
  - `userstart`, `userstop`, `userstatus` - User-level systemd services

**4. Updated UWSM Configuration**
- **File**: `uwsm/.config/uwsm/env`
- Changed: `mise activate bash` → `mise activate zsh`
- Ensures mise tools work correctly with zsh session

**5. Updated Documentation**
- **README.md**:
  - Added `zsh/` to top of package list (primary shell)
  - Updated stow command examples to use `-t ~` target flag
  - Updated example installations to reference zsh instead of bash
- **Created CHANGELOG.md**: This file for tracking future modifications

### Plugin Installation:

Installed via pacman (Arch Linux):
```bash
sudo pacman -S zsh zsh-autosuggestions zsh-syntax-highlighting
```

### Integration with System:

- Plugins sourced from `/usr/share/zsh/plugins/` (Arch standard paths)
- Conditional loading for optional components (FZF, Starship)
- Fallback to standard tools if enhanced versions unavailable
- Full compatibility with Wayland (primary) and X11 (fallback)

### Stow Integration:

- Package created with proper stow structure
- Symlink: `~/.zshrc` → `projects/omarchy-macdots/zsh/.zshrc`
- Installation: `stow -t ~ zsh`
- Works alongside bash package (kept as fallback)

### Bash Fallback:

- **Status**: Bash package (`bash/`) kept but not used as default
- **Rationale**: Preserves ability to use bash if needed for compatibility
- **Future**: Can be removed once zsh is fully proven on the system

### Testing:

Verified the following:
- ✅ Config loads without errors
- ✅ All aliases work correctly
- ✅ Functions (mkcd, extract) work
- ✅ Clipboard aliases resolve correctly (wl-copy/wl-paste)
- ✅ Git aliases available and functional
- ✅ Pacman and systemd aliases available
- ✅ Plugins load from correct paths
- ✅ Mise activation works with zsh

### Impact:

- Improved shell interactive experience with autosuggestions
- Better history management and search
- Unified configuration between macOS and Linux setups
- Full tab completion with fzf integration
- Modern prompting with Starship
- Linux/Arch-native plugin management

### Future Considerations:

1. **Shell as Default**: To make zsh the login shell:
   ```bash
   chsh -s $(which zsh)
   ```

2. **If Removing Bash**: Once confident, `stow -D bash` to remove backup

3. **Plugin Updates**: Plugins installed via pacman, so updates come through `pacman -Syu`

4. **Custom Functions**: Additional functions can be added to `~/.zshrc` in the stow package

---

## 2025-11-06: Add Scratchpad Workspace and Floating Window Overlay Features

**Problem**: Missing workflow features from Omarchy v3.1.0 that improve window management - no way to quickly access a temporary overlay workspace (scratchpad) or pin windows across workspaces for reference material.

**Solution**: Implement two new Hyprland features:
1. Scratchpad workspace (overlay workspace for temporary windows)
2. Floating window overlay (pin windows across all workspaces)

### Changes Made:

**1. Modified Hyprland Bindings**
- **File**: `hypr/.config/hypr/bindings.conf`
- **Added**:
  - `Super + S` - Toggle scratchpad workspace on/off
  - `Super + Alt + S` - Move focused window to scratchpad
  - `Super + O` - Pin active window as floating overlay (toggles floating + pin)
- **Removed**:
  - `Super + O` - Obsidian launcher (unused, not installed)

**2. How It Works**:

**Scratchpad Workspace**:
- `Super + S` shows/hides a special overlay workspace without switching away from current workspace
- `Super + Alt + S` moves the focused window to the scratchpad
- To move windows out of scratchpad, use normal workspace controls (e.g., `Super + Shift + 1` to move to workspace 1)
- Perfect for: temporary notes, calculations, quick references

**Floating Window Overlay**:
- `Super + O` toggles a window between tiled/floating and pins it across all workspaces
- Pinned windows appear on every workspace, allowing quick reference while working
- Press `Super + O` again to unpin (window remains floating)
- Use `Super + V` (existing binding) to just toggle floating without pinning
- Perfect for: reference material, chat windows, documentation, small utilities

**3. Keybinding Notes**:

- Used `Super + O` (Overlay mnemonic) for floating window pin feature as planned in Omarchy v3.1+
- `Super + S` was chosen for consistency with "Scratchpad" first letter
- Keybindings use Hyprland's native dispatchers: `togglespecialworkspace` and `pin`
- Removed unused Obsidian keybind (`Super + O`) from application bindings

### Verification:

Test the features by:
1. Reload Hyprland: `hyprctl reload` (or `Super + Shift + R` if configured)
2. Test scratchpad:
   - Open any window
   - Press `Super + Alt + S` to move it to scratchpad
   - Press `Super + S` to toggle scratchpad visibility
   - Press `Super + S` again to hide scratchpad
3. Test floating overlay:
   - Open a window
   - Press `Super + O` to float and pin it
   - Switch workspaces - the pinned window follows
   - Press `Super + O` again to unpin

### Impact:

- **Workflow improvement**: Quick access to overlay workspace for temporary items without workspace switching
- **Reference material**: Can now pin windows (like documentation, chat) across all workspaces
- **Backward compatible**: All existing keybindings and workflows unchanged
- **Hyprland-native**: Uses built-in Hyprland features, no custom scripts needed

### Future Considerations:

1. **Multiple Scratchpads**: Could add additional scratchpads (e.g., Super + D for dropdown terminal, Super + M for music)
2. **Auto-launch**: Could configure specific apps to auto-launch in scratchpad on first toggle
3. **Window Rules**: Could add workspace rules to auto-float/pin specific window types
4. **Theming**: Scratchpad windows could have distinct visual styling if desired

---

## Future Changes

When making new changes, document them in this file with:
- **Date** (YYYY-MM-DD)
- **Problem**: What issue or improvement is being addressed
- **Solution**: How the change solves the problem
- **Modified Files**: List all affected files with paths
- **Verification**: How to test the change works
- **Impact**: What changes in behavior or functionality
