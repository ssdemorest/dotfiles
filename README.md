# 📄 README.md for your Dotfiles

## 🚀 1. The Homebrew & Chezmoi Workflow

Since autoCommit and autoPush are enabled, your workflow is highly automated.

To update your packages and save them:

1. **Update your actual state**: Install or remove packages via `brew install` or `brew uninstall`.
2. **Update the Brewfile**: Run `brew bundle dump --global --force`.
3. **Sync to Remote**: Run `chezmoi add ~/.Brewfile`.

Because of your config, this will automatically commit and push the new Brewfile to your remote.

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

On Bazzite, you want to ensure your sharpening variables and Brew paths are loaded.

### Check Environment Variables (Sharpening)

Run `printenv | grep QT`. You should see:
```
QT_SCALE_FACTOR_ROUNDING_POLICY=Round
QT_FONT_DPI=120  # (if set)
```

### Check Brew Integrity

Run `brew doctor`. This ensures the `/home/linuxbrew` path is healthy.

### Verify Chezmoi State

Run `chezmoi verify`. If it exits with no output, your home directory exactly matches your tracked config.

## 📥 How to add this README to your Remote

To ensure this README stays with your code but doesn't clutter your actual `$HOME` directory, follow these steps:

1. Create the file in the chezmoi source directory
2. Add it to version control
3. It will be tracked but not applied to your home directory