# Civilization VII Age Modules

## Overview

One of Civilization VII's defining features is its Ages system, which divides gameplay into three distinct historical periods. Each Age is implemented as a separate module with its own content, rules, and mechanics. This document provides an in-depth look at each Age module's structure, content, and implementation specifics.

Understanding the Age modules is essential for modders, as any content you create must be properly integrated with the appropriate Age to function correctly in-game.

## Common Age Module Structure

All three Age modules (Antiquity, Exploration, and Modern) share a common structure and organization. Based on examination of the game files, each Age module contains:

```
age-[name]/
├── age-[name].modinfo       # Module definition
├── package.json             # Package metadata
├── config/                  # Configuration settings
│   └── config.xml           # Core Age properties
├── data/                    # Gameplay data files
│   ├── civilizations.xml    # Age-specific civilizations
│   ├── leaders.xml          # Leader data
│   ├── units.xml            # Units available in this Age
│   ├── constructibles.xml   # Buildings and improvements
│   ├── progression-trees-*.xml  # Tech and civic trees
│   └── various other XMLs   # Age-specific content
├── text/                    # Localization files
├── l10n/                    # Additional localization
├── ui/                      # UI element definitions
├── scripts/                 # JavaScript files
├── metaprogression/         # Progression systems
└── movies/                  # Media resources
```

### Core Files in Each Age

The most important files that define an Age's characteristics are:

1. **age-[name].modinfo**: Defines module identity, dependencies, and actions
2. **config/config.xml**: Contains the core properties of the Age including:
   - Available civilizations
   - Victory conditions for the Age
   - Legacy Path systems
   - Map and world settings
3. **data/civilizations.xml**: Defines civilizations available in that Age
4. **data/progression-trees-*.xml**: Technology and Civic trees specific to the Age

## Antiquity Age (3000 BCE - 500 CE)

The Antiquity Age represents the ancient world from the earliest civilizations through classical antiquity.

**Module Path**: `<GAME_RESOURCES>/Base/modules/age-antiquity/`

### Key Properties

As defined in the Antiquity Age's config.xml:

```xml
<Ages>
    <Row AgeType="AGE_ANTIQUITY" 
         Name="LOC_AGE_ANTIQUITY_NAME" 
         Description="LOC_AGE_ANTIQUITY_DESCRIPTION" 
         PlayerCivilizationDomain="AntiquityAgeCivilizations" 
         DefeatDomain="AntiquityDefeats" 
         VictoryDomain="AntiquityVictories" 
         MapSizeDomain="DistantLandsMapSizes" 
         Banner="age-sel_antiquity_full" 
         SortIndex="10"/>
</Ages>
```

### Antiquity Civilizations

The Antiquity Age includes civilizations from ancient history:

- **Egypt**: Known for early urbanization and monumental architecture
- **Rome**: Famous for military prowess and governance systems
- **Greece**: Celebrated for cultural and philosophical achievements
- **Persia**: Known for imperial administration and tolerance
- **Han**: Famous for technological innovation and bureaucracy
- **Aksum**: Known for trade networks and early Christianity
- **Khmer**: Celebrated for water management and temple complexes
- **Maurya**: Famous for unification and Buddhist governance
- **Maya**: Known for astronomical knowledge and urban planning
- **Mississippian**: Known for mound-building and trade networks

Each civilization is defined in the civilizations.xml file with unique abilities, units, and buildings designed to reflect their historical strengths during this era.

### Unique Age Features

The Antiquity Age has several distinctive features:

1. **Resources**: Resources specific to ancient times (incense, jade, marble)
2. **Wonders**: Ancient world wonders like the Pyramids and Colosseum
3. **Religion**: Basic religious systems (plays a minor role compared to later Ages)
4. **Independent Powers**: Early tribal entities and city-states
5. **Technology Tree**: Focuses on basic technologies (writing, bronze working, mathematics)
6. **Civic Tree**: Emphasis on early governance forms and social organization

### Key Data Files

- **data/progression-trees-tech.xml**: Antiquity Age tech tree
- **data/progression-trees-culture-common.xml**: Civic tree for the Age
- **data/religion.xml**: Religious systems (limited in this Age)
- **data/constructibles.xml**: Buildings and improvements
- **data/units.xml**: Military and civilian units
- **data/victories.xml**: Victory condition definitions

## Exploration Age (500 CE - 1700 CE)

The Exploration Age covers the medieval period through the early modern era, focusing on global exploration, cultural exchange, and religious developments.

**Module Path**: `<GAME_RESOURCES>/Base/modules/age-exploration/`

### Key Properties

As defined in the Exploration Age's config.xml:

```xml
<Ages>
    <Row AgeType="AGE_EXPLORATION" 
         Name="LOC_AGE_EXPLORATION_NAME" 
         Description="LOC_AGE_EXPLORATION_DESCRIPTION" 
         PlayerCivilizationDomain="ExplorationAgeCivilizations" 
         DefeatDomain="ExplorationDefeats" 
         VictoryDomain="ExplorationVictories" 
         MapSizeDomain="DistantLandsMapSizes" 
         Banner="age-sel_exploration_full" 
         SortIndex="20"/>
</Ages>
```

