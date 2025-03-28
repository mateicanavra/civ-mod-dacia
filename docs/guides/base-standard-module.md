# Civilization VII Base-Standard Module

## Overview

The base-standard module is the fundamental core of Civilization VII, containing age-agnostic definitions, mechanics, and systems that apply throughout the entire game. While Age modules provide era-specific content, the base-standard module establishes the foundational elements that enable all gameplay, regardless of which Age the player is experiencing.

This document provides an in-depth reference to the base-standard module's structure, content, and implementation patterns. Understanding this module is crucial for modders, as it defines the core schema and systems that all mods must integrate with.

## Module Location and Structure

**Module Path**: `<GAME_RESOURCES>/Base/modules/base-standard/`

The base-standard module follows the standard module structure, with some unique elements:

```
base-standard/
├── base-standard.modinfo      # Module definition
├── package.json               # Package metadata
├── definitions/               # Schema definitions (unique to base-standard)
│   ├── civilization.civdb
│   ├── leader.civdb
│   ├── unit.civdb
│   ├── constructible.civdb
│   └── ...
├── data/                      # Core gameplay data
│   ├── civilizations.xml
│   ├── leaders.xml
│   ├── units.xml
│   ├── constructibles.xml
│   └── ...
├── ui/                        # User interface elements
├── text/                      # Localization files
├── l10n/                      # Additional localization
├── scripts/                   # JavaScript files
└── maps/                      # Map scripts and definitions
```

