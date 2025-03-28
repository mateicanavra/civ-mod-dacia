# General Guide: Creating New Civilizations in Civilization VII

## Overview

This guide provides a step-by-step approach to creating new civilizations for Civilization VII. It covers the entire process from initial planning to implementation and testing, with practical examples from both official DLC and community mods.

## Prerequisites

Before creating a new civilization, ensure you have:

1. A basic understanding of the Civilization VII modding architecture (see [Modding Architecture](/guides/modding-architecture.md))
2. Familiarity with the game's database schema (see [Database Schemas](/guides/database-schemas.md))
3. Knowledge of the Age system (see [Age Modules](/guides/age-modules.md))
4. The game's base files for reference

## Planning Your Civilization

### Choosing an Age

Every civilization in Civilization VII is tied to a specific Age:
- **Antiquity Age** (earliest period)
- **Exploration Age** (middle period)
- **Modern Age** (latest period)

Choose the Age that best fits your civilization's historical prominence and the gameplay experience you want to create.

### Core Design Elements

Define these essential elements for your civilization:

1. **Civilization Name**: The primary identifier
2. **Historical Context**: The civilization's historical significance
3. **Core Ability**: The unique gameplay advantage
4. **Unique Units**: Special military and/or civilian units
5. **Unique Infrastructure**: Buildings, improvements, or quarters
6. **Traditions**: Policies unlocked through your civilization's unique Civic tree
7. **Associated Wonder**: The wonder that your civilization receives bonuses for building

### Designing for Balance

Consider these factors when balancing your civilization:

1. **Gameplay Niche**: Define a clear strength without making the civilization overpowered
2. **Age-Appropriate Power**: Ensure abilities are appropriate for the assigned Age
3. **Synergy**: Design components that work well together
4. **Counterplay**: Ensure there are strategic counters to your civilization's strengths

## Folder Structure Setup

Create a well-organized folder structure for your mod:

```
<GAME_DATA>/Mods/your-civilization-mod/
├── civilizations/        # Civilization definitions
├── traditions/           # Tradition definitions
├── units/                # Unit definitions
├── constructibles/       # Buildings, improvements, and quarters
├── progression-trees/    # Civic and tech tree modifications
├── imports/              # External assets and dependencies
└── your-mod-name.modinfo # Mod metadata file
```

## Implementation Process

### 1. Creating the Modinfo File

The `.modinfo` file defines your mod's metadata and structure:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Mod id="your-civilization-mod" version="1" xmlns="ModInfo">
  <Properties>
    <n>Your Civilization Name</n>
    <Description>Description of your civilization mod</Description>
    <Authors>Your Name</Authors>
    <Version>1.0.0</Version>
    <AffectsSavedGames>1</AffectsSavedGames>
  </Properties>
  <Dependencies>
    <Mod id="base-standard" title="LOC_MODULE_BASE_STANDARD_NAME"/>
  </Dependencies>
  <ActionCriteria>
    <Criteria id="age-antiquity-current">
      <AgeInUse>AGE_ANTIQUITY</AgeInUse>
    </Criteria>
    <Criteria id="always">
      <AlwaysMet/>
    </Criteria>
  </ActionCriteria>
  <ActionGroups>
    <ActionGroup id="main-group" scope="game" criteria="age-antiquity-current">
      <Actions>
        <UpdateDatabase>
          <Item>civilizations/your_civilization.xml</Item>
          <!-- Add more files as needed -->
        </UpdateDatabase>
      </Actions>
    </ActionGroup>
  </ActionGroups>
