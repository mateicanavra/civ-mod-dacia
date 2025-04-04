# General Guide: Creating Leaders in Civilization VII

## Overview

This guide provides a comprehensive approach to creating new leaders for Civilization VII. It covers the process from planning through implementation and testing, with practical examples from official DLC and community mods.

## Prerequisites

Before creating a new leader, ensure you have:

1. A basic understanding of the Civilization VII modding architecture (see [Modding Architecture](/guides/modding-architecture.md))
2. Familiarity with the database schema for leaders (see [Database Schemas](/guides/database-schemas.md))
3. Knowledge of how leaders integrate with civilizations (see [General: Creating Civilizations](/guides/general-creating-civilizations.md))
4. The game's base files for reference

## Planning Your Leader

### Leader Design Principles

When designing a new leader, consider these key aspects:

1. **Historical Accuracy**: Research the historical figure to ensure authentic representation
2. **Gameplay Niche**: Design abilities that offer unique gameplay opportunities
3. **Synergy with Civilization**: Ensure abilities complement the civilization's strengths
4. **Age Appropriate**: Design abilities that fit the leader's historical Age
5. **Balance**: Create abilities that are powerful but not game-breaking
6. **Specialization Category**: Determine which gameplay category your leader excels in (Military, Diplomacy, Expansion, Science, Culture, or Economy)

### Core Design Elements

Define these essential elements for your leader:

1. **Leader Name**: The primary identifier
2. **Historical Period**: The time period when the leader was most influential
3. **Core Abilities**: The unique gameplay advantages
4. **Diplomatic Agendas**: Leaders' AI behavior and preferences
5. **Civilization Compatibility**: Which civilizations the leader can lead
6. **Leader Type**: Consider whether your leader is a traditional political figure, or represents excellence in other domains like science, philosophy, or human rights

## Folder Structure Setup

Organize your leader mod with a clear folder structure:

```
<GAME_DATA>/Mods/your-leader-mod/
├── leaders/             # Leader definitions
├── agendas/             # Agenda definitions
├── traits/              # Trait definitions
├── assets/              # Leader assets (if you're replacing/adding visuals)
├── text/                # Localization files
└── your-mod-name.modinfo # Mod metadata file
```

## Implementation Process

### 1. Creating the Modinfo File

The `.modinfo` file defines your mod's metadata and structure:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Mod id="your-leader-mod" version="1" xmlns="ModInfo">
  <Properties>
    <n>Your Leader Name</n>
    <Description>Description of your leader mod</Description>
    <Authors>Your Name</Authors>
    <Version>1.0.0</Version>
    <AffectsSavedGames>1</AffectsSavedGames>
  </Properties>
  <Dependencies>
    <Mod id="base-standard" title="LOC_MODULE_BASE_STANDARD_NAME"/>
  </Dependencies>
  <ActionCriteria>
    <Criteria id="always">
      <AlwaysMet/>
    </Criteria>
  </ActionCriteria>
  <ActionGroups>
    <ActionGroup id="main-group" scope="game" criteria="always">
      <Actions>
        <UpdateDatabase>
          <Item>leaders/your_leader.xml</Item>
          <Item>agendas/your_leader_agendas.xml</Item>
          <Item>text/your_leader_text.xml</Item>
          <!-- Add more files as needed -->
        </UpdateDatabase>
      </Actions>
    </ActionGroup>
  </ActionGroups>
</Mod>
```

### 2. Defining Your Leader

Create a file like `leaders/your_leader.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <!-- Define Leader Type -->
  <Types>
    <Row Type="LEADER_YOUR_LEADER" Kind="KIND_LEADER"/>
  </Types>
  
  <!-- Basic Leader Definition -->
  <Leaders>
    <Row LeaderType="LEADER_YOUR_LEADER" 
         Name="LOC_LEADER_YOUR_LEADER_NAME" 
         InheritFrom="LEADER_DEFAULT" 
         Description="LOC_LEADER_YOUR_LEADER_DESCRIPTION"/>
  </Leaders>
  
  <!-- Leader Traits -->
  <LeaderTraits>
    <Row LeaderType="LEADER_YOUR_LEADER" TraitType="TRAIT_LEADER_YOUR_ABILITY"/>
  </LeaderTraits>
  
  <!-- Civilization Compatibility -->
  <LeaderCivilizations>
    <Row LeaderType="LEADER_YOUR_LEADER" CivilizationType="CIVILIZATION_YOUR_CIV"/>
    <!-- Can add multiple civilizations if desired -->
  </LeaderCivilizations>
  
  <!-- Leader Agendas -->
  <HistoricalAgendas>
    <Row LeaderType="LEADER_YOUR_LEADER" AgendaType="AGENDA_YOUR_HISTORICAL"/>
  </HistoricalAgendas>
  
  <RandomAgendas>
    <Row AgendaType="AGENDA_YOUR_RANDOM" LeaderType="LEADER_YOUR_LEADER"/>
  </RandomAgendas>
