# Spec note: the launcher's mod manager

NVForever's own rules for the Mods page and for what a start without `--mo2`
reads. This is not a claim about the original game, and it is not another
program's deploy step. No code was copied. The game `Data` folder is not
written.

The smaller notes are
[launcher-mod-visibility.md](launcher-mod-visibility.md) (what the game
process reads) and [launcher-profile.md](launcher-profile.md) (a named
profile). `--mo2` is a different, read-only path
([mod-organizer-profile.md](mod-organizer-profile.md)).

## Where a mod lives

[design] Each mod is one folder under the launcher home's `mods` directory.
The home is `~/.local/share/nvforever` on Linux (or `$XDG_DATA_HOME/nvforever`)
and `Documents\My Games\NVForever` on Windows, unless `NVFOREVER_HOME` is set
[documented: `EngineHome::default_root` in `crates/fnv-install/src/lib.rs`].

A mod's meshes, textures, and archives sit in that folder, or in a `Data`
folder inside it when the mod has one [documented: `plugins::mod_content_root`].
They are not copied, hard-linked, or symlinked into the game `Data` folder.

A plugin, archive, or loose file that is already in `Data` is still read. The
mods folder does not hide the base game or a mod that was installed there by
hand [documented: `plugin_path_layered`, `Vfs::open_launcher_mods`].

## The two home lists

[documented: `Launcher::save_mods` and `Launcher::remember_lists`] The launcher
keeps two text files in the home:

- `plugins.txt`: which plugins are on, and their load order. A `*` turns a
  line on. The format is NVForever's own
  [documented: `crates/fnv-install/src/plugins.rs`].
- `mod-order.txt`: which mod folders are on, and their order, in Mod Organizer
  2's `modlist.txt` form, so MO2 can read it [design: every list
  file stays readable by MO2]. One line a row, the **highest priority
  first**: `+name` on, `-name` off, a separator as `-<name>_separator`, CRLF
  line ends, no header [documented: docs/spec-notes/mod-organizer-profile.md,
  `modlist.txt`; `fnv_install::mod_order`]. The first on-line wins a loose-file
  overlap [documented: `plugins::mod_content_layers`]. The page shows that
  winner at the bottom of the list.
- `mod-locks.txt`: the locked mods, one name a line. MO2's list has no lock
  mark, so locks are kept beside it [design].

A `mod-order.txt` in the launcher's earlier form is still read: lowest priority
first, `*` on, a bare name off, `=` locked, `% name` a separator, `;` a comment.
The `;` comment, a `*` line or a bare name marks that form. The next write
turns it into MO2's form [documented: `mod_order::read_mod_order` and its
tests].

Both files are written when the list changes, with no profile open and without
pressing **Save**. Closing the launcher and opening it again restores that
list. A profile is not required for this.

Writing either file does not make a backup. A dated copy is made only when
**Save backup** is pressed [documented: `Launcher::save_dated_backup`].
Lowering the number of backups kept asks before the oldest copies are deleted.
The mods themselves are not deleted.

## Where an install may write

An install writes only inside the new mod's folder [design]. Every path an archive
entry or a FOMOD installer names is taken as plain names under that folder
(`fomod::rel_path`). A path is refused when it holds `..`, starts with `/` or `\`
(a root), or holds `:` (a drive such as `C:`, or a Windows stream name). Then each
file's folder is made and checked to be inside the mod folder once it exists, so a
link already there cannot lead out (`fomod::place_in`). Zip, tar and FOMOD copies
all go through these two checks.

The one plugin link outside an install (`NVFOREVER_PLUGIN_SEARCH`, see
`plugins::link_into_mods`) is refused into the game folder (Data's parent, Data
included) and anywhere `guard_write` refuses: the remembered game install and
`Documents\My Games\FalloutNV`. The variable's folders are split as the system
splits `PATH` (`;` on Windows), so `C:\Mods` stays whole [design].

## What the game is given

The game process does not read `mod-order.txt`. When the launcher starts the
game it passes each enabled folder as `--mod-layer`, lowest priority first
[documented: `launch::spawn`]. The last layer wins.

A start that was not given `--mod-layer` still layers every mod folder, in
name order. Folder on/off is not applied on that path
[documented: `plugins::mod_folder_layers`].

`--mo2` is not this path. It still only reads another program's instance.
The launcher does not write that instance.

## Masters the game would refuse

The game starts without an enabled plugin whose master is not in the load order
or loads after it, and without every plugin that needs a plugin it left out
[documented: `fnv_esm::loadorder::drop_unloadable`, called by the game's
`world::open_plugins`]. The plugin list marks those plugins before the start, so
the player sees it before playing:

- A plugin's masters are the `MAST` names of its header record, in order
  [documented: docs/spec-notes/esm.md, "TES4"; `plugins::read_masters`]. Only
  the 24-byte record header and that record are read.
- A master is **missing** when the game would open no file of that name: none in
  a mod folder that is on, none loose at the top of the mods folder, none in
  Data. The check asks the game's own lookup for this, from the page's rows
  [documented: `plugins::layered_plugin_files`, which the game uses on a start
  through the launcher]. A master whose only copy is in a folder that is off is
  missing.
  A master is **off** when its file is there but it is not ticked. A master
  **loads after** the plugin when it is ticked but lower in the list. A master
  the game leaves out for its own masters makes its dependents go too.
- A header record that is compressed (flag `0x00040000`) or cannot be read is
  shown as "masters not checked". It is never shown as fine, and it is not
  counted as left out [design].
- The check runs again only when the load order, a plugin's on/off or the mod
  folders change; the mod folders are walked again only when the folders' order
  or on/off or the folder scan change, and each header is read once per scan. A
  frame compares a fingerprint of those [design].
- The count by the Load order heading is of plugins left out for their masters.
  A ticked plugin whose own file is in no folder that is on is already greyed on
  its row ("Not in a ticked mod, and not in Data") and is not in that count
  [design].
- Play, Continue and the other starts ask "Play anyway" or "Cancel" while a
  plugin would be left out [design]. Nothing is written by the check.

## A named profile

[documented: docs/spec-notes/launcher-profile.md] **Save** writes
`<home>/profiles/<name>/` and only `modlist.txt`, `plugins.txt`, and
`loadorder.txt`. **Load** puts those lists back on the page. An open profile
overrides the home lists the next time that profile is opened. The home lists
still exist on their own.

## Not in this manager

- The game `Data` folder is not written. Neither is
  `Documents\My Games\FalloutNV`, except the one case already documented for
  an external instance ([documented: `crates/fnv-install` module notes]).
- An `overwrite` folder is not a layer: the game is not given it, so a file
  there does not win over a mod folder [documented: `launch::spawn`,
  `plugins::mod_content_layers`]. The launcher uses `<home>/overwrite` only for
  **Make mod below**, which moves that folder's loose files into a new mod and
  then makes the folder again, empty [documented: `App::make_mod_below`]. Nothing
  else creates it. As the highest layer it is planned, not built
  [design: docs/design/launcher-mods.md].
- Nexus and ModPub downloads are not part of the running launcher.
