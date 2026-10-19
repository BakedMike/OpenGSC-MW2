# OpenGSC [MW2]
<img width="270" height="158" alt="menu" src="https://github.com/user-attachments/assets/5dc14adc-6481-40ae-8783-f2d12755b2d3" />

An Xbox 360 plugin (xex/DLL) that loads precompiled GSC menus into **Modern Warfare 2 (TU9)** straight from the console's hard drive. No PC connection is needed at runtime.

It is a native C++ port of the *loading* half of the [OpenRTM](https://github.com/Avexiis/OpenRTM) GSC injector. Compiling GSC source is **not** part of this project: you bring a ready-made `.gscbin` file.

## Features

- Loads `.gscbin` scripts from `hdd:\GSC\MW2\`.
- In-game list menu (top left) showing every `.gscbin` in that folder.
- Load and unload a script from the menu.
- Status tags next to the file name: `(Loading)`, `(Loaded)`, `(Unloading)`.
- Safe by design: the script is installed only while you are in the main menu. If you pick a script mid-match it is queued and loaded after you leave.
- XNotify messages for every step and every failure, with the reason.

## Usage

1. Copy your compiled scripts to the console:

   ```
   hdd:\GSC\MW2\YourMenu.gscbin
   ```

2. Load the plugin into MW2.
3. Hold **LT** and press **D-Pad Right** to open the menu.

| Button | Action |
|---|---|
| D-Pad Up / Down | Move Up / Down |
| A on a file | Expands to Load / Unload button |
| A on `Load GSC` (green) | Load the script |
| A on `Unload GSC` (red) | Unload the script |
| B | Collapse the entry, or close the menu |

While the menu is open, controller input is blocked for the game.

Loaded scripts start on the next script load (map load or `fast_restart`). Unloading is also deferred until the game's script system has shut down, so it can never crash a running match.

## How it works

1. The `.gscbin` is read and decoded back into game bytecode (strings and custom field names are re-linked).
2. A small hook is placed on the game's script loader (`Scr_SetLoading`). The game's string hash table is moved to a private pool so the script image can live at its fixed address.
3. The game's own string functions register the script's strings, and the script's entry function runs on every script load.
4. Every write is verified by reading it back and can be rolled back. The script lock of the game is held while patching.

## Building

- Xbox 360 SDK (XDK) with Visual Studio 2010
- [xkelib](https://github.com/cOzInABox/xkelib) (included in this repository)
- Open the solution, build `module` in Release, and sign/deploy the resulting xex for your console setup.

## Notes

- Target: MW2 multiplayer, title update 9. All addresses are specific to this build.
- Scripts larger than 384 KB are rejected.
- Failure details (such as file access errors) are shown in the XNotify message.

## Credits

- **[OpenRTM](https://github.com/Avexiis/OpenRTM)** for the Java GSC injection logic that was ported to C++ here.
- **cOzInABox[cOz]** for xkelib.
- **Xx jAmes t xX** for the Detour class.