### Exploration Civilizations

The Exploration Age includes civilizations from the medieval and early modern periods:

- **Spain**: Known for global exploration and colonization
- **Norman**: Famous for feudal administration and military innovation
- **Ming**: Celebrated for naval expeditions and cultural flourishing
- **Abbasid**: Known for scientific advancement and cultural preservation
- **Chola**: Famous for maritime trade and temple architecture
- **Hawaii**: Known for social organization and navigation
- **Inca**: Celebrated for infrastructure and vertical farming
- **Majapahit**: Known for maritime empire and cultural syncretism
- **Mongolia**: Famous for cavalry warfare and imperial administration
- **Songhai**: Known for trade networks and Islamic scholarship

Each civilization has unique abilities tailored to the Age's focus on exploration, trade, and religious development.

### Unique Age Features

The Exploration Age has several distinctive features:

1. **Religion**: Enhanced religious mechanics (major gameplay driver in this Age)
2. **Naval Systems**: Advanced naval exploration and combat units
3. **Trade Networks**: Expanded trade routes and mechanics
4. **Legacies**: Age transition legacy bonuses from Antiquity
5. **Distant Lands**: Expanded map areas accessible through naval exploration
6. **World Religion**: Enhanced faith systems with competing beliefs
7. **Scientific Revolution**: Early modern scientific advancements

### Key Data Files

