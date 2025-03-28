# General Guide: Modifying Existing Content in Civilization VII

## Overview

This guide provides strategies and techniques for modifying existing content in Civilization VII. Instead of creating new elements from scratch, we'll focus on how to adjust, rebalance, and extend the game's existing civilizations, leaders, units, buildings, and gameplay systems.

## Prerequisites

Before modifying existing content, ensure you have:

1. A basic understanding of the Civilization VII modding architecture (see [Modding Architecture](/guides/modding-architecture.md))
2. Familiarity with the relevant database schemas (see [Database Schemas](/guides/database-schemas.md))
3. Knowledge of the Age system (see [Age Modules](/guides/age-modules.md))
4. The game's base files for reference
5. A working knowledge of creating content (see [General: Creating Civilizations](/guides/general-creating-civilizations.md) and [General: Creating Leaders](/guides/general-creating-leaders.md))

## Planning Your Modifications

### Modification Approaches

There are several approaches to modifying existing content:

1. **Override Approach**: Completely replace existing definitions
2. **Addition Approach**: Add new attributes or abilities to existing elements
3. **Adjustment Approach**: Alter specific values or parameters
4. **Removal Approach**: Disable or remove existing features
5. **Hybrid Approach**: Combine multiple approaches for comprehensive modifications

### Design Principles for Modifications

When modifying existing content, consider these key principles:

1. **Compatibility**: Ensure modifications work with the base game and other mods
2. **Balance**: Make changes that improve gameplay without creating overpowered elements
3. **Purpose**: Have a clear goal for each modification
4. **Theme Consistency**: Maintain the thematic integrity of the original elements
5. **Age Appropriateness**: Respect the Age system when modifying content

## Folder Structure Setup

Organize your modification mod with a clear folder structure:

```
<GAME_DATA>/Mods/your-mod-name/
├── civilization_overrides/   # Modifications to existing civilizations
├── leader_overrides/         # Modifications to existing leaders
├── unit_overrides/           # Modifications to existing units
├── building_overrides/       # Modifications to existing buildings
├── gameplay_overrides/       # Modifications to gameplay systems
├── text_overrides/           # Modified localization
└── your-mod-name.modinfo     # Mod metadata file
```

## Implementation Process

### 1. Creating the Modinfo File

The `.modinfo` file defines your mod's metadata and structure. Here's a real example from Sukritact's Inca Rework mod:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Mod id="sukritacts_inca_rework" version="1" xmlns="ModInfo">
  <Properties>
    <n>Sukritact's Inca Rework</n>
    <Description>Reworks the Inca Civilization</Description>
    <Authors>Sukritact</Authors>
    <Package>Mod</Package>
  </Properties>
  <Dependencies>
    <Mod id="base-standard" title="LOC_MODULE_BASE_STANDARD_NAME"/>
  </Dependencies>
  <References>
    <Mod id="sukritacts_assorted_adjustments" title="Sukritact's Assorted Adjustments"/>
  </References>
  <ActionCriteria>
    <Criteria id="exploration-age-current">
      <AgeInUse>AGE_EXPLORATION</AgeInUse>
    </Criteria>
    <Criteria id="exploration-age-persist" any="true">
      <AgeInUse>AGE_EXPLORATION</AgeInUse>
      <AgeInUse>AGE_MODERN</AgeInUse>
    </Criteria>
    <Criteria id="always">
      <AlwaysMet></AlwaysMet>
    </Criteria>
  </ActionCriteria>
  <ActionGroups>
    <ActionGroup id="game-suk-inca-rework-always" scope="shell" criteria="always">
      <Properties>
        <LoadOrder>20</LoadOrder>
      </Properties>
      <Actions>
        <UpdateText>
          <Item>text/Suk_Inca_Rework_Text.sql</Item>
        </UpdateText>
      </Actions>
    </ActionGroup>
    <ActionGroup id="game-suk-inca-rework-EXPPLUS" scope="game" criteria="exploration-age-persist">
      <Properties>
        <LoadOrder>20</LoadOrder>
      </Properties>
      <Actions>
        <UpdateText>
          <Item>text/Suk_Inca_Rework_Text.sql</Item>
        </UpdateText>
        <UpdateDatabase>
          <Item>data/Suk_Inca_Rework_Traditions.sql</Item>
          <Item>data/Suk_Inca_Rework_Constructibles.sql</Item>
        </UpdateDatabase>
      </Actions>
    </ActionGroup>
    <ActionGroup id="game-suk-inca-rework-EXP" scope="game" criteria="exploration-age-current">
      <Properties>
        <LoadOrder>20</LoadOrder>
      </Properties>
      <Actions>
        <UpdateDatabase>
          <Item>data/Suk_Inca_Rework_Civics.sql</Item>
        </UpdateDatabase>
      </Actions>
    </ActionGroup>
  </ActionGroups>
