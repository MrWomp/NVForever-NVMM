# NVMM

A fork of [NVForever](https://github.com/LootJunkieAzza/NVForever) where I changed the launcher's Mods page so I could manage mod folders myself.

This is a barebones attempt. It is not the official launcher, and it is not a replacement for Mod Organizer 2. NVForever still runs on the data files of your own copy of Fallout: New Vegas. This fork ships no Bethesda content.

NVForever is not affiliated with or endorsed by Bethesda Softworks, ZeniMax Media, Microsoft or Obsidian Entertainment.

## Why this exists

I wanted a way to manage mods in the launcher. The Mods page only knew about `plugins.txt`, and a lot of what I use is a folder of loose files, not one plugin dropped in Data. I don't know this codebase, so I had Grok poke at it until it did what I needed on my machine.

Use whatever is useful and leave the rest.

## What changed

- The Mods page is split in two. Folders on the left, plugins on the right.
- Every folder in the launcher's mods folder shows up on the left. Drag a row to change its order. A folder lower in the list overwrites one above it.
- **Apply** copies that mod's loose files into the mods folder. It skips `.esm` and `.esp` files, so Apply does not turn a plugin on.
- The plugin list is still the load order. Opening the page looks through the mods once. **Refresh** looks again.
- A plugin buried inside a mod folder is found, not only one sitting loose in the mods folder.
- If a plugin is only on another drive, it is linked into the mods folder. Nothing is written into the game's Data folder.
- Folder names inside NVForever's own folders are matched without caring about capital letters. The game's Data folder is left alone.
- The bar along the bottom can be dragged shorter. That height is remembered the next time the launcher opens, and the buttons shrink with it.

There is no help text on the page.

## Using it

1. Own Fallout: New Vegas and point the launcher at that install, the same way stock NVForever does.
2. Put each mod in its own folder under the launcher's mods folder. Do not put them in the game's Data folder.
3. Open **Mods**. The left list is those folders. Drag them into the order you want.
4. Press **Apply**. Loose files are copied into the mods folder. Plugins are not enabled by this.
5. On the right, turn on the plugins you actually want and put them in load order.
6. If you add, remove, or rename a mod folder, press **Refresh**.

A mod that is only a plugin still belongs on the right. Apply is for the folders of meshes, textures, and other loose files.

## What it will not do

- It does not write into the game folder.
- It does not edit the original game's mod list.
- Apply does not tick a plugin or change `plugins.txt`.
- It does not install Mod Organizer 2, and it does not read an MO2 profile.
- It does not make a mod work if NVForever does not support that mod yet. Script extenders are still whatever upstream has built. This fork does not change the engine's mod support.
- It is not finished. I use it. I have not run the project's test list or smoke checks.
