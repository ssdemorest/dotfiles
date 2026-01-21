# About dotfiles repo

## 🚀 1. The Homebrew & Chezmoi Workflow

A Highly automated workflow for maintaining brew and dotfile state w/ Chezmoi

To update your packages and save them:

1. **Update your actual state**: Install or remove packages via `brew install` or `brew uninstall`.
2. **Update the Brewfile**: Run `brew bundle dump --global --force`.
3. **Sync to Remote**: Run `chezmoi add ~/.Brewfile`.

Because of the Chezmoi configuration toml, this will automatically commit and push the new Brewfile to remote.

## ⏪ 2. Restoring & Rolling Back State

If you want to go back to a previous set of packages or a specific "known good" state:

### Option A: Reverting to the latest Remote state

If you messed up locally and want to reset to what's on GitHub:

```bash
chezmoi update  # Pulls from remote and applies
brew bundle install --global --cleanup  # Installs missing and removes packages not in the Brewfile
```

### Option B: Rolling back to a specific Git Commit

```bash
chezmoi cd
git checkout <commit-hash> dot_Brewfile
exit
chezmoi apply ~/.Brewfile
brew bundle install --global --cleanup
```

## 🛠️ 3. Managing Chezmoi Daily

| Command | Action |
|---------|--------|
| `chezmoi status` | See what has changed in your home dir vs. your repo |
| `chezmoi diff` | See line-by-line changes before applying |
| `chezmoi apply` | Push changes from your repo to your home directory |
| `chezmoi add <file>` | Start tracking a new file (or update a changed one) |
| `chezmoi cd` | Open a shell in the source directory for manual Git work |

## ✅ 4. Startup & Validation

`~/.local/bin/auto-update.sh` handles regular updates to the brewfile, so the remote is regularly updated with new versions (commits) when the brew state changes.
This update script also handles updates for flatpaks and yay-managed packages in the Arch distrobox. `auto-update.sh` is managed via the `distrobox-flatpak-update.service` located in `~/.config/systemd/user`

### Check Brew Integrity

Run `brew doctor`. This ensures the `/home/linuxbrew` path is healthy.

### Verify Chezmoi State

Run `chezmoi verify`. If it exits with no output, your home directory exactly matches your tracked config.

## 🔧 5. Custom Systemd Services

### btrfs-scrub.service
A monthly maintenance service that automatically performs Btrfs filesystem integrity checks and cleanup:

- **Purpose**: Runs `btrfs scrub` on `/var` to detect and repair data corruption
- **Balance Check**: Monitors unused space (>10GB threshold) and notifies if a balance is recommended
- **Notification**: Sends desktop notifications with completion status and recommendations
- **Script**: Located at `~/.local/bin/btrfs-maint.sh`

### filen-mount.service
Automatically mounts Filen cloud storage as a local filesystem using rclone:

- **Purpose**: Mounts Filen cloud storage to `~/FilenDrive` using rclone
- **VFS Caching**: Uses full caching mode with 10GB max cache and 24-hour cache age
- **Network Dependency**: Waits for network connectivity before mounting
- **Graceful Shutdown**: Properly handles unmounting with 5-minute timeout to ensure uploads complete
- **Auto-restart**: Restarts on failure to maintain consistent cloud storage access
