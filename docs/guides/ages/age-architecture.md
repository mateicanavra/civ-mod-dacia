# Civilization VII Age System Architecture

## The Ages System

One of Civilization VII's defining features is its Ages system, which directly impacts how modding works:

```
<GAME_RESOURCES>/Base/
├── base-standard (core definitions)
├── age-antiquity (earliest era)
├── age-exploration (middle era)
└── age-modern (latest era)
```

Each Age module:
- Contains content specific to that historical period
- Has its own independent database tables
- Maintains its own localization and assets
- Can be conditionally loaded based on game state

This modular approach means mods must consider which Age their content belongs to. For example, a civilization intended for the Antiquity Age should depend on the "age-antiquity" module, while Modern Age content should properly reference "age-modern" dependencies.

As Ed Beach, Creative Director, explained in a dev diary: "Ages determine which Civics and Technologies can be researched" and "Available types of Units, Buildings, and Wonders are determined by the Age." This age-specific content is a fundamental consideration for mod creators.

## Age Module Relationships

The base game content is distributed across multiple age modules that work together:

- **age-antiquity**: Early game civilizations, units, and mechanics (3000 BCE - 500 CE)
- **age-exploration**: Middle period content and progression (500 CE - 1700 CE)
- **age-modern**: Late game technologies, units, and systems (1700 CE - Present)

Custom mods can interact with these modules through dependencies, additions, or modifications, giving modders precise control over which parts of the game they affect.

### Age Module File Structure

Examining the actual game files, each age module follows a consistent structure:

```
<GAME_RESOURCES>/Base/modules/module-name/
├── module-name.modinfo   # Module definition file
├── package.json          # Package metadata
├── config/               # Configuration settings
├── data/                 # XML files for game elements
│   ├── civilizations.xml # Civilization definitions (not in subfolders)
│   ├── leaders.xml       # Leader definitions
│   ├── units.xml         # Unit definitions
│   ├── ...               # Other game element files
├── text/                 # Localization files
├── l10n/                 # Additional localization
├── ui/                   # UI element definitions
├── scripts/              # JavaScript files for game logic
└── movies/               # Media resources
```

Age modules focus on content specific to their historical period, but lack the definition files found in the base-standard module.

## Age Transitions and Mod Compatibility

One unique aspect of Civilization VII modding is accounting for Age transitions. Edward Zhang, Economics Feature Lead, explains that during transitions:

- Leaders remain the same across Ages
- Wonders and unique Quarters persist as "Ageless" structures
- Commander units maintain attributes and experience
- Traditions unlocked from Civic trees remain available

Mods need to consider these persistence mechanics when designing content that spans multiple Ages.

## Best Practices for Age-Specific Content

### Organization

- Use unique identifiers for all content (prefixed with mod name and age)
- Group age-related files logically (data, text, UI, etc.)

### Compatibility

- Specify clear dependencies on the appropriate age modules
- Use conditional loading for era-specific content
- Test compatibility across age transitions

### Technical Execution

- Follow naming conventions from the base game age modules
- Balance new content against existing age-specific game elements
- Test with multiple ages to ensure proper transitions

## Related Documentation

- [Age Modules](/guides/ages/age-modules.md) - Detailed documentation on age module structure and content
- [Age Gameplay Mechanics](/guides/ages/age-gameplay-mechanics.md) - Age-specific gameplay systems and mechanics
- [Base-Standard Module](/guides/base-standard-module.md) - Core structure that interacts with age modules 