# Community Modding Tips & Discoveries

This document compiles community-discovered information, tips, and tricks for modding Civilization VII. The information is gathered from various community sources including CivFanatics forums and other community discussions.

## Table of Contents

- [Core Technical Changes](#core-technical-changes)
- [File Structure](#file-structure)
- [Modding Capabilities](#modding-capabilities)
- [Known Limitations](#known-limitations)
- [Tips & Tricks](#tips--tricks)
- [File Locations](#file-locations)
- [Community Resources](#community-resources)
- [Current State of Modding](#current-state-of-modding)
- [Best Practices](#best-practices)
- [Latest Community Discoveries](#latest-community-discoveries)

## Core Technical Changes

### JavaScript-Based Modding
- Civilization VII uses JavaScript instead of Lua for modding (confirmed by multiple modders)
- This is a significant change from previous Civilization games that used Lua
- Waiting for official modding tools to provide JavaScript API documentation
- Modders familiar with Lua will need to adapt their approaches and learn JavaScript
- The map scripts in `base-standard/maps` provide examples of JavaScript implementation
- No gameplay DLL is visible, with core functionality packed in executable files
- The JavaScript API extent is currently unknown, but expected to be robust based on prior games

## File Structure

### Core Files
- Uses `.dep` and `.modinfo` files for DLC and mods
- DLC structure can serve as examples for mod creation

### Age System Organization
The game uses a modular age system with separate files:
- `core.modinfo` - Core game systems
- `base-standard.modinfo` - Base game content
- `age-antiquity.modinfo` - Antiquity Age specific content
- `age-exploration.modinfo` - Exploration Age content
- `age-modern.modinfo` - Modern Age content

### Global Parameters
- No longer contained in a single XML file
- Split across multiple files based on relevance
- Some Civilization VI artifacts remain in parameters
- Parameters are era-specific

## Modding Capabilities

### Map Generation
- Map scripts are editable and can be modified for custom maps
- Located in: `steamapps\common\Sid Meier's Civilization VII\Base\modules\base-standard\maps`
- Current maximum map width: 128 tiles (confirmed by Gedemon on CivFanatics)
- Map generation parameters can be modified
- Each map type has its own script file in the maps directory
- Scripts appear to be written in JavaScript, consistent with other modding changes
- Custom map types can potentially be created by duplicating and modifying existing scripts

### Asset Management
- Assets are loaded/unloaded between eras during age transitions
- Possibly implemented due to Civilization VI asset limit bug (suggested by Gedemon)
- May affect how mods handle era-specific content
- Buildings from previous ages remain on map after transition despite asset unloading
- Global parameters are era-specific and split across multiple files (discovered by thecrazyscot)
- Each age has its own modinfo file that defines its content and assets
- Modders should consider creating age-specific mods that respect this architecture

## Known Limitations

### Technical Restrictions
- No visible gameplay DLL
- All core functionality packed in exe files
- Denuvo protection may limit certain modding capabilities
- Art definitions (artdef) currently packed
- Unit model modding may require SDK release

## Tips & Tricks

### Working with Ages
- Consider asset loading/unloading when creating era-specific content
- Buildings from previous ages remain on map after transition
- Each age module can be modified independently

### Map Modding
- Scripts for each map type are editable
- Located in base-standard/maps directory
- Consider 128-tile width limitation

## File Locations

### Base Game Files
```
steamapps\common\Sid Meier's Civilization VII\Base\modules\
├── base-standard\
│   └── maps\
├── age-antiquity\
├── age-exploration\
└── age-modern\
```

## Community Resources

### Active Projects
- Several modders working on:
  - Map generation modifications
  - UI improvements
  - Civilization/Leader additions
  - Game mechanics adjustments

### Tools & Utilities
- Waiting for official SDK release
- Community discovering modding capabilities
- JavaScript development tools recommended

## Current State of Modding

### What's Possible Now
- Basic configuration file modifications
- Map script editing
- Era-specific content modifications
- UI adjustments

### What Needs SDK
- Unit model modifications
- Advanced gameplay modifications
- Custom civilization creation (limited)
- Full asset manipulation

## Best Practices

### Organization
- Keep mods era-specific when possible
- Consider asset loading/unloading
- Follow modular structure of base game

### Performance
- Be mindful of asset limits
- Consider era transitions in mod design
- Test thoroughly across different ages

### Compatibility
- Check for conflicts with other mods
- Document dependencies clearly
- Consider base game version compatibility

## Latest Community Discoveries
This section tracks the most recent findings from the modding community, primarily from the [CivFanatics Modding Discoveries thread](https://forums.civfanatics.com/threads/modding-discoveries.694816/).

### Map Generation (March 2025)
- Maximum map width is 128 tiles (discovered by Gedemon)
- Map scripts are located in `base-standard/maps` directory (reported by Archanist)
- Scripts are editable and follow JavaScript format

### Asset System (March 2025)
- Global parameters are split across multiple files rather than in a single XML file (discovered by thecrazyscot)
- Some Civilization VI artifacts remain in the parameters files
- Assets are loaded/unloaded between era transitions

### Technical Limitations (March 2025)
- Art definitions (artdef) appear to be packed and not easily accessible (reported by Gedemon)
- Unit model modding will likely require the SDK release
- No visible gameplay DLL, with functionality packed in exe files

### Notable Community Mods (March 2025)
- thecrazyscot: [Improved Mod Page](https://forums.civfanatics.com/resources/tcs-improved-mod-page.32024/), [Improved Plot Tooltip](https://forums.civfanatics.com/resources/tcs-improved-plot-tooltip.31859/), [Outremer (Exploration)](https://forums.civfanatics.com/resources/tcs-outremer-exploration.32002/)
- Archanist: [Philippines (Modern)](https://forums.civfanatics.com/resources/philippines-modern.32036/), [Tondo (Antiquity)](https://forums.civfanatics.com/resources/tondo-actually-tagalogs-antiquity.32094/)

---

> Note: This document is community-sourced and continuously updated as new discoveries are made. Some information may change as official modding tools are released or new game updates are published. 