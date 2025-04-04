# Age-Specific Gameplay Mechanics in Civilization VII

## Introduction

Civilization VII's Ages system fundamentally changes how the game progresses through history. Unlike previous Civilization titles that featured a continuous timeline, Civilization VII divides gameplay into three distinct historical ages: Antiquity (3000 BCE - 500 CE), Exploration (500 CE - 1700 CE), and Modern (1700 CE - Present). Each age has unique gameplay mechanics that affect everything from unit availability to victory conditions.

This document focuses specifically on the gameplay mechanics that are tied to the Ages system, how they function in each age, and how they impact modding.

## Age Transition Mechanics

### Legacy Paths and Traditions

One of the most important mechanics in Civilization VII is how progress carries forward between ages. As players advance through an age, they progress along four Legacy Paths:

1. **Science Path**: Scientific and technological achievements
2. **Military Path**: Combat victories and military development
3. **Culture Path**: Cultural and civic advancements
4. **Economy Path**: Resource production and financial growth

Completing milestones on these paths provides Legacy Points that contribute to Age Progress. Completing an entire Legacy Path unlocks a Golden Age Legacy, while failing to complete any milestones results in a Dark Age Legacy for the next Age.

### Traditions System

Connected to the Ages system is the **Traditions** mechanic. Each civilization has unique Civics that unlock Traditions - special policy-like bonuses that persist across age transitions. When you complete a civilization's unique civic, you earn a Tradition that can be slotted into your government policies in all future ages.

For example:
- **Han China's** "Tianxia" Tradition provides increased Science from Specialists in all future eras
- **Maya's** "Miracles of the Twins" Tradition gives units a Poison attack bonus against wounded enemies

Traditions are one of the primary ways the game maintains continuity between different ages, allowing players to carry forward advantages from previous civilizations.

### Elements That Persist Through Age Transitions

During Age transitions, these elements persist to maintain continuity:

1. **Leaders**: The same leader continues to lead your civilization, though the civilization itself changes
2. **Wonders and Unique Quarters**: These remain as "Ageless" structures on the map
3. **Commander Units**: These maintain their attributes and experience
4. **Traditions**: Unlocked Traditions remain available for use in your government
5. **Strategic Resources**: Control over strategic resources continues
6. **Diplomatic Relationships**: Relations with other civilizations carry forward, though their identity changes

## Age-Specific Gameplay Systems

### Antiquity Age (3000 BCE - 500 CE)

The Antiquity Age focuses on early development and expansion, with mechanics emphasizing:

1. **City Foundation**: Simplified early settlement mechanics
2. **Basic Resources**: Primary focus on basic resources (food, production) 
3. **Tribal Governance**: Early government forms with limited policy options
4. **Religion**: Basic pantheon systems (plays a minor role compared to later Ages)
5. **Military**: Emphasis on melee and early ranged combat

Unique to Antiquity:
- **Early Exploration**: Special bonuses for discovering natural wonders and meeting other civilizations
- **Tribal Villages**: Ancient tribal settlements that provide one-time bonuses
- **Barbarian Camps**: Threat-based barbarian spawning system

### Exploration Age (500 CE - 1700 CE)

The Exploration Age shifts focus to global interaction and cultural exchange:

1. **Naval Exploration**: Enhanced naval systems and exploration mechanics
2. **Religion**: Major gameplay driver in this Age with enhanced religious mechanics
3. **Trade Networks**: Expanded trade routes and international commerce
4. **Distant Lands**: Expanded map areas accessible through naval exploration
5. **Colonization**: Systems for establishing colonies in newly discovered territories

Unique to Exploration:
- **Religious Spread**: Enhanced religious mechanics and missionary units
- **Trade Routes**: Expanded international trade mechanics
- **Colonial Settlements**: Special settlement types for distant colonies
- **Resource Exploitation**: Improved extraction from colonial territories

### Modern Age (1700 CE - Present)

The Modern Age represents industrial and technological advancement:

1. **Industry**: Factory production and resource exploitation systems
2. **Advanced Units**: Aircraft, tanks, and modern military units
3. **Projects**: Space race and other advanced scientific endeavors
4. **Diplomacy**: Enhanced diplomatic options and alliances 
5. **Global Systems**: World Congress, Climate Change, and other global mechanics