</Mod>
```

Note how this mod uses the `ActionCriteria` to ensure certain modifications only apply during specific Ages, implementing Age-aware changes to the Inca civilization.

### 2. Modifying Civilizations

To modify an existing civilization, you can use either SQL or XML approaches. Many community modders prefer SQL for its efficiency. Here's how Sukritact's Inca Rework mod modifies the Terrace Farm improvement:

```sql
-- Remove restrictive terrain requirement
UPDATE Constructibles
SET AdjacentTerrain = NULL
WHERE ConstructibleType = 'IMPROVEMENT_TERRACE_FARM';

-- Rebalance yields
UPDATE Constructible_YieldChanges
SET YieldChange = 3
WHERE ConstructibleType = 'IMPROVEMENT_TERRACE_FARM' AND YieldType = 'YIELD_FOOD';

-- Delete existing modifier and create a new one
DELETE FROM ConstructibleModifiers WHERE ModifierId = 'MOD_SUK_TERRACE_FARM_FOOD';
DELETE FROM Types WHERE Type = 'MOD_SUK_TERRACE_FARM_FOOD_TYPE';
DELETE FROM Modifiers WHERE ModifierId = 'MOD_SUK_TERRACE_FARM_FOOD';

-- Add new modifier with different requirements
INSERT INTO ConstructibleModifiers
  (ConstructibleType, ModifierId)
VALUES
  ('IMPROVEMENT_TERRACE_FARM', 'MOD_SUK_INCA_REWORK_TERRACE_FARM_FOOD');

-- Define remaining modifier components...
```

This approach uses SQL UPDATE, DELETE, and INSERT statements to modify existing game elements, replace their functionality, and rebalance their effects.

### 3. Modifying Leaders

To modify an existing leader, create a file like `leader_overrides/trajan_modifications.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <!-- Modify Leader Trait -->
  <Traits>
    <Update>
      <Where TraitType="TRAIT_LEADER_TRAJAN_ABILITY"/>
      <Set Description="LOC_TRAIT_LEADER_TRAJAN_ABILITY_MODIFIED_DESCRIPTION"/>
    </Update>
  </Traits>
  
  <!-- Add New Trait to Existing Leader -->
  <LeaderTraits>
    <Row LeaderType="LEADER_TRAJAN" TraitType="TRAIT_LEADER_ADDITIONAL_ABILITY"/>
  </LeaderTraits>
  
  <!-- Modify Leader Agenda -->
  <Agendas>
    <Update>
      <Where AgendaType="AGENDA_TRAJAN"/>
      <Set Description="LOC_AGENDA_TRAJAN_MODIFIED_DESCRIPTION"/>
    </Update>
  </Agendas>
  
  <!-- Modify Leader Categories -->
  <LeaderCategories>
    <Delete>
      <Where LeaderType="LEADER_TRAJAN" CategoryType="LEADER_CATEGORY_MILITARY"/>
    </Delete>
    <Row LeaderType="LEADER_TRAJAN" CategoryType="LEADER_CATEGORY_EXPANSION"/>
  </LeaderCategories>