</Mod>
```

### 2. Defining Your Civilization

Create a file like `civilizations/your_civilization.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <Civilizations>
    <Row CivilizationType="CIVILIZATION_YOUR_CIV" Name="LOC_CIVILIZATION_YOUR_CIV_NAME" 
         Description="LOC_CIVILIZATION_YOUR_CIV_DESCRIPTION" 
         Adjective="LOC_CIVILIZATION_YOUR_CIV_ADJECTIVE"
         StartingCivilizationLevelType="CIVILIZATION_LEVEL_FULL_CIV"/>
  </Civilizations>
  
  <CivilizationTraits>
    <Row TraitType="TRAIT_YOUR_CIV_ABILITY" CivilizationType="CIVILIZATION_YOUR_CIV"/>
    <Row TraitType="TRAIT_ANTIQUITY_CIV" CivilizationType="CIVILIZATION_YOUR_CIV"/>
  </CivilizationTraits>
  
  <!-- Add city names -->
  <CityNames>
    <Row CivilizationType="CIVILIZATION_YOUR_CIV" CityName="LOC_CIVILIZATION_YOUR_CIV_CITY_NAMES_1"/>
    <Row CivilizationType="CIVILIZATION_YOUR_CIV" CityName="LOC_CIVILIZATION_YOUR_CIV_CITY_NAMES_2"/>
  </CityNames>
  
  <!-- Add start bias -->
  <StartBiasBiomes>
    <Row CivilizationType="CIVILIZATION_YOUR_CIV" BiomeType="BIOME_PLAINS" Score="30"/>
  </StartBiasBiomes>
  
  <StartBiasResources>
    <Row CivilizationType="CIVILIZATION_YOUR_CIV" ResourceType="RESOURCE_HORSES" Score="80"/>
  </StartBiasResources>
</Database>
```

### 3. Creating Unique Abilities

Define your civilization's unique abilities:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <Types>
    <Row Type="TRAIT_YOUR_CIV_ABILITY" Kind="KIND_TRAIT"/>
  </Types>
  
  <Traits>
    <Row TraitType="TRAIT_YOUR_CIV_ABILITY" Name="LOC_TRAIT_YOUR_CIV_ABILITY_NAME" 
         Description="LOC_TRAIT_YOUR_CIV_ABILITY_DESCRIPTION" InternalOnly="true"/>
  </Traits>
  
  <!-- Define modifiers that implement the ability effects -->
  <Modifiers>
    <Row ModifierId="MODIFIER_YOUR_CIV_ABILITY_EFFECT" 
         ModifierType="MODIFIER_PLAYER_ADJUST_YIELD_CHANGE"/>
  </Modifiers>
  
  <ModifierArguments>
    <Row ModifierId="MODIFIER_YOUR_CIV_ABILITY_EFFECT" 
         Name="YieldType" Value="YIELD_PRODUCTION"/>
    <Row ModifierId="MODIFIER_YOUR_CIV_ABILITY_EFFECT" 
         Name="Amount" Value="1"/>
  </ModifierArguments>
  
  <!-- Link trait to modifiers -->
  <TraitModifiers>
    <Row TraitType="TRAIT_YOUR_CIV_ABILITY" 
         ModifierId="MODIFIER_YOUR_CIV_ABILITY_EFFECT"/>
  </TraitModifiers>
</Database>
```

### 4. Creating Unique Units

Define a unique unit for your civilization:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <Types>
    <Row Type="UNIT_YOUR_CIV_UNIQUE" Kind="KIND_UNIT" />
  </Types>
  
  <Units>
    <Row UnitType="UNIT_YOUR_CIV_UNIQUE" 
         Name="LOC_UNIT_YOUR_CIV_UNIQUE_NAME"
         Description="LOC_UNIT_YOUR_CIV_UNIQUE_DESCRIPTION"
         BaseMoves="2" 
         BaseSightRange="2"
         Maintenance="3"
         CoreClass="CORE_CLASS_MILITARY"
         Domain="DOMAIN_LAND"
         FormationClass="FORMATION_CLASS_LAND_COMBAT"
         TraitType="TRAIT_YOUR_CIV"/>
  </Units>
  
  <Unit_Costs>
    <Row UnitType="UNIT_YOUR_CIV_UNIQUE" YieldType="YIELD_PRODUCTION" Cost="150"/>
  </Unit_Costs>
  
  <Unit_Stats>
    <Row UnitType="UNIT_YOUR_CIV_UNIQUE" Combat="35"/>
  </Unit_Stats>
  
  <UnitReplaces>
    <Row CivUniqueUnitType="UNIT_YOUR_CIV_UNIQUE" ReplacesUnitType="UNIT_WARRIOR"/>
  </UnitReplaces>
  
  <!-- Type tags for classification -->
  <TypeTags>
    <Row Type="UNIT_YOUR_CIV_UNIQUE" Tag="UNIT_CLASS_COMBAT"/>
  </TypeTags>