Unique to Modern:
- **Victory Conditions**: Final victory condition evaluations and requirements
- **Global Crises**: Climate change and other global challenges
- **Advanced Diplomacy**: Complex diplomatic relationships and alliances
- **Advanced Economic Systems**: Stock markets, corporations, and modern economic structures

## Age Balancing Mechanics

### Age-Specific Unit Balance

Units are balanced differently in each age:

1. **Antiquity Units**: Lower combat values, simpler abilities, fewer resource requirements
2. **Exploration Units**: Moderate combat values, special abilities tied to exploration and religion
3. **Modern Units**: Higher combat values, complex abilities, and resource dependencies

The game prevents "tech rushing" by making certain units only available in specific ages, regardless of technology progress.

### Age-Specific Buildings and Districts

Buildings and districts follow similar age-specific progressions:

1. **Antiquity Buildings**: Focus on basic yields (Food, Production, Culture)
2. **Exploration Buildings**: Enhanced yields and special effects related to trade and religion
3. **Modern Buildings**: Complex yield combinations and special abilities

### Age Transition Rebalancing

When transitioning between ages, several mechanical adjustments occur:

1. **Yield Scaling**: Yields from previous age buildings are rebalanced
2. **Unit Obsolescence**: Some units become obsolete while maintaining utility in specific roles
3. **Legacy Bonuses**: Special bonuses are applied based on Legacy Path progress
4. **Crisis Events**: Potential negative events if empire expansion was too rapid

## Age-Specific Victory Mechanics

Each age contributes differently to victory paths:

1. **Antiquity**: Establishes foundation for all victory types but doesn't allow for completion
2. **Exploration**: Advances victory progress substantially, with some victory types becoming viable
3. **Modern**: Final push toward victory, with all victory conditions being achievable

### Victory Progress Across Ages

Victory progress carries forward between ages, with adjustments:

1. **Science Victory**: Research progress carries forward but with new technologies in each age
2. **Domination**: Territorial control translates to starting position in the next age
3. **Culture**: Cultural development evolves into new forms of cultural expression
4. **Diplomatic**: Relationships evolve but maintain their general positive/negative status

## Implementing Age-Specific Mechanics in Mods

When modding age-specific content, consider these implementation points:

1. **Module Dependencies**: Content should properly reference the appropriate age module:
   - `<GAME_RESOURCES>/Base/age-antiquity/`
   - `<GAME_RESOURCES>/Base/age-exploration/`
   - `<GAME_RESOURCES>/Base/age-modern/`

2. **Age-Specific Yields**: When defining yields, consider how they scale between ages
   - Antiquity yields are typically lower
   - Exploration yields moderate and more specialized
   - Modern yields high but requiring more infrastructure

3. **Age Transition Events**: Mods can hook into age transition events to trigger special effects
   - Use `AgeTransitionEvent` trigger to execute actions
   - `TransitionToAge_[NAME]` requirements can check specific age transitions

4. **Ageless Content**: Certain content can be marked as "Ageless" to persist through all ages
   - Add the `Ageless` flag to buildings or improvements that should persist
   - Example: The Han Great Wall is marked as "Ageless" and remains on the map

5. **Age-Specific Balancing**: Take care to balance content for the appropriate age
   - Combat strength values should align with the age's baseline
   - Building yields should match age-specific standards
   - Costs should reflect the economy of that particular age

## Technical Reference for Modders

For modders implementing age-specific content, here are key reference points:

1. **Age Definition Files**:
   - Check `<GAME_RESOURCES>/Base/modules/age-[name]/config/config.xml` for age configuration
   - Review `age-[name].modinfo` for dependencies and structure

2. **Age Transition Events**:
   - Utilize `OnAgeTransition` trigger for mods that respond to age changes
   - The `SOURCE_AGE` and `TARGET_AGE` parameters allow specific targeting

3. **Legacy Path Implementation**:
   - Legacy Paths are defined in `data/legacies/` folders in each age module
   - Each path has milestones that award Legacy Points

4. **Traditions Implementation**:
   - Traditions are essentially policies with the `IsTradition` flag set to true
   - They remain available through age transitions via the `PersistAcrossAges` flag

Remember that age-specific content should be balanced within its specific historical context while supporting a cohesive gameplay experience across all three ages. 