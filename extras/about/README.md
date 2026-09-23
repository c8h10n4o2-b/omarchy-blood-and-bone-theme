# Optional skull About logo

Add a bone-white skull inside the Omarchy outline to the About screen. This is an **optional, global user customization**, not an automatically applied theme feature. It remains when switching themes, and removing the theme does not undo it.

Tested with Omarchy 4.0.4-1, Fastfetch, Foot and a 9pt terminal font. The supplied `about.txt` is 54 columns × 26 rows and contains no escape sequences. Keep colour settings in Fastfetch: embedded ANSI sequences are counted by this Omarchy version's About sizing code and can produce an extremely wide window.

## Get the artwork

A URL-based theme installation clones this folder into your theme directory. Marketplace installations may omit optional extras; if the folder is missing, download [about.txt](https://raw.githubusercontent.com/c8h10n4o2-b/omarchy-blood-and-bone-theme/main/extras/about/about.txt) separately, or clone the repository into a separate folder. Adjust the source path in the copy command below accordingly.

## 1. Save your existing settings

Close About before making changes. In Bash, run:

```bash
backup_dir="$HOME/.local/state/blood-and-bone-about-$(date +%Y%m%d-%H%M%S)"
mkdir -p "$backup_dir"
cp "$HOME/.config/omarchy/branding/about.txt" "$backup_dir/about.txt"
cp "$HOME/.config/hypr/hyprland.lua" "$backup_dir/hyprland.lua"
if [ -f "$HOME/.config/fastfetch/config.jsonc" ]; then
  cp "$HOME/.config/fastfetch/config.jsonc" "$backup_dir/config.jsonc"
else
  touch "$backup_dir/no-user-fastfetch-config"
fi
printf 'Keep this backup path for rollback: %s\n' "$backup_dir"
```

## 2. Install the skull

```bash
cp "$HOME/.config/omarchy/themes/blood-and-bone/extras/about/about.txt" \
   "$HOME/.config/omarchy/branding/about.txt"
```

## 3. Set the logo to bone white

If you already have a user Fastfetch configuration, edit it in place and preserve your modules and other custom settings. Otherwise, create one from your own system's defaults:

```bash
mkdir -p "$HOME/.config/fastfetch"
if [ ! -f "$HOME/.config/fastfetch/config.jsonc" ]; then
  cp /etc/fastfetch/config.jsonc "$HOME/.config/fastfetch/config.jsonc"
fi
```

In `~/.config/fastfetch/config.jsonc`, edit the existing `logo` object to use these values (this is a fragment, **not a replacement for the complete configuration**):

```json
"logo": {
  "type": "file",
  "source": "~/.config/omarchy/branding/about.txt",
  "color": { "1": "38;2;230;223;207" },
  "padding": { "top": 2, "right": 6, "left": 2 }
}
```

The RGB value is bone white `#e6dfcf`. This changes the logo colour without changing the terminal's green palette entry. A copied user configuration takes precedence over future system Fastfetch defaults; you maintain this copy yourself.

## 4. Give About enough room

**Required with this Omarchy version:** the About launcher disables automatic sizing when `~/.config/fastfetch/config.jsonc` exists. Without a size override, its default 920×480 window can clip this layout.

Add the following at the end of `~/.config/hypr/hyprland.lua`, after Omarchy's defaults and personal modules:

```lua
-- BEGIN Blood and Bone optional About size
-- Adjust for your terminal font and information layout.
o.window("^org[.]omarchy[.]about$", { size = { 920, 650 }, center = true })
-- END Blood and Bone optional About size
```

This uses Omarchy's user configuration helper and affects only the About window. Do not add a second copy if you already have an About size override; update that rule instead. The 920×650 size worked with the tested 9pt font. Increase it for larger fonts or a longer module list; this is not automatic content fitting. This Lua example is documentation only and is not shipped as an executable theme file.

Validate, then reopen About:

```bash
hyprctl reload
hyprctl configerrors
omarchy launch about
```

If `configerrors` reports anything, correct or remove the added rule before continuing. These instructions target Omarchy's Lua configuration; do not paste the rule into older Hyprland `.conf` files.

## Rollback

Set `backup_dir` to the exact path printed in step 1. Close About, then:

```bash
# Replace this example with your recorded backup directory.
backup_dir="$HOME/.local/state/blood-and-bone-about-YYYYMMDD-HHMMSS"
if [ -f "$backup_dir/about.txt" ]; then
  cp "$backup_dir/about.txt" "$HOME/.config/omarchy/branding/about.txt"
  if [ -f "$backup_dir/config.jsonc" ]; then
    cp "$backup_dir/config.jsonc" "$HOME/.config/fastfetch/config.jsonc"
  elif [ -f "$backup_dir/no-user-fastfetch-config" ]; then
    rm -f "$HOME/.config/fastfetch/config.jsonc"
  fi
else
  printf 'Backup not found; no files changed.\n'
fi
```

Remove the `BEGIN`/`END Blood and Bone optional About size` block from your user `hyprland.lua`, then run `hyprctl reload` and `hyprctl configerrors`. The backed-up `hyprland.lua` is a reference; avoid restoring the entire file if you have made unrelated edits since installation. Likewise, merge the old Fastfetch settings manually if you have since added other customizations. Reopen About to verify the original appearance.

No files under `/usr/share/omarchy` or `/etc` need modification. The artwork is covered by the repository's MIT license.