</Database>
```

### 5. Creating Unique Infrastructure

Define unique buildings, improvements, or quarters:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <!-- For a unique building -->
  <Types>
    <Row Type="BUILDING_YOUR_CIV_UNIQUE" Kind="KIND_BUILDING" />
  </Types>
  
  <Buildings>
    <Row BuildingType="BUILDING_YOUR_CIV_UNIQUE"
         Name="LOC_BUILDING_YOUR_CIV_UNIQUE_NAME" 
         Description="LOC_BUILDING_YOUR_CIV_UNIQUE_DESCRIPTION"
         TraitType="TRAIT_YOUR_CIV"
         QuarterType="QUARTER_CITY_CENTER"
         IsAgeless="true"
         PurchaseYield="YIELD_GOLD"/>
  </Buildings>
  
  <Building_YieldChanges>
    <Row BuildingType="BUILDING_YOUR_CIV_UNIQUE" YieldType="YIELD_FOOD" YieldChange="2"/>
  </Building_YieldChanges>
  
  <Building_Costs>
    <Row BuildingType="BUILDING_YOUR_CIV_UNIQUE" YieldType="YIELD_PRODUCTION" Cost="120"/>
  </Building_Costs>
  
  <BuildingReplaces>
    <Row CivUniqueBuildingType="BUILDING_YOUR_CIV_UNIQUE" ReplacesBuildingType="BUILDING_GRANARY"/>
  </BuildingReplaces>
</Database>
```

### 6. Creating Traditions

Define traditions that persist across Ages:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <Types>
    <Row Type="TRADITION_YOUR_CIV_UNIQUE" Kind="KIND_TRADITION" />
  </Types>
  
  <Traditions>
    <Row TraditionType="TRADITION_YOUR_CIV_UNIQUE"
         Name="LOC_TRADITION_YOUR_CIV_UNIQUE_NAME"
         Description="LOC_TRADITION_YOUR_CIV_UNIQUE_DESCRIPTION"/>
  </Traditions>
  
  <!-- Define tradition effects through modifiers -->
  <Modifiers>
    <Row ModifierId="MODIFIER_YOUR_TRADITION_EFFECT"
         ModifierType="MODIFIER_PLAYER_CITIES_ADJUST_BUILDING_YIELD_CHANGE"/>
  </Modifiers>
  
  <ModifierArguments>
    <Row ModifierId="MODIFIER_YOUR_TRADITION_EFFECT"
         Name="BuildingType" Value="BUILDING_MARKET"/>
    <Row ModifierId="MODIFIER_YOUR_TRADITION_EFFECT"
         Name="YieldType" Value="YIELD_GOLD"/>
    <Row ModifierId="MODIFIER_YOUR_TRADITION_EFFECT"
         Name="Amount" Value="2"/>
  </ModifierArguments>
  
  <!-- Link tradition to modifiers -->
  <TraditionModifiers>
    <Row TraditionType="TRADITION_YOUR_CIV_UNIQUE"
         ModifierId="MODIFIER_YOUR_TRADITION_EFFECT"/>
  </TraditionModifiers>
</Database>
```

### 7. Modifying the Civic Tree

Create a unique Civic Tree branch for your civilization:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <ProgressionTreeNodes>
    <Row Type="NODE_YOUR_CIV_CIVIC_1"
         ProgressionTreeType="TREE_CIVICS_ANTIQUITY"
         NodeType="CIVIC_YOUR_CIV_UNIQUE"
         Cost="85"
         Column="3"
         Row="4"
         IsUnlockForCivilizationTypes="1"/>
  </ProgressionTreeNodes>
  
  <ProgressionNodeUnlockTraits>
    <Row ProgressionNodeType="NODE_YOUR_CIV_CIVIC_1"
         TraitType="TRADITION_YOUR_CIV_UNIQUE"/>
  </ProgressionNodeUnlockTraits>
  
  <ProgressionTreeNodeUnlockCivilizations>
    <Row ProgressionTreeNodeType="NODE_YOUR_CIV_CIVIC_1"
         CivilizationType="CIVILIZATION_YOUR_CIV"/>
  </ProgressionTreeNodeUnlockCivilizations>
</Database>
```

### 8. Adding Localization

