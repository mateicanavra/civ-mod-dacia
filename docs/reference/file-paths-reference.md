# Civilization VII File Paths Reference

## Overview

This document provides a comprehensive reference of file paths and directory structures for Civilization VII modding. Understanding these paths is essential for creating, organizing, and implementing mods effectively. The information here covers the base game structure, mod organization patterns, and key file locations by category.

## Game Installation Directories

Civilization VII uses two main directory locations:

### Game Resources Directory (Read-Only)

This is where the base game files are installed and contains all game assets, modules, and definitions:

**macOS:**
```
~/Library/Application Support/Steam/steamapps/common/Sid Meier's Civilization VII/CivilizationVII.app/Contents/Resources/
```

**Windows:**
```
C:\Program Files (x86)\Steam\steamapps\common\Sid Meier's Civilization VII\Base\
```

### Game Data Directory (User-Writable)

This is where user-specific data including saves, logs, and installed mods are stored:

**macOS:**
```
~/Library/Application Support/Civilization VII/
```

**Windows:**
```
C:\Users\[Username]\Documents\My Games\Sid Meier's Civilization VII\
```

For the remainder of this document, we'll use platform-independent path references. When you see:
- `<GAME_RESOURCES>` - This refers to the game resources directory
- `<GAME_DATA>` - This refers to the game data directory

## Base Game Structure

The Civilization VII installation follows a modular structure, with the game itself organized as a collection of modules:

```
<GAME_RESOURCES>/
├── Base/
│   ├── modules/
│   │   ├── base-standard/        # Core gameplay definitions and systems
│   │   ├── age-antiquity/        # Antiquity Age (3000 BCE - 500 CE) content
│   │   ├── age-exploration/      # Exploration Age (500 CE - 1700 CE) content
│   │   ├── age-modern/           # Modern Age (1700 CE - Present) content
│   │   ├── core/                 # Core engine functionality
│   │   └── telemetry/            # Telemetry systems
│   └── Assets/
│       ├── schema/               # Database schema definitions
│       │   ├── gameplay/         # Core gameplay schemas
│       │   ├── ui/               # UI system schemas
│       │   ├── loc/              # Localization schemas
│       │   └── modding/          # Modding framework schemas
│       ├── textures/             # Game textures
│       ├── models/               # 3D models
│       ├── ui/                   # UI assets
│       └── fonts/                # Font files
└── DLC/                          # Official DLC packages
```

## User Data Structure

The user data directory contains user-specific files:

```
<GAME_DATA>/
├── Mods/                         # Installed mods
├── Logs/                         # Game logs (various .csv and .log files)
├── Debug/                        # Debug information
├── Database/                     # Local database files
└── DNA/                          # User profile data
```

## Base-Standard Module Structure

The base-standard module contains the core definitions and systems that all other modules depend on:

```
<GAME_RESOURCES>/Base/modules/base-standard/
├── base-standard.modinfo     # Module definition file
├── package.json              # Package metadata
├── definitions/              # Database schema definition files (.civdb)
│   ├── civilization.civdb    # Civilization schema
│   ├── leader.civdb          # Leader schema
│   ├── unit.civdb            # Unit schema
│   ├── constructible.civdb   # Buildings/improvements schema
│   ├── trait.civdb           # Traits and abilities schema
│   └── ...                   # Other schema definitions
├── data/                     # Core gameplay data files
│   ├── civilizations.xml     # Base civilization definitions
│   ├── leaders.xml           # Leader definitions
│   ├── units.xml             # Unit definitions
│   ├── constructibles.xml    # Building and improvement definitions
│   ├── progression-trees.xml # Tech and civic tree definitions
│   └── ...                   # Other game element definitions
├── text/                     # Localization files
│   ├── en_US.xml             # English localization
│   └── ...                   # Other language files
├── ui/                       # UI definitions and scripts
├── scripts/                  # JavaScript game logic
├── l10n/                     # Additional localization resources
├── maps/                     # Map scripts and definitions
├── metaprogression/          # Progression systems
└── movies/                   # Media resources
```

## Age Modules Structure

Each Age module follows a similar structure, containing content specific to that historical period:

```
<GAME_RESOURCES>/Base/modules/age-[name]/
├── age-[name].modinfo        # Module definition
├── package.json              # Package metadata
├── config/                   # Age-specific configuration
│   └── config.xml            # Core Age properties and settings
├── data/                     # Age-specific game data
│   ├── civilizations.xml     # Civilizations available in this Age
│   ├── leaders.xml           # Leader data
│   ├── units.xml             # Units available in this Age
│   ├── constructibles.xml    # Buildings and improvements
│   ├── progression-trees-*.xml # Tech and civic trees
│   └── ...                   # Other Age-specific content
├── text/                     # Localization files
├── l10n/                     # Additional localization
├── ui/                       # UI element definitions
├── scripts/                  # JavaScript files
├── metaprogression/          # Progression systems
└── movies/                   # Media resources
```