</Database>
```

### 4. Modifying Units

Here's a real example from the Han Chukonu Rebalance mod showing how to modify an existing unit's stats and functionality:

```sql
-- Update unit properties
UPDATE Units
SET Tier="2", Maintenance="1" 
WHERE UnitType="UNIT_NU";
  
UPDATE Units
SET Tier="3", Maintenance="2" 
WHERE UnitType="UNIT_NU_2";

-- Update combat stats
UPDATE Unit_Stats
SET Combat = "15", RangedCombat="20", Bombard="15" 
WHERE UnitType="UNIT_NU";
  
UPDATE Unit_Stats
SET Combat = "20", RangedCombat="25", Bombard="20" 
WHERE UnitType="UNIT_NU_2";
  
-- Update production costs
UPDATE Unit_Costs
SET Cost="45" 
WHERE UnitType="UNIT_NU";

-- Change unit replacement relationship
UPDATE UnitReplaces
SET ReplacesUnitType = "UNIT_ARCHER" 
WHERE CivUniqueUnitType="UNIT_NU";

-- Remove the second unit tier's replacement relationship
DELETE FROM UnitReplaces 
WHERE CivUniqueUnitType = "UNIT_NU_2";

-- Change tech tree unlocks
UPDATE ProgressionTreeNodeUnlocks
SET ProgressionTreeNodeType="NODE_TECH_AQ_BRONZE_WORKING" 
WHERE TargetType="UNIT_NU";
```

The mod also adds an entirely new bonus modifier for the unit:

```sql
-- Add new Iron resource bonus for the Chu Ko Nu
INSERT INTO Types ([Type], Kind) VALUES
  ("MOD_IRON_CHU_KO_NU_COMBAT_STRENGTH_TYPE", "KIND_EFFECT");

INSERT INTO GameModifiers (ModifierId) VALUES
  ("MOD_IRON_CHU_KO_NU_COMBAT_STRENGTH");

INSERT INTO DynamicModifiers (ModifierType, CollectionType, EffectType) VALUES
  ("MOD_IRON_CHU_KO_NU_COMBAT_STRENGTH_TYPE", "COLLECTION_ALL_UNITS", "EFFECT_UNIT_ADJUST_COMBAT_STRENGTH_PER_RESOURCE");
  
-- Set up requirements so it only applies to this unit type
INSERT INTO RequirementSets (RequirementSetId, RequirementSetType) VALUES
  ("MOD_IRON_CHU_KO_NU_COMBAT_STRENGTH_REQUIREMENTS", "REQUIREMENTSET_TEST_ALL");
  
INSERT INTO Requirements (RequirementId, RequirementType) VALUES
  ("MOD_IRON_CHU_KO_NU_COMBAT_STRENGTH_REQUIREMENTS_1", "REQUIREMENT_UNIT_TAG_MATCHES");
  
INSERT INTO RequirementArguments (RequirementId, Name, [Value]) VALUES
  ("MOD_IRON_CHU_KO_NU_COMBAT_STRENGTH_REQUIREMENTS_1", "Tag", "UNIT_CLASS_NU");
```

This mod demonstrates the "Hybrid Approach" - adjusting numerical values while also removing some functionality and adding new capabilities.

### 5. Modifying Buildings and Infrastructure

To modify existing buildings, create a file like `building_overrides/building_modifications.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <!-- Modify Building Yields -->
  <Building_YieldChanges>
    <Update>
      <Where BuildingType="BUILDING_GRANARY" YieldType="YIELD_FOOD"/>
      <Set YieldChange="3"/>
    </Update>
    <Row BuildingType="BUILDING_GRANARY" YieldType="YIELD_PRODUCTION" YieldChange="1"/>
  </Building_YieldChanges>
  
  <!-- Modify Building Costs -->
  <Building_Costs>
    <Update>
      <Where BuildingType="BUILDING_GRANARY" YieldType="YIELD_PRODUCTION"/>
      <Set Cost="60"/>
    </Update>
  </Building_Costs>
  
  <!-- Modify Building Properties -->
  <Buildings>
    <Update>
      <Where BuildingType="BUILDING_GRANARY"/>
      <Set Description="LOC_BUILDING_GRANARY_MODIFIED_DESCRIPTION"/>
    </Update>
  </Buildings>