Create localization files for your civilization:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GameData>
  <LocalizedText>
    <!-- Civilization -->
    <Row Tag="LOC_CIVILIZATION_YOUR_CIV_NAME" Language="en_US">
      <Text>Your Civilization</Text>
    </Row>
    <Row Tag="LOC_CIVILIZATION_YOUR_CIV_DESCRIPTION" Language="en_US">
      <Text>Description of your civilization's history and characteristics.</Text>
    </Row>
    <Row Tag="LOC_CIVILIZATION_YOUR_CIV_ADJECTIVE" Language="en_US">
      <Text>Your Civilization Adjective</Text>
    </Row>
    
    <!-- City Names -->
    <Row Tag="LOC_CIVILIZATION_YOUR_CIV_CITY_NAMES_1" Language="en_US">
      <Text>Capital City</Text>
    </Row>
    <Row Tag="LOC_CIVILIZATION_YOUR_CIV_CITY_NAMES_2" Language="en_US">
      <Text>Second City</Text>
    </Row>
    
    <!-- Ability -->
    <Row Tag="LOC_TRAIT_YOUR_CIV_ABILITY_NAME" Language="en_US">
      <Text>Your Unique Ability Name</Text>
    </Row>
    <Row Tag="LOC_TRAIT_YOUR_CIV_ABILITY_DESCRIPTION" Language="en_US">
      <Text>Description of what the ability does in gameplay terms.</Text>
    </Row>
    
    <!-- Unit -->
    <Row Tag="LOC_UNIT_YOUR_CIV_UNIQUE_NAME" Language="en_US">
      <Text>Your Unique Unit</Text>
    </Row>
    <Row Tag="LOC_UNIT_YOUR_CIV_UNIQUE_DESCRIPTION" Language="en_US">
      <Text>Description of your unique unit and its special abilities.</Text>
    </Row>
    
    <!-- Building -->
    <Row Tag="LOC_BUILDING_YOUR_CIV_UNIQUE_NAME" Language="en_US">
      <Text>Your Unique Building</Text>
    </Row>
    <Row Tag="LOC_BUILDING_YOUR_CIV_UNIQUE_DESCRIPTION" Language="en_US">
      <Text>Description of your unique building and its benefits.</Text>
    </Row>
    
    <!-- Tradition -->
    <Row Tag="LOC_TRADITION_YOUR_CIV_UNIQUE_NAME" Language="en_US">
      <Text>Your Unique Tradition</Text>
    </Row>
    <Row Tag="LOC_TRADITION_YOUR_CIV_UNIQUE_DESCRIPTION" Language="en_US">
      <Text>Description of your unique tradition and its effects.</Text>
    </Row>
  </LocalizedText>
</GameData>
```

## Testing Your Civilization

1. **Install the Mod:**
   - Place your mod folder in the Civilization VII mods directory (`<GAME_DATA>/Mods/`)
   - Enable the mod in the game's mod menu

2. **Initial Testing:**
   - Start a new game with your civilization
   - Verify all components appear correctly
   - Test unique abilities, units, and buildings

3. **Age Transition Testing:**
   - Play through an Age transition
   - Verify traditions persist correctly
   - Check for any unexpected behavior

4. **Troubleshooting Common Issues:**
   - Missing icons: Check atlas references and file paths
   - Non-functioning abilities: Verify modifier connections
   - Game crashes: Check XML syntax and required fields
   - Unlocalized text: Ensure all text keys are properly defined

## Real-World Example: Scythia Civilization

The community-created Scythia civilization mod demonstrates these principles in action:

### Folder Structure
```
<GAME_DATA>/Mods/civmods-izica-civilization-scythia-38056/
├── civilizations/           # Civilization definition
├── traditions/              # Scythia-specific traditions
├── units/                   # Unique units like Saka Rider
├── constructibles/          # Buildings and improvements
├── progression-trees/       # Civic tree modifications
├── imports/                 # External assets 
└── izica-civilization-scythia.modinfo
```

### Key Implementation Features
- **Age Assignment**: Positioned in the Antiquity Age
- **Unique Units**: Includes the Saka Rider with increased mobility
- **Civilization Ability**: Features bonuses related to horseback warfare and plains biomes
- **Localization**: Complete text definitions in multiple languages
- **Progression Tree Integration**: Custom civic nodes for unlocking traditions

## Conclusion

Creating a new civilization for Civilization VII involves careful planning, understanding of the Age system, and implementation of multiple interconnected components. By following this guide and studying examples like the Scythia mod, you can create balanced, engaging civilizations that seamlessly integrate with the game's architecture.

For additional guidance, refer to:
- [Modding Architecture](/guides/modding-architecture.md)
- [Database Schemas](/guides/database-schemas.md)
- [Age Modules](/guides/age-modules.md)
- [DLC and Community Mod Patterns](/guides/mod-patterns.md) 