## DLC Module Structure

Official DLC follows this general structure:

```
<GAME_RESOURCES>/DLC/[dlc-name]/
├── [dlc-name].modinfo        # DLC definition
├── package.json              # Package metadata
├── data/                     # DLC-specific data
│   ├── civilizations/        # Added civilizations
│   ├── leaders/              # Added leaders
│   ├── units/                # Added units
│   └── ...                   # Other added content
├── text/                     # Localization
├── ui/                       # UI modifications
└── assets/                   # DLC-specific assets
```

## User Mod Directory Structure

When creating a mod, follow this recommended structure to ensure compatibility and organization:

```
<GAME_DATA>/Mods/your-mod-name/
├── your-mod-name.modinfo     # Mod definition file
├── config/                   # Configuration files (if needed)
├── data/                     # Game data modifications/additions
│   ├── civilizations/        # Civilization definitions
│   ├── leaders/              # Leader definitions
│   ├── units/                # Unit definitions
│   ├── buildings/            # Building definitions
│   └── ...                   # Other game element files
├── text/                     # Localization files
│   ├── en_US/                # English localization
│   └── ...                   # Other languages
├── ui/                       # UI modifications (if applicable)
├── scripts/                  # Custom scripts (if applicable)
└── assets/                   # Custom assets for your mod
    ├── icons/                # Custom icons
    ├── textures/             # Custom textures
    └── models/               # Custom 3D models (if applicable)
```

### Leader Mod Directory Structure

For mods that add new leaders, use this structure:

```
<GAME_DATA>/Mods/your-leader-mod/
├── your-leader-mod.modinfo   # Mod definition file
├── leaders/                  # Leader definitions
│   └── your_leader.xml       # Your custom leader
├── agendas/                  # Agenda definitions
│   └── your_leader_agendas.xml # Leader agendas
├── traits/                   # Trait definitions
│   └── your_leader_traits.xml # Leader abilities
├── text/                     # Localization files
│   └── your_leader_text.xml  # Text strings for your leader
└── assets/                   # Leader assets
    └── icons/                # Leader icons
```

### Civilization Mod Directory Structure

For mods that add new civilizations, use this structure:

```
<GAME_DATA>/Mods/your-civilization-mod/
├── your-civilization-mod.modinfo # Mod definition file
├── civilizations/             # Civilization definitions
│   └── your_civilization.xml  # Your custom civilization
├── units/                     # Unique unit definitions
│   └── your_unique_units.xml  # Civilization's unique units
├── buildings/                 # Unique building definitions
│   └── your_unique_buildings.xml # Civilization's unique buildings
├── traits/                    # Trait definitions
│   └── your_civ_traits.xml    # Civilization abilities
├── text/                      # Localization files
│   └── your_civ_text.xml      # Text strings for your civilization
└── assets/                    # Civilization assets
    └── icons/                 # Civilization icons
```

## TypeScript Modding Tools Output Structure

When using the TypeScript Modding Tools, the output structure follows a more specific organization based on element types:

```
<OUTPUT_DIRECTORY>/
├── your-mod-id.modinfo           # Generated modinfo file
├── civilizations/                # Generated civilization definitions
├── units/                        # Generated unit definitions
├── constructibles/               # Generated building definitions
├── unlocks/                      # Generated unlock definitions
└── imports/                      # Imported assets
```

This structure is directly compatible with the game's mod loading system and can be copied to the game's Mods directory.

## Key File Locations By Category

### Database Schema Files

```
<GAME_RESOURCES>/Base/Assets/schema/gameplay/01_GameplaySchema.sql   # Core gameplay schema
<GAME_RESOURCES>/Base/Assets/schema/modding/schema-modding-10.sql    # Modding framework schema
<GAME_RESOURCES>/Base/Assets/schema/icons/IconManager.sql            # Icon management schema
<GAME_RESOURCES>/Base/Assets/schema/loc/schema-loc-10.sql            # Localization schema
<GAME_RESOURCES>/Base/Assets/schema/worldbuilder/MapSchema.sql       # World builder map schema
<GAME_RESOURCES>/Base/Assets/schema/frontend/FrontendSchema.sql      # Frontend UI schema
```

### Definition Files

```
<GAME_RESOURCES>/Base/modules/base-standard/definitions/*.civdb      # Core game element schemas
```

### Civilization Files

```
<GAME_RESOURCES>/Base/modules/base-standard/data/civilizations.xml   # Base civilizations
<GAME_RESOURCES>/Base/modules/age-antiquity/data/civilizations.xml   # Antiquity Age civilizations
<GAME_RESOURCES>/Base/modules/age-exploration/data/civilizations.xml # Exploration Age civilizations
<GAME_RESOURCES>/Base/modules/age-modern/data/civilizations.xml      # Modern Age civilizations
```