</Database>
```

### 3. Creating Leader Abilities

Define your leader's unique abilities in `traits/your_leader_traits.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <!-- Define Trait Type -->
  <Types>
    <Row Type="TRAIT_LEADER_YOUR_ABILITY" Kind="KIND_TRAIT"/>
  </Types>
  
  <!-- Basic Trait Definition -->
  <Traits>
    <Row TraitType="TRAIT_LEADER_YOUR_ABILITY" 
         Name="LOC_TRAIT_LEADER_YOUR_ABILITY_NAME" 
         Description="LOC_TRAIT_LEADER_YOUR_ABILITY_DESCRIPTION"/>
  </Traits>
  
  <!-- Define the trait effects through modifiers -->
  <Modifiers>
    <Row ModifierId="MODIFIER_YOUR_LEADER_ABILITY_EFFECT" 
         ModifierType="MODIFIER_PLAYER_ADJUST_YIELD_CHANGE"/>
  </Modifiers>
  
  <ModifierArguments>
    <Row ModifierId="MODIFIER_YOUR_LEADER_ABILITY_EFFECT" 
         Name="YieldType" Value="YIELD_SCIENCE"/>
    <Row ModifierId="MODIFIER_YOUR_LEADER_ABILITY_EFFECT" 
         Name="Amount" Value="10"/>
  </ModifierArguments>
  
  <!-- Link trait to modifiers -->
  <TraitModifiers>
    <Row TraitType="TRAIT_LEADER_YOUR_ABILITY" 
         ModifierId="MODIFIER_YOUR_LEADER_ABILITY_EFFECT"/>
  </TraitModifiers>
</Database>
```

### 4. Creating Leader Agendas

Define your leader's agenda in `agendas/your_leader_agendas.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <!-- Define Agenda Types -->
  <Types>
    <Row Type="AGENDA_YOUR_HISTORICAL" Kind="KIND_AGENDA"/>
    <Row Type="AGENDA_YOUR_RANDOM" Kind="KIND_AGENDA"/>
  </Types>
  
  <!-- Basic Agenda Definitions -->
  <Agendas>
    <Row AgendaType="AGENDA_YOUR_HISTORICAL" 
         Name="LOC_AGENDA_YOUR_HISTORICAL_NAME" 
         Description="LOC_AGENDA_YOUR_HISTORICAL_DESCRIPTION"/>
    <Row AgendaType="AGENDA_YOUR_RANDOM" 
         Name="LOC_AGENDA_YOUR_RANDOM_NAME" 
         Description="LOC_AGENDA_YOUR_RANDOM_DESCRIPTION"/>
  </Agendas>
  
  <!-- Agenda biases - define AI behavior -->
  <AgendaPreferredLeaders>
    <Row AgendaType="AGENDA_YOUR_HISTORICAL" LeaderType="LEADER_YOUR_LEADER"/>
  </AgendaPreferredLeaders>
  
  <!-- Define how the agenda affects AI behavior -->
  <AgendaBehaviors>
    <Row AgendaType="AGENDA_YOUR_HISTORICAL" BehaviorType="DIPLOACTION_DENOUNCE"/>
  </AgendaBehaviors>
  
  <!-- Define conditions that make the AI like other players -->
  <AgendaPlayerFavoredValues>
    <Row AgendaType="AGENDA_YOUR_HISTORICAL" ObjectiveType="OBJECTIVE_BUILD_WONDERS" ValueType="DIPLOVALUETYPE_POSITIVE" FavoredValue="10"/>
  </AgendaPlayerFavoredValues>
  
  <!-- Define conditions that make the AI dislike other players -->
  <AgendaPlayerHatedValues>
    <Row AgendaType="AGENDA_YOUR_HISTORICAL" ObjectiveType="OBJECTIVE_RAZE_CITIES" ValueType="DIPLOVALUETYPE_NEGATIVE" HatedValue="-10"/>
  </AgendaPlayerHatedValues>