The most distinctive feature of the base-standard module is the **definitions/** folder, which contains .civdb files that define the database structures for all game elements. This is exclusive to base-standard and not present in the Age modules.

## Core Definition Files

The definitions folder contains schema files that define the structure for all gameplay elements. These .civdb files use a JSON-like format to specify tables, columns, relationships, and constraints for the game's database.

### Key Definition Files

#### civilization.civdb

**File Path**: `<GAME_RESOURCES>/Base/modules/base-standard/definitions/civilization.civdb`

This file defines the database structure for civilizations. Key tables include:

```json
{
  "Tables": [
    {
      "Name": "Civilizations",
      "Columns": [
        {"Name": "CivilizationType", "Type": "TEXT", "NotNull": true},
        {"Name": "Name", "Type": "TEXT", "NotNull": true},
        {"Name": "Adjective", "Type": "TEXT", "NotNull": true},
        {"Name": "Description", "Type": "TEXT", "NotNull": false},
        {"Name": "Icon", "Type": "TEXT", "NotNull": false},
        {"Name": "SortIndex", "Type": "INTEGER", "NotNull": false}
      ],
      "PrimaryKey": {"Name": "PK_Civilizations", "Columns": ["CivilizationType"]}
    },
    {
      "Name": "CivilizationTraits",
      "Columns": [
        {"Name": "CivilizationType", "Type": "TEXT", "NotNull": true},
        {"Name": "TraitType", "Type": "TEXT", "NotNull": true}
      ],
      "PrimaryKey": {"Name": "PK_CivilizationTraits", "Columns": ["CivilizationType", "TraitType"]}
    },
    {
      "Name": "CivilizationDomains",
      "Columns": [
        {"Name": "CivilizationType", "Type": "TEXT", "NotNull": true},
        {"Name": "Domain", "Type": "TEXT", "NotNull": true}
      ],
      "PrimaryKey": {"Name": "PK_CivilizationDomains", "Columns": ["CivilizationType", "Domain"]}
    }
  ]
}
```

These tables define the core structure for civilizations, their traits, and which domains (Ages) they belong to.

#### leader.civdb

**File Path**: `<GAME_RESOURCES>/Base/modules/base-standard/definitions/leader.civdb`

This file defines the database structure for leaders. Key tables include:

```json
{
  "Tables": [
    {
      "Name": "Leaders",
      "Columns": [
        {"Name": "LeaderType", "Type": "TEXT", "NotNull": true},
        {"Name": "Name", "Type": "TEXT", "NotNull": true},
        {"Name": "IsPlayable", "Type": "BOOLEAN", "NotNull": true, "Default": true},
        {"Name": "Icon", "Type": "TEXT", "NotNull": false},
        {"Name": "SortIndex", "Type": "INTEGER", "NotNull": false}
      ],
      "PrimaryKey": {"Name": "PK_Leaders", "Columns": ["LeaderType"]}
    },
    {
      "Name": "LeaderTraits",
      "Columns": [
        {"Name": "LeaderType", "Type": "TEXT", "NotNull": true},
        {"Name": "TraitType", "Type": "TEXT", "NotNull": true}
      ],
      "PrimaryKey": {"Name": "PK_LeaderTraits", "Columns": ["LeaderType", "TraitType"]}
    },
    {
      "Name": "CivilizationLeaders",
      "Columns": [
        {"Name": "CivilizationType", "Type": "TEXT", "NotNull": true},
        {"Name": "LeaderType", "Type": "TEXT", "NotNull": true},
        {"Name": "Legacy", "Type": "BOOLEAN", "Default": false}
      ],
      "PrimaryKey": {"Name": "PK_CivilizationLeaders", "Columns": ["CivilizationType", "LeaderType"]}
    }
  ]
}
```

These tables define leaders, their traits, and their relationships with civilizations. Unlike civilizations, leaders are not age-specific and can span multiple Ages, as indicated by the "Legacy" column.

#### unit.civdb

**File Path**: `<GAME_RESOURCES>/Base/modules/base-standard/definitions/unit.civdb`

This file defines the database structure for units. Key tables include:

```json
{
  "Tables": [
    {
      "Name": "Units",
      "Columns": [
        {"Name": "UnitType", "Type": "TEXT", "NotNull": true},
        {"Name": "Name", "Type": "TEXT", "NotNull": true},
        {"Name": "Description", "Type": "TEXT"},
        {"Name": "Domain", "Type": "TEXT", "NotNull": true},
        {"Name": "FormationClass", "Type": "TEXT"},
        {"Name": "Cost", "Type": "INTEGER"},
        {"Name": "Maintenance", "Type": "INTEGER"},
        {"Name": "BaseMoves", "Type": "INTEGER"},
        {"Name": "Combat", "Type": "INTEGER"},
        {"Name": "RangedCombat", "Type": "INTEGER"},
        {"Name": "Range", "Type": "INTEGER"},
        {"Name": "PrereqTech", "Type": "TEXT"},
        {"Name": "PrereqCivic", "Type": "TEXT"},
        {"Name": "ReplacesUnitType", "Type": "TEXT"},
        {"Name": "PromotionClass", "Type": "TEXT"}
      ],
      "PrimaryKey": {"Name": "PK_Units", "Columns": ["UnitType"]}
    },
    {
      "Name": "UnitAbilities",
      "Columns": [
        {"Name": "UnitAbilityType", "Type": "TEXT", "NotNull": true},
        {"Name": "Name", "Type": "TEXT", "NotNull": true},
        {"Name": "Description", "Type": "TEXT"}
      ],
      "PrimaryKey": {"Name": "PK_UnitAbilities", "Columns": ["UnitAbilityType"]}
    },
    {
      "Name": "CivilizationUniqueUnits",
      "Columns": [
        {"Name": "CivilizationType", "Type": "TEXT", "NotNull": true},
        {"Name": "UnitType", "Type": "TEXT", "NotNull": true}
      ],
      "PrimaryKey": {"Name": "PK_CivilizationUniqueUnits", "Columns": ["CivilizationType", "UnitType"]}
    }
  ]
}
```

These tables define unit properties, abilities, and their associations with civilizations for unique units.

#### constructible.civdb

**File Path**: `<GAME_RESOURCES>/Base/modules/base-standard/definitions/constructible.civdb`

This file defines the database structure for buildings, improvements, and other constructible elements. Key tables include:

```json
{
  "Tables": [
    {
      "Name": "Buildings",
      "Columns": [
        {"Name": "BuildingType", "Type": "TEXT", "NotNull": true},
        {"Name": "Name", "Type": "TEXT", "NotNull": true},
        {"Name": "Description", "Type": "TEXT"},
        {"Name": "Cost", "Type": "INTEGER"},
        {"Name": "Maintenance", "Type": "INTEGER"},
        {"Name": "PrereqTech", "Type": "TEXT"},
        {"Name": "PrereqCivic", "Type": "TEXT"},
        {"Name": "PrereqDistrict", "Type": "TEXT"},
        {"Name": "ReplacementBuildingType", "Type": "TEXT"},
        {"Name": "IsWonder", "Type": "BOOLEAN", "Default": false},
        {"Name": "IsAgeless", "Type": "BOOLEAN", "Default": false}
      ],
      "PrimaryKey": {"Name": "PK_Buildings", "Columns": ["BuildingType"]}
    },
    {
      "Name": "Districts",
      "Columns": [
        {"Name": "DistrictType", "Type": "TEXT", "NotNull": true},
        {"Name": "Name", "Type": "TEXT", "NotNull": true},
        {"Name": "Description", "Type": "TEXT"},
        {"Name": "Cost", "Type": "INTEGER"},
        {"Name": "Maintenance", "Type": "INTEGER"},
        {"Name": "PrereqTech", "Type": "TEXT"},
        {"Name": "IsSpecialized", "Type": "BOOLEAN", "Default": false}
      ],
      "PrimaryKey": {"Name": "PK_Districts", "Columns": ["DistrictType"]}
    },
    {
      "Name": "CivilizationUniqueBuildings",
      "Columns": [
        {"Name": "CivilizationType", "Type": "TEXT", "NotNull": true},
        {"Name": "BuildingType", "Type": "TEXT", "NotNull": true}
      ],
      "PrimaryKey": {"Name": "PK_CivilizationUniqueBuildings", "Columns": ["CivilizationType", "BuildingType"]}
    }
  ]
}
```

These tables define buildings, districts (called Quarters in Civilization VII), and their associations with civilizations. Note the "IsAgeless" flag which determines if a building persists across Age transitions.

#### modifier-system.civdb

**File Path**: `<GAME_RESOURCES>/Base/modules/base-standard/definitions/modifier-system.civdb`

This file defines the core modifier system that powers traits, abilities, and effects in the game. Key tables include:

```json
{
  "Tables": [
    {
      "Name": "Modifiers",
      "Columns": [
        {"Name": "ModifierId", "Type": "TEXT", "NotNull": true},
        {"Name": "ModifierType", "Type": "TEXT", "NotNull": true},
        {"Name": "RunOnce", "Type": "BOOLEAN", "Default": false},
        {"Name": "Permanent", "Type": "BOOLEAN", "Default": false},
        {"Name": "NewOnly", "Type": "BOOLEAN", "Default": false},
        {"Name": "OwnerRequirementSetId", "Type": "TEXT"},
        {"Name": "SubjectRequirementSetId", "Type": "TEXT"}
      ],
      "PrimaryKey": {"Name": "PK_Modifiers", "Columns": ["ModifierId"]}
    },
    {
      "Name": "ModifierArguments",
      "Columns": [
        {"Name": "ModifierId", "Type": "TEXT", "NotNull": true},
        {"Name": "Name", "Type": "TEXT", "NotNull": true},
        {"Name": "Value", "Type": "TEXT", "NotNull": true},
        {"Name": "Type", "Type": "TEXT", "NotNull": true, "Default": "ARGTYPE_IDENTITY"}
      ],
      "PrimaryKey": {"Name": "PK_ModifierArguments", "Columns": ["ModifierId", "Name", "Value"]}
    },
    {
      "Name": "TraitModifiers",
      "Columns": [
        {"Name": "TraitType", "Type": "TEXT", "NotNull": true},
        {"Name": "ModifierId", "Type": "TEXT", "NotNull": true}
      ],
      "PrimaryKey": {"Name": "PK_TraitModifiers", "Columns": ["TraitType", "ModifierId"]}
    }
  ]
}
```

The modifier system is one of the most complex but powerful aspects of Civilization VII. It enables traits and abilities to affect gameplay through a flexible, data-driven approach.

#### age.civdb and age-transition.civdb

**File Paths**: 
- `<GAME_RESOURCES>/Base/modules/base-standard/definitions/age.civdb`
- `<GAME_RESOURCES>/Base/modules/base-standard/definitions/age-transition.civdb`

These files define the Age system and the transitions between Ages. Key tables include:

```json
{
  "Tables": [
    {
      "Name": "Ages",
      "Columns": [
        {"Name": "AgeType", "Type": "TEXT", "NotNull": true},
        {"Name": "Name", "Type": "TEXT", "NotNull": true},
        {"Name": "Description", "Type": "TEXT"},
        {"Name": "PlayerCivilizationDomain", "Type": "TEXT"},
        {"Name": "DefeatDomain", "Type": "TEXT"},
        {"Name": "VictoryDomain", "Type": "TEXT"},
        {"Name": "MapSizeDomain", "Type": "TEXT"},
        {"Name": "Banner", "Type": "TEXT"},
        {"Name": "SortIndex", "Type": "INTEGER"}
      ],
      "PrimaryKey": {"Name": "PK_Ages", "Columns": ["AgeType"]}
    },
    {
      "Name": "AgeTransitions",
      "Columns": [
        {"Name": "FromAgeType", "Type": "TEXT", "NotNull": true},
        {"Name": "ToAgeType", "Type": "TEXT", "NotNull": true},
        {"Name": "LegacyPointsRequired", "Type": "INTEGER", "NotNull": true},
        {"Name": "UnlocksNewLeaders", "Type": "BOOLEAN", "Default": false},
        {"Name": "LeaderDomain", "Type": "TEXT"}
      ],
      "PrimaryKey": {"Name": "PK_AgeTransitions", "Columns": ["FromAgeType", "ToAgeType"]}
    },
    {
      "Name": "LegacyPaths",
      "Columns": [
        {"Name": "LegacyPathType", "Type": "TEXT", "NotNull": true},
        {"Name": "Name", "Type": "TEXT", "NotNull": true},
        {"Name": "Description", "Type": "TEXT"},
        {"Name": "Icon", "Type": "TEXT"}
      ],
      "PrimaryKey": {"Name": "PK_LegacyPaths", "Columns": ["LegacyPathType"]}
    }
  ]
}
```

These tables define the core Age mechanics, transitions between Ages, and the Legacy Path system that plays a crucial role in Age progression.

## Data Structure for Gameplay Elements

The base-standard module implements the definitions through XML files in the data directory. These files provide the baseline implementation of game elements that are then extended or modified by the Age modules.

### Key Data Files

#### civilizations.xml

**File Path**: `<GAME_RESOURCES>/Base/modules/base-standard/data/civilizations.xml`

This file defines base civilizations that might span multiple Ages or serve as templates for Age-specific civilizations. It includes:

- Basic civilization definitions
- Civilization traits and abilities
- Starting biases and preferences

```xml
<Civilizations>
    <Row CivilizationType="CIVILIZATION_GENERIC" 
         Name="LOC_CIVILIZATION_GENERIC_NAME" 
         Adjective="LOC_CIVILIZATION_GENERIC_ADJECTIVE" 
         StartingTechType="TECH_POTTERY" 
         StartingCivicType="CIVIC_CODE_OF_LAWS" 
         FavoredReligion="RELIGION_PAGANISM"/>
</Civilizations>
<CivilizationAbilities>
    <Row CivilizationType="CIVILIZATION_GENERIC" TraitType="TRAIT_CIVILIZATION_GENERIC_ABILITY"/>
</CivilizationAbilities>
```

#### leaders.xml

**File Path**: `<GAME_RESOURCES>/Base/modules/base-standard/data/leaders.xml`

This file defines leaders that span all Ages. Since leaders persist across Age transitions, they're primarily defined in the base-standard module. It includes:

- Leader definitions
- Leader traits and abilities
- Leader agendas (AI behavior patterns)
- Leader attribute progression

```xml
<Leaders>
    <Row LeaderType="LEADER_AUGUSTUS" 
         Name="LOC_LEADER_AUGUSTUS_NAME" 
         InheritFrom="LEADER_DEFAULT" 
         SceneLighting="LEADER_AUGUSTUS_LIGHTING"/>
</Leaders>
<LeaderTraits>
    <Row LeaderType="LEADER_AUGUSTUS" TraitType="TRAIT_LEADER_AUGUSTUS_ABILITY"/>
</LeaderTraits>
<LeaderQuotes>
    <Row LeaderType="LEADER_AUGUSTUS" Quote="LOC_LEADER_AUGUSTUS_QUOTE"/>
</LeaderQuotes>
```

#### units.xml

**File Path**: `<GAME_RESOURCES>/Base/modules/base-standard/data/units.xml`

This file defines universal unit types and mechanics. While most specific units are defined in Age modules, the base-standard module establishes core unit categories and systems. It includes:

- Base unit types (Settler, Builder, etc.)
- Unit mechanics and systems
- Formation classes
- Promotion systems

```xml
<Units>
    <Row UnitType="UNIT_SETTLER" 
         Name="LOC_UNIT_SETTLER_NAME" 
         Description="LOC_UNIT_SETTLER_DESCRIPTION" 
         Domain="DOMAIN_LAND" 
         FormationClass="FORMATION_CLASS_CIVILIAN" 
         Cost="80" 
         BaseMoves="2" 
         Population="1" 
         FoundCity="true"/>
</Units>
<UnitCapabilities>
    <Row UnitType="UNIT_SETTLER" CapabilityType="CAPABILITY_FOUND_CITY"/>
</UnitCapabilities>
```

#### constructibles.xml

**File Path**: `<GAME_RESOURCES>/Base/modules/base-standard/data/constructibles.xml`

This file defines buildings, quarters (districts), and other constructible elements that are available across all Ages. It includes:

- Base building types
- Quarter (district) definitions
- Wonder mechanics
- Adjacency systems
- Building yields and effects

```xml
<Buildings>
    <Row BuildingType="BUILDING_GRANARY" 
         Name="LOC_BUILDING_GRANARY_NAME" 
         Description="LOC_BUILDING_GRANARY_DESCRIPTION" 
         Cost="65" 
         Maintenance="1" 
         FoodYield="1"/>
</Buildings>
<Districts>
    <Row DistrictType="DISTRICT_CITY_CENTER" 
         Name="LOC_DISTRICT_CITY_CENTER_NAME" 
         Description="LOC_DISTRICT_CITY_CENTER_DESCRIPTION" 
         Cost="0" 
         IsDefault="true"/>
</Districts>
```

#### modifier-system-gameeffects.xml

**File Path**: `<GAME_RESOURCES>/Base/modules/base-standard/data/modifier-system-gameeffects.xml`

This file implements the modifier system that powers traits, abilities, and effects. It includes:

- Base modifier types
- Standard effect definitions
- Requirement sets
- Common gameplay modifiers

```xml
<Modifiers>
    <Row ModifierId="MODIFIER_PLAYER_CITIES_ADJUST_YIELD" 
         ModifierType="MODIFIER_PLAYER_CITIES_ADJUST_YIELD" 
         SubjectRequirementSetId="REQSET_PLAYER_HAS_AT_LEAST_ONE_CITY"/>
</Modifiers>
<ModifierArguments>
    <Row ModifierId="MODIFIER_PLAYER_CITIES_ADJUST_YIELD" 
         Name="YieldType" 
         Value="YIELD_FOOD" 
         Type="ARGTYPE_IDENTITY"/>
    <Row ModifierId="MODIFIER_PLAYER_CITIES_ADJUST_YIELD" 
         Name="Amount" 
         Value="1" 
         Type="ARGTYPE_IDENTITY"/>
</ModifierArguments>
```

## Implementation Patterns Common Across Ages

The base-standard module establishes several implementation patterns that are followed across all Age modules. Understanding these patterns is essential for creating mods that integrate seamlessly with the game.

### The Trait-Modifier Pattern

One of the most important patterns is the trait-modifier system, which powers civilization and leader abilities:

1. Define a **Trait** (e.g., `TRAIT_CIVILIZATION_DACIA_ABILITY`)
2. Create **Modifiers** that implement the effects (e.g., `MODIFIER_DACIA_COMBAT_BONUS`)
3. Link modifiers to traits through the **TraitModifiers** table
4. Assign traits to civilizations or leaders through **CivilizationTraits** or **LeaderTraits**

```xml
<!-- Define the trait -->
<Traits>
    <Row TraitType="TRAIT_CIVILIZATION_DACIA_ABILITY" 
         Name="LOC_TRAIT_CIVILIZATION_DACIA_ABILITY_NAME" 
         Description="LOC_TRAIT_CIVILIZATION_DACIA_ABILITY_DESCRIPTION"/>
</Traits>

<!-- Create modifiers -->
<Modifiers>
    <Row ModifierId="MODIFIER_DACIA_COMBAT_BONUS" 
         ModifierType="MODIFIER_UNIT_ADJUST_COMBAT_STRENGTH" 
         SubjectRequirementSetId="REQSET_UNIT_IS_DOMAIN_LAND"/>
</Modifiers>
<ModifierArguments>
    <Row ModifierId="MODIFIER_DACIA_COMBAT_BONUS" 
         Name="Amount" 
         Value="5" 
         Type="ARGTYPE_IDENTITY"/>
</ModifierArguments>

<!-- Link modifiers to traits -->
<TraitModifiers>
    <Row TraitType="TRAIT_CIVILIZATION_DACIA_ABILITY" 
         ModifierId="MODIFIER_DACIA_COMBAT_BONUS"/>
</TraitModifiers>

<!-- Assign traits to civilizations -->
<CivilizationTraits>
    <Row CivilizationType="CIVILIZATION_DACIA" 
         TraitType="TRAIT_CIVILIZATION_DACIA_ABILITY"/>
</CivilizationTraits>
```

This pattern is used consistently across the game for all special abilities, bonuses, and effects.

### The Requirement System

Requirements define conditions under which modifiers apply. The base-standard module establishes a robust system for creating and combining requirements:

1. Define **RequirementSets** that group related requirements
2. Create individual **Requirements** that check specific conditions
3. Combine requirements using logical operators (ALL, ANY, etc.)
4. Reference requirement sets in modifiers

```xml
<!-- Define a requirement set -->
<RequirementSets>
    <Row RequirementSetId="REQSET_DACIA_HILL_COMBAT" 
         RequirementSetType="REQUIREMENTSET_TEST_ALL"/>
</RequirementSets>

<!-- Create individual requirements -->
<Requirements>
    <Row RequirementId="REQ_UNIT_ON_HILL" 
         RequirementType="REQUIREMENT_PLOT_TERRAIN_TYPE_MATCHES" 
         Inverse="false"/>
</Requirements>
<RequirementArguments>
    <Row RequirementId="REQ_UNIT_ON_HILL" 
         Name="TerrainType" 
         Value="TERRAIN_HILLS"/>
</RequirementArguments>

<!-- Link requirements to sets -->
<RequirementSetRequirements>
    <Row RequirementSetId="REQSET_DACIA_HILL_COMBAT" 
         RequirementId="REQ_UNIT_ON_HILL"/>
</RequirementSetRequirements>

<!-- Reference requirement sets in modifiers -->
<Modifiers>
    <Row ModifierId="MODIFIER_DACIA_HILL_COMBAT_BONUS" 
         ModifierType="MODIFIER_UNIT_ADJUST_COMBAT_STRENGTH" 
         SubjectRequirementSetId="REQSET_DACIA_HILL_COMBAT"/>
</Modifiers>
```

This system allows for complex conditional mechanics that respond to game state.

### The Ageless Flag Pattern

The base-standard module establishes the pattern for content that persists across Age transitions:

1. Set the **IsAgeless** flag to true for buildings or content that should persist
2. For units, set the **IsPersistent** flag
3. For mechanics, ensure they're defined in base-standard rather than Age-specific modules

```xml
<Buildings>
    <Row BuildingType="BUILDING_WORLD_WONDER_PYRAMIDS" 
         Name="LOC_BUILDING_WORLD_WONDER_PYRAMIDS_NAME" 
         Description="LOC_BUILDING_WORLD_WONDER_PYRAMIDS_DESCRIPTION" 
         IsWonder="true" 
         IsAgeless="true"/>
</Buildings>

<Units>
    <Row UnitType="UNIT_COMMANDER" 
         IsPersistent="true"/>
</Units>
```

Ageless content serves as a crucial bridge between Ages, maintaining continuity in the player's empire.

## Icon Sets and Assets

The base-standard module includes definitions for icons and visual assets used throughout the game.

### Icon XML Files

**File Path**: `<GAME_RESOURCES>/Base/modules/base-standard/data/icons/`

This directory contains XML files defining icons for various game elements:

- **civilization-icons.xml**: Civilization symbols
- **leader-icons.xml**: Leader portraits
- **unit-icons.xml**: Unit symbols
- **building-icons.xml**: Building icons
- **resource-icons.xml**: Resource icons

```xml
<GameData>
  <IconDefinitions>
    <Row ID="ICON_CIVILIZATION_ROME" Size="32" Path="civ_sym_rome_32.dds"/>
    <Row ID="ICON_CIVILIZATION_ROME" Size="45" Path="civ_sym_rome_45.dds"/>
    <Row ID="ICON_CIVILIZATION_ROME" Size="50" Path="civ_sym_rome_50.dds"/>
    <Row ID="ICON_CIVILIZATION_ROME" Size="64" Path="civ_sym_rome_64.dds"/>
    <Row ID="ICON_CIVILIZATION_ROME" Size="80" Path="civ_sym_rome_80.dds"/>
  </IconDefinitions>
</GameData>
```

When creating mods, you can either reference existing icons or define new ones following this pattern.

### Asset References

The base-standard module establishes patterns for referencing various types of assets:

1. **Icon References**: Use the icon ID in relevant tables (e.g., `Icon="ICON_CIVILIZATION_ROME"`)
2. **Model References**: Reference 3D models for units and structures
3. **Texture References**: Reference textures for UI elements
4. **Animation References**: Reference animations for units and leaders

```xml
<Leaders>
    <Row LeaderType="LEADER_AUGUSTUS" 
         Name="LOC_LEADER_AUGUSTUS_NAME" 
         Icon="ICON_LEADER_AUGUSTUS"/>
</Leaders>

<Units>
    <Row UnitType="UNIT_WARRIOR" 
         Name="LOC_UNIT_WARRIOR_NAME" 
         MeshAsset="ASSET_UNIT_WARRIOR"/>
</Units>
```

Modders typically reference existing assets rather than creating new ones, as the latter requires specialized tools.

## Cross-References

- For core architecture concepts, see [Civ7 Modding Architecture](/guides/modding-architecture.md)
- For database schema details, see [Database Schemas](/guides/database-schemas.md)
- For Age module specifics, see [Age Modules](/guides/age-modules.md)
- For real-world examples, see [DLC and Community Mod Patterns](/guides/mod-patterns.md)

## Conclusion

The base-standard module serves as the foundation of Civilization VII, defining the core structures, systems, and patterns that all gameplay is built upon. While Age modules provide era-specific content, base-standard establishes the universal elements that ensure consistency across the entire game experience.

By understanding the base-standard module, modders can create content that integrates seamlessly with the game's core systems and functions consistently across all Ages. Whether adding new civilizations, implementing unique abilities, or modifying gameplay mechanics, a thorough knowledge of base-standard is essential for effective Civilization VII modding. 