# omarchy-themes-macos

[Omarchy](https://omarchy.org)-style theme switching for macOS.

`omarchy-theme` downloads the official themes from [basecamp/omarchy](https://github.com/basecamp/omarchy) and applies them across your setup with one command, the way `omarchy-theme-set` does on Arch:

- **Ghostty** – colors, live-reloaded in open windows
- **Neovim** (LazyVim) – the theme's colorscheme
- **btop** – the theme, live-reloaded if btop is running
- **Wallpaper** – the theme's backgrounds, with cycling
- **macOS appearance** – light or dark, to match the theme

## Install

Requires macOS, `git` and `zsh` (the macOS default shell).

```sh
git clone https://github.com/matteogaldi/omarchy-themes-macos.git
cp omarchy-themes-macos/omarchy-theme ~/.local/bin/   # or anywhere on your PATH
omarchy-theme set tokyo-night
```

The first run downloads the themes. The first time it sets the wallpaper or appearance, macOS asks to let your terminal control "System Events". Allow it, or turn it on later under System Settings › Privacy & Security › Automation.

## Usage

```sh
omarchy-theme list               # all themes, * marks the current one
omarchy-theme set tokyo-night    # or "Tokyo Night"
omarchy-theme next               # switch to the next theme in the list
omarchy-theme current            # print the current theme
omarchy-theme bg-next            # next wallpaper for the current theme
omarchy-theme update             # pull the latest themes and re-apply the current one
omarchy-theme install <git-url>  # add a community theme
omarchy-theme remove <theme>     # remove a community theme
```

## How it works

Each Omarchy theme is a directory with a `colors.toml` palette. Omarchy builds every app's config from that palette using its templates in `default/themed/`. `omarchy-theme` uses the same templates, so new and updated themes work after an `update` without changes to the script.

On `set`, the script renders the theme into `~/.local/state/omarchy-theme/current/` and points each app at it:

| App | What changes |
| --- | --- |
| Ghostty | Your `theme =` line is commented out and a `config-file =` include for the generated colors is appended to `~/Library/Application Support/com.mitchellh.ghostty/config.ghostty`. Ghostty reloads on `SIGUSR2`. |
| Neovim | `~/.config/nvim/lua/plugins/omarchy-theme.lua` is linked to the theme's plugin spec. Running instances are told to switch colorscheme, which works if the theme's plugin is already installed; otherwise it applies the next time Neovim starts. |
| btop | The theme is installed as `~/.config/btop/themes/omarchy-current.theme` and `color_theme` is set in `btop.conf`. btop reloads on `SIGUSR2`. |
| Wallpaper | The theme's first background is converted to PNG (cached in `~/.local/share/omarchy-theme/wallpapers/`) and set on every desktop. |
| Appearance | macOS dark mode follows the theme's `mode`. |

Apps that aren't installed are skipped.

### Files

| Path | Contents |
| --- | --- |
| `~/.local/share/omarchy-theme/omarchy/` | Sparse clone of the Omarchy repo (themes and templates only) |
| `~/.config/omarchy-theme/themes/` | Community themes |
| `~/.local/state/omarchy-theme/current/` | The rendered current theme |
| `~/.local/state/omarchy-theme/backup/` | Your original Ghostty config and `btop.conf`, saved the first time they're changed |

## Community themes

```sh
omarchy-theme install https://github.com/someone/omarchy-some-theme.git
```

A theme must use the current format, with a `colors.toml` at its root. Like Omarchy, the script ignores a community theme's own `neovim.lua` and terminal configs, because those can run code, and builds them from its colors instead. A community theme with the same name as an official one takes precedence. `update` also pulls installed community themes.

## Uninstall

1. Restore your configs from `~/.local/state/omarchy-theme/backup/` (or remove the `config-file =` line from your Ghostty config and set `color_theme` back in `btop.conf`).
2. Remove `~/.config/nvim/lua/plugins/omarchy-theme.lua`.
3. Delete `~/.local/share/omarchy-theme`, `~/.local/state/omarchy-theme` and `~/.config/omarchy-theme`.

## Credits

Themes and templates come from [Omarchy](https://github.com/basecamp/omarchy) by Basecamp. This project only applies them on macOS.