</Database>
```

### 5. Setting Up Localization

Create localization files in `text/your_leader_text.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GameData>
  <LocalizedText>
    <!-- Leader -->
    <Row Tag="LOC_LEADER_YOUR_LEADER_NAME" Language="en_US">
      <Text>Your Leader Name</Text>
    </Row>
    <Row Tag="LOC_LEADER_YOUR_LEADER_DESCRIPTION" Language="en_US">
      <Text>Historical description of your leader.</Text>
    </Row>
    
    <!-- Leader Trait -->
    <Row Tag="LOC_TRAIT_LEADER_YOUR_ABILITY_NAME" Language="en_US">
      <Text>Your Leader Ability Name</Text>
    </Row>
    <Row Tag="LOC_TRAIT_LEADER_YOUR_ABILITY_DESCRIPTION" Language="en_US">
      <Text>Description of what the ability does in gameplay terms.</Text>
    </Row>
    
    <!-- Historical Agenda -->
    <Row Tag="LOC_AGENDA_YOUR_HISTORICAL_NAME" Language="en_US">
      <Text>Your Historical Agenda Name</Text>
    </Row>
    <Row Tag="LOC_AGENDA_YOUR_HISTORICAL_DESCRIPTION" Language="en_US">
      <Text>Description of the historical agenda's effects.</Text>
    </Row>
    
    <!-- Random Agenda -->
    <Row Tag="LOC_AGENDA_YOUR_RANDOM_NAME" Language="en_US">
      <Text>Your Random Agenda Name</Text>
    </Row>
    <Row Tag="LOC_AGENDA_YOUR_RANDOM_DESCRIPTION" Language="en_US">
      <Text>Description of the random agenda's effects.</Text>
    </Row>
  </LocalizedText>
</GameData>
```

## Advanced Leader Implementations

### Creating Leader Personas

Civilization VII allows for implementing multiple personas for the same historical figure, representing different periods or aspects of their life:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <!-- Define multiple leader personas -->
  <Types>
    <Row Type="LEADER_YOUR_LEADER" Kind="KIND_LEADER"/>
    <Row Type="LEADER_YOUR_LEADER_ALT" Kind="KIND_LEADER"/>
  </Types>
  
  <!-- Basic Leader Definitions -->
  <Leaders>
    <Row LeaderType="LEADER_YOUR_LEADER" 
         Name="LOC_LEADER_YOUR_LEADER_NAME" 
         InheritFrom="LEADER_DEFAULT" 
         Description="LOC_LEADER_YOUR_LEADER_DESCRIPTION"/>
    <Row LeaderType="LEADER_YOUR_LEADER_ALT" 
         Name="LOC_LEADER_YOUR_LEADER_ALT_NAME" 
         InheritFrom="LEADER_DEFAULT" 
         Description="LOC_LEADER_YOUR_LEADER_ALT_DESCRIPTION"/>
  </Leaders>
  
  <!-- Different traits for different personas -->
  <LeaderTraits>
    <Row LeaderType="LEADER_YOUR_LEADER" TraitType="TRAIT_LEADER_FIRST_PERSONA"/>
    <Row LeaderType="LEADER_YOUR_LEADER_ALT" TraitType="TRAIT_LEADER_SECOND_PERSONA"/>
  </LeaderTraits>
  
  <!-- Different agendas for different personas -->
  <HistoricalAgendas>
    <Row LeaderType="LEADER_YOUR_LEADER" AgendaType="AGENDA_FIRST_PERSONA"/>
    <Row LeaderType="LEADER_YOUR_LEADER_ALT" AgendaType="AGENDA_SECOND_PERSONA"/>
  </HistoricalAgendas>
</Database>
```

Personas typically require separate asset files for different visual appearances and may focus on different gameplay categories.

### Age-Specific Leader Features

In Civilization VII, leaders may have different abilities depending on the current Age. You can implement this as follows:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <!-- Age-specific traits -->
  <LeaderTraits>
    <Row LeaderType="LEADER_YOUR_LEADER" TraitType="TRAIT_LEADER_ANTIQUITY_BONUS"/>
    <Row LeaderType="LEADER_YOUR_LEADER" TraitType="TRAIT_LEADER_EXPLORATION_BONUS"/>
  </LeaderTraits>
  
  <!-- Define age-specific modifiers -->
  <Modifiers>
    <Row ModifierId="MODIFIER_LEADER_ANTIQUITY_EFFECT" 
         ModifierType="MODIFIER_PLAYER_CITIES_ADJUST_BUILDING_YIELD_CHANGE"
         SubjectRequirementSetId="REQUIREMENTSET_ANTIQUITY_AGE_ACTIVE"/>
  </Modifiers>
  
  <!-- Requirement set for Age detection -->
  <RequirementSets>
    <Row RequirementSetId="REQUIREMENTSET_ANTIQUITY_AGE_ACTIVE" 
         RequirementSetType="REQUIREMENTSET_TEST_ALL"/>
  </RequirementSets>
  
  <RequirementSetRequirements>
    <Row RequirementSetId="REQUIREMENTSET_ANTIQUITY_AGE_ACTIVE" 
         RequirementId="REQUIREMENT_GAME_IN_ANTIQUITY_AGE"/>
  </RequirementSetRequirements>
  
  <Requirements>
    <Row RequirementId="REQUIREMENT_GAME_IN_ANTIQUITY_AGE" 
         RequirementType="REQUIREMENT_GAME_AGE_MATCHES"/>
  </Requirements>
  
  <RequirementArguments>
    <Row RequirementId="REQUIREMENT_GAME_IN_ANTIQUITY_AGE" 
         Name="AgeType" Value="AGE_ANTIQUITY"/>
  </RequirementArguments>
