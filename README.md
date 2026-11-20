# NVMM

My fork of [NVForever](https://github.com/LootJunkieAzza/NVForever). I mainly changed the launcher's Mods page so I can keep mods in separate folders and see which copy of a file actually gets used.

This isn't the official launcher, and it isn't Mod Organizer 2. NVForever still runs off the data files from your own copy of Fallout: New Vegas. This fork doesn't ship any Bethesda content.

NVForever is not affiliated with or endorsed by Bethesda Softworks, ZeniMax Media, Microsoft or Obsidian Entertainment.

## Why this exists

I wanted a Linux mod manager that could handle loose file folders the way I actually use mods, and I plan on this getting more fleshed out into NVForever having its own proper mod manager.

## Mods page

Left side is mod folders. Right side is plugins.

- Put mods in separate folders under NVForever's mods folder. Don't drop those folders into the game's Data folder.
- Drag a folder to move it. The list goes from top to bottom. The game checks the bottom folder last.
- Checking a folder turns that folder's plugins on. The number is how many plugins are in the folder. There's no limit.
- If two checked folders have the same file, the lower folder's copy wins. The higher one's gets skipped.
- **Show overwrites** shows that under the folder. Green means this mod's copy is the one being used. Red means this mod's copy is skipped. Same result always gets the same color. Hover a note to see the file names.
- **Find a mod** and **Find a plugin** filter the lists. Click a plugin and its folder gets marked.
- If a plugin shows up in more than one checked folder, the row tells you which copy is used and which ones are skipped.
- **Refresh** re-reads the mods folder.
- **Save** writes NVForever's own `plugins.txt` and `mod-order.txt`. **Undo changes** reloads them. It doesn't touch the original game's mod list.
- **Apply** saves those same lists. It asks first. It doesn't copy any files — they stay in their folders.
- **Save backup** stores the folder order and plugin list without changing what the game is currently using. **Max backups kept** controls how many of those it holds. **Load backup** puts one back into the list. **Save** keeps it.
- **Open mods folder** opens that folder.
- The new-game start box on this page gets saved to `NVForever.ini`. If Tale of Two Wastelands is in the list, there's a button that fills in its start quest and turns the intro film off.

Inside NVForever's own home, two directories whose names only differ by capital letters get joined together. The game's Data folder is never written. Set `NVFOREVER_NO_CASEFOLD` if you want to skip that.

## What it will not do

- It doesn't write into the game folder.
- It doesn't edit the original game's mod list.
- It doesn't install Mod Organizer 2, and it doesn't read an MO2 profile. If you use MO2, just start NVForever from MO2 instead.
- It doesn't magically make a mod work if NVForever doesn't support it yet. Script extenders are still whatever upstream has.
- I use this on my machine. I haven't run upstream's test list or smoke checks.

## Building

Needs [Rust](https://rustup.rs). These are two separate builds. The launcher looks for the game next to itself, then falls back to `engine/target/release/nvforever`.