- **data/progression-trees-tech.xml**: Exploration Age tech tree
- **data/progression-trees-culture-common.xml**: Civic tree for the Age
- **data/religion.xml**: Enhanced religious systems
- **data/naval/**: Naval exploration and combat systems
- **data/trade/**: Trading mechanics and routes
- **data/legacies/**: Age transition legacy bonuses
- **data/distant-lands/**: Expanded map discovery

## Modern Age (1700 CE - Present)

The Modern Age represents industrialization, globalization, and technological advancement from the Industrial Revolution to the present day.

**Module Path**: `<GAME_RESOURCES>/Base/modules/age-modern/`

### Key Properties

As defined in the Modern Age's config.xml:

```xml
<Ages>
    <Row AgeType="AGE_MODERN" 
         Name="LOC_AGE_MODERN_NAME" 
         Description="LOC_AGE_MODERN_DESCRIPTION" 
         PlayerCivilizationDomain="ModernAgeCivilizations" 
         DefeatDomain="ModernDefeats" 
         VictoryDomain="ModernVictories" 
         MapSizeDomain="StandardMapSizes" 
         Banner="age-sel_modern_full" 
         SortIndex="30"/>
</Ages>
```

### Modern Civilizations

The Modern Age includes civilizations from the industrial era to contemporary times:

- **America**: Known for industrial might and cultural influence
- **Russia**: Famous for territorial expansion and scientific achievement
- **Prussia**: Celebrated for military organization and education
- **French Empire**: Known for cultural exports and republicanism
- **Mexico**: Known for revolutionary politics and cultural identity
- **Mughal**: Famous for architectural achievements and religious syncretism
- **Meiji**: Known for rapid modernization and industrial development
- **Qing**: Famous for bureaucratic governance and population management
- **Buganda**: Known for adaptive governance and strategic diplomacy
- **Siam**: Celebrated for maintaining independence and diplomatic skill

Each civilization has unique abilities reflecting modern governance, industrial capacity, or cultural influence.

### Unique Age Features

The Modern Age has several distinctive features:

1. **Victory Conditions**: Final victory condition definitions and requirements
2. **Global Systems**: World Congress, Climate Change, and other global mechanics
3. **Advanced Units**: Aircraft, tanks, and modern military units
4. **Projects**: Space race and other advanced scientific projects
5. **Diplomacy**: Enhanced diplomatic options and alliances
6. **Urban Development**: Advanced city planning and infrastructure
7. **Industrial Economy**: Factory production and resource exploitation

### Key Data Files

- **data/victories/**: Victory condition definitions
- **data/global-systems/**: World Congress and climate change
- **data/advanced-units/**: Modern military units
- **data/projects/**: Scientific and cultural projects
- **data/diplomacy/**: Enhanced diplomatic options

## Age Transitions

One of the most important features of Civilization VII is the transition between Ages. As Creative Director Ed Beach explains, Age transitions address issues like snowballing, excessive micromanagement, and civilization balancing.

### Legacy Paths

Each Age has four Legacy Paths that players can progress along:

1. **Science Path**: Scientific and technological achievements
2. **Military Path**: Combat victories and military development
3. **Culture Path**: Cultural and civic advancements
4. **Economy Path**: Resource production and financial growth

Completing milestones on these paths provides Legacy Points that contribute to Age Progress, and completing an entire path unlocks a Golden Age Legacy for the next Age. Failing to complete any milestones on a path results in a Dark Age Legacy.

### Persistent Elements During Transitions

During Age transitions, several elements persist to maintain continuity:

1. **Leaders**: Leaders remain the same across all Ages
2. **Ageless Buildings**: Wonders and unique Quarters persist as "Ageless" structures
3. **Commander Units**: Commander attributes and experience continue
4. **Traditions**: Social policies unlocked from Civic trees remain available

As Edward Zhang, Economics Feature Lead, notes: "Leaders persist across all Ages, ensuring you always have a sense of who is part of your empire, and who are your rivals. Relationships that you develop persist, and Leaders continue to grow by leveling up their attributes."

### Implementation in XML

Age transitions are defined in various XML files, including:

```xml
<!-- <GAME_RESOURCES>/Base/modules/base-standard/definitions/age-transition.civdb defines the structure -->
<!-- While each age module implements specific transitions -->

<AgeTransitions>
    <Row FromAgeType="AGE_ANTIQUITY" 
         ToAgeType="AGE_EXPLORATION" 
         LegacyPointsRequired="100" 
         UnlocksNewLeaders="true"
         LeaderDomain="StandardLeaders" />
</AgeTransitions>

<LegacyPaths>
    <Row LegacyPathType="LEGACY_PATH_SCIENCE" 
         Name="LOC_LEGACY_PATH_SCIENCE_NAME" 
         Description="LOC_LEGACY_PATH_SCIENCE_DESCRIPTION"
         Icon="ICON_LEGACY_PATH_SCIENCE" />
    <!-- Military, Culture, Economy paths similarly defined -->
</LegacyPaths>

<LegacyTiers>
    <Row LegacyPathType="LEGACY_PATH_SCIENCE" 
         Tier="1" 
         PointsRequired="20" 
         RewardType="LEGACY_REWARD_SCIENCE_1" />
    <!-- Additional tiers with increasing requirements -->
</LegacyTiers>
```

## Assigning Content to Specific Ages

When creating mods, you need to properly assign your content to the appropriate Age(s). This determines when the content is available to players.

### Civilization Assignment

To assign a civilization to an Age, use the CivilizationDomains table:

```xml
<CivilizationDomains>
    <Row CivilizationType="CIVILIZATION_DACIA" Domain="AntiquityAgeCivilizations" />
</CivilizationDomains>
```

The available domains are:
- **AntiquityAgeCivilizations**: For civilizations starting in the Antiquity Age
- **ExplorationAgeCivilizations**: For civilizations in the Exploration Age
- **ModernAgeCivilizations**: For civilizations in the Modern Age

### Unit Assignment

Units can be assigned to specific Ages through tech tree prerequisites:

```xml
<Units>
    <Row UnitType="UNIT_DACIA_FALX_WARRIOR" 
         PrereqTech="TECH_IRON_WORKING" />
</Units>
```

By using a tech available only in a specific Age, you restrict when the unit becomes available.

### Building Assignment

Similarly, buildings can be restricted to Ages:

```xml
<Buildings>
    <Row BuildingType="BUILDING_DACIA_FORTRESS" 
         PrereqTech="TECH_ENGINEERING" />
</Buildings>
```

### Cross-Age Content

Some content can exist across multiple Ages. Define this by setting appropriate values:

```xml
<Buildings>
    <Row BuildingType="BUILDING_WORLD_WONDER_PYRAMIDS" 
         IsAgeless="true" />
</Buildings>
```

The "IsAgeless" property ensures the content persists across Age transitions.

## Practical Implementation Tips

When creating mods that work with the Age system, consider these best practices:

1. **Age-Appropriate Content**: Design civilizations, units, and buildings that fit the historical context of their Age
2. **Proper Dependencies**: Ensure your mod depends on the appropriate Age module(s)
3. **Transition Planning**: Consider how your content transitions between Ages (if at all)
4. **Age-Specific Mechanics**: Leverage the unique systems in each Age (religion in Exploration, global systems in Modern)
5. **Path Integration**: If adding new legacy paths or objectives, ensure they balance with existing content
6. **Leader Continuity**: Remember that leaders persist across Ages, so design leader abilities that remain relevant

## Cross-References

- For core architecture concepts, see [Civilization VII Modding Architecture](/guides/modding-architecture.md)
- For database schema details, see [Database Schemas](/guides/database-schemas.md)
- For civilization creation guides, see [Creating New Civilizations](/guides/creating-civilizations.md)

## Conclusion

The Age system is a foundational element of Civilization VII, significantly changing how content is organized and experienced. By understanding each Age module's unique characteristics and the transition mechanics between them, modders can create content that integrates seamlessly with the game's progression systems.

Whether creating an ancient civilization for the Antiquity Age, naval units for the Exploration Age, or modern systems for the final Age, proper integration with these module structures ensures your mod functions correctly and provides an authentic experience for players. 