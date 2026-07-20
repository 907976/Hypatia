# Castle Crashers Remastered Save Editor (CCSaveEditor)

A comprehensive Python-based save game editor for **Castle Crashers Remastered** (PC/Steam and Goldberg Emulator environments). 

Unlike legacy tools that only edit unencrypted Xbox 360 files, this tool dynamically decrypts, modifies, signs, and re-encrypts modern Steam `cc_save.dat` files using cryptographic routines matched to the game engine.

---

## Features

### 1. Character Progression & Stats (Per-Character)
*   **Unlock State**: Toggle the unlock status for all 42 character slots.
*   **Level & XP**: Set character levels (1–99) and modify experience points.
*   **Attributes**: Adjust Strength, Defense, Magic, and Agility parameters (0–25 cap).
*   **Currency**: Set gold amount individually per character.

### 2. Equipment & Items (Per-Character)
*   **Weapons**: Equipped weapon dropdown mapping all 70+ standard and DLC weapons.
*   **Pets**: Equipped pet dropdown mapping all 29 animal orbs.
*   **Consumables**: Set inventory limits for Potions, Bombs, and Sandwiches.

### 3. Story Relics & Badges
*   **Story Progress**: Set normal and insane checkpoints (from Checkpoint 0 to Final Battle).
*   **Relics**: Toggle normal and insane story items (Compass, Steering Wheel, Telescope, Horn).
*   **Completion Badges**: Assign White Skulls (Normal completion) or Gold Skulls (Insane completion).

### 4. Global Profile Settings
*   **Global Unlocks**: Bulk unlock all main/DLC weapons, all animal orbs, and multiplayer battle arenas.
*   **Settings Editor**: Toggle game options like Gore/Blood, controller vibration, and adjust music volume.
*   **Mini-game High Scores**: Edit the *Back Off Barbarian* high score time (in seconds) and configure which character set the record.

---

## Technical Details

Castle Crashers Remastered protects save integrity through two mechanisms:
1.  **Dynamic Blowfish Encryption**: The file is encrypted using Blowfish in ECB mode. The 24-byte decryption key is dynamically built at runtime using the player's 17-digit **Steam ID64**. Standard Blowfish operates on big-endian blocks, whereas the game operates on little-endian uint32s, requiring 4-byte chunk byte-swapping during encryption and decryption.
2.  **Rolling Checksum**: The last 4 bytes of the decrypted file contain a custom rolling XOR checksum initialized to `0xD971`. Any manual hex edits without updating this checksum will cause the game to flag the file as corrupted and reset progress.

This tool automatically resolves both protections on save.

---

## Prerequisites

Ensure you have Python 3.x installed along with the `cryptography` library for Blowfish operations:

```bash
pip install cryptography
```

---

## Usage

The project provides both a native desktop Graphical User Interface (GUI) and a Command Line Interface (CLI).

### 1. Graphical User Interface (GUI)
Run the GUI to select, view, and edit save files visually:

```bash
python cc_save_gui.py
```

*Note: On startup, the GUI automatically attempts to resolve your local `cc_save.dat` path and Steam ID from your environment variables and registry.*

### 2. Command Line Interface (CLI)
For scripted or manual terminal modifications:

```bash
# View list of available characters
python cc_save_editor.py <path_to_cc_save.dat> <steam_id>

# View detailed stats for a specific character
python cc_save_editor.py <path_to_cc_save.dat> <steam_id> --character "Red Knight"

# Max out a specific character's stats
python cc_save_editor.py <path_to_cc_save.dat> <steam_id> --character "Red Knight" --level 99 --gold 99999 --xp 120000 --max-stats

# Max out all characters and unlock global assets
python cc_save_editor.py <path_to_cc_save.dat> <steam_id> --boost-all
```

---

## Locating Save Files

On Windows, the save file (`cc_save.dat`) is stored in one of the following directories:

*   **Steam (Standard)**:
    `C:\Program Files (x86)\Steam\userdata\<YourSteamID32>\204360\remote\cc_save.dat`
*   **Goldberg Steam Emulator**:
    `C:\Users\<YourUsername>\AppData\Roaming\GSE Saves\204360\remote\cc_save.dat`

> [!WARNING]
> **Always close Castle Crashers before modifying or saving changes.** The game keeps save data in memory and will overwrite the file on disk during autosaves or when closing the application.

> [!IMPORTANT]
> A backup file (`cc_save.dat.bak`) is automatically generated in your save directory before writing modifications.