</Database>
```

### Cross-Age Leaders

Some leaders can be designed to span multiple Ages, with special abilities that evolve or change across Ages:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <!-- Leader traits for multiple ages -->
  <LeaderTraits>
    <Row LeaderType="LEADER_YOUR_LEADER" TraitType="TRAIT_LEADER_CROSS_AGE_ABILITY"/>
  </LeaderTraits>
  
  <!-- Modifiers that activate different effects based on age -->
  <Modifiers>
    <Row ModifierId="MODIFIER_LEADER_ANTIQUITY_EFFECT" 
         ModifierType="MODIFIER_PLAYER_ADJUST_UNIT_PRODUCTION"
         SubjectRequirementSetId="REQUIREMENTSET_ANTIQUITY_AGE_ACTIVE"/>
         
    <Row ModifierId="MODIFIER_LEADER_EXPLORATION_EFFECT" 
         ModifierType="MODIFIER_PLAYER_ADJUST_TRADE_ROUTE_YIELD"
         SubjectRequirementSetId="REQUIREMENTSET_EXPLORATION_AGE_ACTIVE"/>
         
    <Row ModifierId="MODIFIER_LEADER_MODERN_EFFECT" 
         ModifierType="MODIFIER_PLAYER_ADJUST_TECHNOLOGY_SPEED"
         SubjectRequirementSetId="REQUIREMENTSET_MODERN_AGE_ACTIVE"/>
  </Modifiers>
  
  <!-- Link all modifiers to the same trait -->
  <TraitModifiers>
    <Row TraitType="TRAIT_LEADER_CROSS_AGE_ABILITY" 
         ModifierId="MODIFIER_LEADER_ANTIQUITY_EFFECT"/>
    <Row TraitType="TRAIT_LEADER_CROSS_AGE_ABILITY" 
         ModifierId="MODIFIER_LEADER_EXPLORATION_EFFECT"/>
    <Row TraitType="TRAIT_LEADER_CROSS_AGE_ABILITY" 
         ModifierId="MODIFIER_LEADER_MODERN_EFFECT"/>
  </TraitModifiers>
</Database>
```

## Implementing Leader Categories

Civilization VII categorizes leaders into gameplay specializations. Implement this as follows:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <!-- Define leader category -->
  <LeaderCategories>
    <Row LeaderType="LEADER_YOUR_LEADER" CategoryType="LEADER_CATEGORY_SCIENCE"/>
  </LeaderCategories>
  
  <!-- Secondary categories if applicable -->
  <LeaderSecondaryCategories>
    <Row LeaderType="LEADER_YOUR_LEADER" CategoryType="LEADER_CATEGORY_CULTURE"/>
  </LeaderSecondaryCategories>
</Database>
```

Available categories include:
- LEADER_CATEGORY_MILITARY - For combat-focused leaders
- LEADER_CATEGORY_DIPLOMACY - For leaders focused on international relations
- LEADER_CATEGORY_EXPANSION - For leaders specializing in growth and territory
- LEADER_CATEGORY_SCIENCE - For leaders emphasizing technological advancement
- LEADER_CATEGORY_CULTURE - For leaders with strengths in cultural development
- LEADER_CATEGORY_ECONOMY - For leaders focusing on trade and production

## Implementing Leader Diplomacy

Leaders in Civilization VII have unique diplomatic interactions. Implement them as follows:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <!-- Define leader-specific diplomacy responses -->
  <DiplomaticResponses>
    <Row Type="DIPLORESPONSE_YOUR_LEADER_GREET_FIRST" 
         LeaderType="LEADER_YOUR_LEADER" 
         DiplomaticStateType="DIPLO_STATE_FIRST_MEET"
         Sentiment="SENTIMENT_FRIENDLY"
         ResponseText="LOC_DIPLO_FIRST_MEET_YOUR_LEADER_FRIENDLY"/>
  </DiplomaticResponses>
  
  <!-- Define war declarations -->
  <DiplomaticWarResponses>
    <Row Type="DIPLORESPONSE_YOUR_LEADER_WAR_DECLARATION" 
         LeaderType="LEADER_YOUR_LEADER" 
         WarTypeType="WAR_TYPE_FORMAL"
         ResponseText="LOC_DIPLO_WAR_YOUR_LEADER_ANY"/>
  </DiplomaticWarResponses>
</Database>
```