</Database>
```

### 6. Modifying Gameplay Systems

To modify core gameplay systems, create a file like `gameplay_overrides/gameplay_modifications.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <!-- Modify Research Costs -->
  <TechnologyCosts>
    <Update>
      <Where TechnologyType="TECH_MINING"/>
      <Set Cost="70"/>
    </Update>
  </TechnologyCosts>
  
  <!-- Modify Civic Costs -->
  <CivicCosts>
    <Update>
      <Where CivicType="CIVIC_EARLY_EMPIRE"/>
      <Set Cost="65"/>
    </Update>
  </CivicCosts>
  
  <!-- Modify Global Parameters -->
  <GlobalParameters>
    <Update>
      <Where Name="COMBAT_EXPERIENCE_PER_KILL"/>
      <Set Value="6"/>
    </Update>
    <Update>
      <Where Name="CITY_GROWTH_RATE"/>
      <Set Value="1.5"/>
    </Update>
  </GlobalParameters>
</Database>
```

### 7. Modifying Text and Localization

Create localization files in `text_overrides/modified_text.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GameData>
  <LocalizedText>
    <!-- Modified Trait Description -->
    <Row Tag="LOC_TRAIT_CIVILIZATION_ROME_ABILITY_MODIFIED_DESCRIPTION" Language="en_US">
      <Text>Modified description of Rome's civilization ability.</Text>
    </Row>
    
    <!-- Modified Leader Ability -->
    <Row Tag="LOC_TRAIT_LEADER_TRAJAN_ABILITY_MODIFIED_DESCRIPTION" Language="en_US">
      <Text>Modified description of Trajan's leader ability.</Text>
    </Row>
    
    <!-- Modified Agenda -->
    <Row Tag="LOC_AGENDA_TRAJAN_MODIFIED_DESCRIPTION" Language="en_US">
      <Text>Modified description of Trajan's agenda.</Text>
    </Row>
    
    <!-- Modified Building Description -->
    <Row Tag="LOC_BUILDING_GRANARY_MODIFIED_DESCRIPTION" Language="en_US">
      <Text>Modified description of the Granary building.</Text>
    </Row>
  </LocalizedText>
</GameData>
```

## Age-Aware Modifications

Civilization VII's Age system requires special consideration when modifying content. Sukritact's mods demonstrate best practices:

```xml
<ActionCriteria>
  <Criteria id="exploration-age-current">
    <AgeInUse>AGE_EXPLORATION</AgeInUse>
  </Criteria>
  <Criteria id="exploration-age-persist" any="true">
    <AgeInUse>AGE_EXPLORATION</AgeInUse>
    <AgeInUse>AGE_MODERN</AgeInUse>
  </Criteria>
</ActionCriteria>
```

Then in ActionGroups:

```xml
<ActionGroup id="game-suk-inca-rework-EXP" scope="game" criteria="exploration-age-current">
  <Actions>
    <UpdateDatabase>
      <Item>data/Suk_Inca_Rework_Civics.sql</Item>
    </UpdateDatabase>
  </Actions>