### Leader Files

```
<GAME_RESOURCES>/Base/modules/base-standard/data/leaders.xml         # Base leader definitions
<GAME_RESOURCES>/Base/modules/age-antiquity/data/leaders.xml         # Antiquity Age leaders
<GAME_RESOURCES>/Base/modules/age-exploration/data/leaders.xml       # Exploration Age leaders
<GAME_RESOURCES>/Base/modules/age-modern/data/leaders.xml            # Modern Age leaders
```

### Unit Files

```
<GAME_RESOURCES>/Base/modules/base-standard/data/units.xml           # Base unit definitions
<GAME_RESOURCES>/Base/modules/age-antiquity/data/units.xml           # Antiquity Age units
<GAME_RESOURCES>/Base/modules/age-exploration/data/units.xml         # Exploration Age units
<GAME_RESOURCES>/Base/modules/age-modern/data/units.xml              # Modern Age units
```

### Building and Improvement Files

```
<GAME_RESOURCES>/Base/modules/base-standard/data/constructibles.xml  # Base buildings/improvements
<GAME_RESOURCES>/Base/modules/age-antiquity/data/constructibles.xml  # Antiquity Age buildings
<GAME_RESOURCES>/Base/modules/age-exploration/data/constructibles.xml # Exploration Age buildings
<GAME_RESOURCES>/Base/modules/age-modern/data/constructibles.xml     # Modern Age buildings
```

### Technology and Civic Tree Files

```
<GAME_RESOURCES>/Base/modules/age-antiquity/data/progression-trees-tech.xml      # Antiquity tech tree
<GAME_RESOURCES>/Base/modules/age-antiquity/data/progression-trees-culture.xml   # Antiquity civic tree
<GAME_RESOURCES>/Base/modules/age-exploration/data/progression-trees-tech.xml    # Exploration tech tree
<GAME_RESOURCES>/Base/modules/age-exploration/data/progression-trees-culture.xml # Exploration civic tree
<GAME_RESOURCES>/Base/modules/age-modern/data/progression-trees-tech.xml         # Modern tech tree
<GAME_RESOURCES>/Base/modules/age-modern/data/progression-trees-culture.xml      # Modern civic tree
```

### Localization Files

```
<GAME_RESOURCES>/Base/modules/base-standard/text/*.xml               # Base game text strings
<GAME_RESOURCES>/Base/modules/age-*/text/*.xml                       # Age-specific text strings
```

### Log Files

```
<GAME_DATA>/Logs/                                # All game logs
<GAME_DATA>/Logs/system.log                      # System-level logs
<GAME_DATA>/Logs/database.log                    # Database operation logs
<GAME_DATA>/Logs/AI_*.csv                        # AI-related logs
<GAME_DATA>/Logs/error.log                       # Error messages
<GAME_DATA>/Logs/mods.log                        # Mod loading information
```

## Installing Mods

Mods should be installed in the user's mod directory:

**macOS:**
```
~/Library/Application Support/Civilization VII/Mods/
```

**Windows:**
```
C:\Users\[Username]\Documents\My Games\Sid Meier's Civilization VII\Mods\
```

After building your mod, copy the entire mod folder to this location. The mod will be available in the Mod menu when you start the game.

## Mod Organization Best Practices

### File Naming Conventions

1. **Use Descriptive Names**: Name files to clearly indicate their contents and purpose
2. **Use Lowercase**: Keep filenames lowercase for consistency with game files
3. **Use Underscores**: Separate words with underscores (e.g., `roman_legion.xml`)
4. **Include Mod Prefix**: Prefix filenames with your mod ID to avoid conflicts
5. **Keep Extensions Consistent**: Use `.xml` for data files, `.sql` for database files

### Directory Organization

1. **Group by Element Type**: Organize files by game element type (civilizations, leaders, units, etc.)
2. **Separate Assets**: Keep assets (icons, textures) in their own directories
3. **Age-Specific Content**: If creating content for multiple Ages, consider organizing by Age

### Cross-Module References

When referencing files across different modules, use these path patterns:

1. **Same Module References**: `./file.xml` (relative to current directory)
2. **Base Game References**: Use platform-independent paths with the `fs://` protocol: `fs://game/modules/module-name/path/to/file.xml`
3. **External Asset References**: `fs://game/assets/path/to/asset`

## Conclusion

Understanding the file organization of Civilization VII is critical for creating well-structured mods that integrate properly with the game. By following the patterns established in the base game and organizing your mod files according to these best practices, you can create mods that are easier to maintain, troubleshoot, and share with the community.

For additional information on specific modding tasks, please refer to the other documents in the modding documentation set. 