## Testing Your Leader

1. **Install the Mod:**
   - Place your mod folder in the Civilization VII mods directory (`<GAME_DATA>/Mods/`)
   - Enable the mod in the game's mod menu

2. **Initial Testing:**
   - Start a new game with your leader
   - Verify the leader appears with correct name and civilization
   - Test unique abilities function as intended
   - Observe AI agenda behavior

3. **Advanced Testing:**
   - Test Age transitions to ensure abilities work across Ages
   - Test diplomacy and agenda responses
   - Test interactions with other leaders
   - Verify localization and text appears correctly

4. **Troubleshooting Common Issues:**
   - Missing leader in selection screen: Check leader definition
   - Non-functioning abilities: Verify modifier connections
   - Missing text: Check localization keys
   - Game crashes: Check XML syntax and required fields

## Real-World Example: Cyrus of Persia

The official DLC leader Cyrus of Persia demonstrates these principles in action:

### Basic Structure
```
<GAME_RESOURCES>/DLC/persian-dlc/
├── leaders/           # Cyrus leader definition
├── agendas/           # Persian Empire agenda
├── traits/            # Royal Road ability definition
├── text/              # Localization files
└── persians.modinfo   # Mod metadata
```

### Key Implementation Features
- **Age Assignment**: Positioned in the Antiquity Age
- **Leader Ability**: "Royal Road" provides movement and gold bonuses to trade routes
- **Historical Agenda**: "Persian Empire" focuses on territorial expansion
- **Civilization Compatibility**: Compatible with the Persian civilization
- **Age-Specific Bonuses**: Different bonuses for Antiquity vs. Exploration Age

## Practical Examples

### Basic Leader Ability with Civilization Synergy

```xml
<!-- Leader definition -->
<Row LeaderType="LEADER_CYRUS" CivilizationType="CIVILIZATION_PERSIA"/>

<!-- Leader trait that works with civilization trait -->
<Modifiers>
  <Row ModifierId="MODIFIER_CYRUS_TRADE_ROUTE_GOLD" 
       ModifierType="MODIFIER_PLAYER_ADJUST_TRADE_ROUTE_YIELD"/>
</Modifiers>

<ModifierArguments>
  <Row ModifierId="MODIFIER_CYRUS_TRADE_ROUTE_GOLD" 
       Name="YieldType" Value="YIELD_GOLD"/>
  <Row ModifierId="MODIFIER_CYRUS_TRADE_ROUTE_GOLD" 
       Name="Amount" Value="2"/>
</ModifierArguments>
```

### Conditional Leader Ability

```xml
<!-- Ability that only applies when certain conditions are met -->
<Modifiers>
  <Row ModifierId="MODIFIER_LEADER_CULTURAL_BONUS" 
       ModifierType="MODIFIER_PLAYER_ADJUST_YIELD_CHANGE"
       SubjectRequirementSetId="REQUIREMENTSET_HAS_BUILT_WONDER"/>
</Modifiers>

<RequirementSets>
  <Row RequirementSetId="REQUIREMENTSET_HAS_BUILT_WONDER" 
       RequirementSetType="REQUIREMENTSET_TEST_ALL"/>
</RequirementSets>

<Requirements>
  <Row RequirementId="REQUIREMENT_PLAYER_HAS_WONDER" 
       RequirementType="REQUIREMENT_PLAYER_HAS_WONDER"/>
</Requirements>
```

## Conclusion

Creating a leader for Civilization VII involves designing unique abilities, agendas, and diplomatic behaviors that complement their associated civilizations. With the mix-and-match functionality, leaders can now be paired with different civilizations, opening up new strategic possibilities. The expanded concept of leadership also allows for creating figures who made contributions outside traditional political realms.

For additional guidance, refer to:
- [Modding Architecture](/guides/modding-architecture.md)
- [Database Schemas](/guides/database-schemas.md)
- [General: Creating Civilizations](/guides/general-creating-civilizations.md)
- [DLC and Community Mod Patterns](/guides/mod-patterns.md)