</ActionGroup>
```

This ensures that modifications only apply during appropriate Ages. For example, civic tree changes only apply in the Exploration Age, while some improvements persist into the Modern Age.

## SQL vs. XML Approach

While our guide has focused on both approaches, many successful community mods use SQL statements for modifications. Advantages include:

1. **Concise Syntax**: SQL modifications are often more compact than XML equivalents
2. **Direct Database Manipulation**: SQL directly modifies the game's internal database 
3. **Combined Operations**: Can perform updates, deletions, and insertions in a single file
4. **Familiar to Database Users**: Those with SQL experience find this approach intuitive

To use the SQL approach:
1. Create `.sql` files in your mod's data directory
2. Reference them in your `.modinfo` file with `<UpdateDatabase>` actions
3. Use standard SQL commands: UPDATE, DELETE, INSERT, etc.
4. Organize files by category (traditions, units, constructibles, etc.)

## Real-World Examples

### Sukritact's Inca Rework

This community mod reworks the Exploration Age Inca civilization:

#### Key Files
```
<GAME_DATA>/Mods/sukritacts_inca_rework/
├── data/
│   ├── Suk_Inca_Rework_Constructibles.sql  # Modifies Terrace Farm and Machu Pikchu
│   ├── Suk_Inca_Rework_Traditions.sql      # Reworks Inca traditions
│   └── Suk_Inca_Rework_Civics.sql          # Adjusts civic tree progression
├── text/
│   └── Suk_Inca_Rework_Text.sql            # Updates localization text
└── Sukritact's Inca Rework.modinfo         # Mod metadata with Age-aware criteria
```

#### Implementation Highlights
- Removes restrictive terrain requirements from the Terrace Farm improvement
- Rebalances yields to compensate for increased placement flexibility
- Changes Machu Pikchu wonder to require adjacent Urban District instead of biome restriction
- Implements Age-specific ActionGroups to ensure changes apply in appropriate game phases

### Han Chukonu Rebalance

This focused mod rebalances the Han civilization's unique unit:

#### Key Files
```
<GAME_DATA>/Mods/civmods-han_chukonu_rebalance-38120/
├── data/
│   └── BaseGame_Antiquity.sql  # Unit stat modifications and new Iron bonus
├── text/
│   └── (Localization text files)
└── han_chukonu_rebalance.modinfo
```

#### Implementation Highlights
- Adjusts the Chukonu (Nu) unit's combat stats, maintenance, and production costs
- Changes its tech tree progression path
- Adds a new bonus that gives +1 Combat Strength per Iron resource
- Alters the unit replacement relationship with standard units

## Testing Your Modifications

1. **Install the Mod:**
   - Place your mod folder in the Civilization VII mods directory (`<GAME_DATA>/Mods/`)
   - Enable the mod in the game's mod menu

2. **Initial Testing:**
   - Start a new game with the civilizations/leaders you've modified
   - Verify that your changes appear correctly
   - Test that modified abilities function as intended
   - Check for any unintended side effects

3. **Compatibility Testing:**
   - Test your mod alone with the base game
   - Test your mod with official DLC content
   - If possible, test with popular community mods

4. **Performance Testing:**
   - Ensure your modifications don't introduce performance issues
   - Particularly important for mods that make systemic changes

5. **Troubleshooting Common Issues:**
   - Changes not appearing: Check XML syntax and file references
   - Game crashes: Look for conflicts with base game definitions
   - Balance issues: Iteratively adjust values based on testing

## Best Practices for Modifying Content

1. **Document Your Changes:** Keep detailed notes of what you've modified and why.

2. **Use Clear Commenting:** Include comments in your XML files explaining your changes.

3. **Version Control:** Use version numbers in your mod files to track iterations.

4. **Incremental Changes:** Test small changes before making large overhauls.

5. **Respect Original Design:** Try to understand why the original design choices were made before changing them.

6. **Maintain Theming:** Ensure your changes fit the historical and thematic context of the elements you're modifying.

7. **Gather Feedback:** Share your mod with the community to get testing feedback.

## Conclusion

Modifying existing content in Civilization VII provides an opportunity to refine the game to your preferences, rebalance elements you find too weak or too strong, and create a more personalized gaming experience. By carefully planning your modifications and following good modding practices, you can create high-quality mods that enhance the game while maintaining compatibility with other content.

For additional guidance, refer to:
- [Modding Architecture](/guides/modding-architecture.md)
- [Database Schemas](/guides/database-schemas.md)
- [General: Creating Civilizations](/guides/general-creating-civilizations.md)
- [General: Creating Leaders](/guides/general-creating-leaders.md)
- [DLC and Community Mod Patterns](/guides/mod-patterns.md)
