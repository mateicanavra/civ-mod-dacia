# TypeScript Modding Tools: Creating Civilizations

This guide covers how to create custom civilizations using the Civilization VII TypeScript Modding Tools.

## Basic Civilization Creation

Creating a complete civilization requires defining multiple components and binding them together:

```typescript
import {
    ACTION_GROUP_BUNDLE,
    CivilizationBuilder,
    ImportFileBuilder,
    Mod,
    ModifierBuilder,
    TAG_TRAIT,
    TRAIT,
    COLLECTION,
    EFFECT,
    REQUIREMENT,
    UNIT_CLASS
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-civ-mod',
    version: '1.0',
});

// Step 1: Import civilization icon
const civIcon = new ImportFileBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    content: './assets/civ-icon.png',  // Path to your icon file
    name: 'civ_sym_dacia'              // Name for the icon in the mod
});

// Step 2: Create the civilization definition
const dacia = new CivilizationBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    
    // Core civilization properties
    civilization: {
        domain: 'AntiquityAgeCivilizations',
        civilizationType: 'CIVILIZATION_DACIA'
    },
    
    // Traits that affect gameplay and AI behavior
    civilizationTraits: [
        TRAIT.ANTIQUITY_CIV,
        TRAIT.ATTRIBUTE_MILITARISTIC,
        TRAIT.ATTRIBUTE_CULTURAL
    ],
    
    // Tags for categorization
    civilizationTags: [
        TAG_TRAIT.CULTURAL,
        TAG_TRAIT.MILITARY
    ],
    
    // Reference to the icon
    icon: {
        path: `fs://game/${mod.id}/${civIcon.name}`
    },
    
    // Text that appears in-game
    localizations: [
        { 
            name: 'Dacia',
            description: 'Ancient kingdom in Eastern Europe known for fierce warriors and rich gold mines.',
            fullName: 'Kingdom of Dacia',
            adjective: 'Dacian',
            cityNames: ['Sarmizegetusa', 'Argidava', 'Buridava', 'Sucidava', 'Petrodava']
        }
    ],
});

// Step 3: Create a unique ability for the civilization
const dacianUprisings = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.PLAYER_UNITS,
        effect: EFFECT.UNIT_ADJUST_COMBAT_STRENGTH,
        permanent: true,
        requirements: [{
            type: REQUIREMENT.UNIT_TAG_MATCHES,
            arguments: [{ name: 'Tag', value: UNIT_CLASS.MELEE }]
        }],
        arguments: [{ name: 'Amount', value: 5 }]
    },
    localizations: [{
        description: 'Mountain Warriors: +5 Combat Strength for Melee units.'
    }]
});

// Step 4: Bind the ability to the civilization
dacia.bind([dacianUprisings]);

// Step 5: Add the civilization and icon to the mod
mod.add([dacia, civIcon]);

// Build the mod
mod.build('./dist');
```

## Key Components

When creating a civilization, these are the essential components:

1. **Civilization Type**: A unique identifier for your civilization
2. **Domain**: The age category your civilization belongs to
3. **Traits**: Characteristics that affect AI behavior and gameplay
4. **Tags**: Used for categorization and targeting by game mechanics
5. **Icon**: Visual symbol representing your civilization
6. **Localization**: Names, descriptions, and city names
7. **Unique Abilities**: Special bonuses and gameplay mechanics

## Creating Unique Abilities

Unique abilities are what make civilizations distinct in gameplay:

```typescript
// Combat bonus for units in specific terrain
const mountainWarfare = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.PLAYER_UNITS,
        effect: EFFECT.UNIT_ADJUST_COMBAT_STRENGTH,
        permanent: true,
        requirements: [
            {
                type: REQUIREMENT.COMBAT_IS_IN_HILLS
            }
        ],
        arguments: [{ name: 'Amount', value: 3 }]
    },
    localizations: [{
        description: 'Mountain Tactics: +3 Combat Strength when fighting in Hills.'
    }]
});

// Production bonus for specific buildings
const goldsmithCraft = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.PLAYER_CITIES,
        effect: EFFECT.CITY_ADJUST_YIELD_FROM_TAG,
        permanent: true,
        arguments: [
            { name: 'Tag', value: 'PRODUCTION' },
            { name: 'YieldType', value: 'YIELD_GOLD' },
            { name: 'Amount', value: 25 },
            { name: 'Percent', value: true }
        ]
    },
    localizations: [{
        description: 'Dacian Goldsmithing: +25% Gold from production buildings.'
    }]
});

// Bind multiple abilities to the civilization
dacia.bind([mountainWarfare, goldsmithCraft]);
```

## Adding Custom Units and Buildings

You can create unique units and buildings for your civilization:

```typescript
import {
    UnitBuilder,
    ConstructibleBuilder,
    UNIT_CLASS,
    CONSTRUCTIBLE_TYPE_TAG
} from "civ7-modding-tools";

// Create a unique unit for Dacia
const falxWarrior = new UnitBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    typeTags: [UNIT_CLASS.MELEE, UNIT_CLASS.HEAVY],
    unit: {
        unitType: 'UNIT_DACIA_FALX_WARRIOR'
    },
    unitCost: { cost: 90 },
    unitStat: { combat: 35 },
    localizations: [
        { 
            name: 'Falx Warrior', 
            description: 'Dacian warrior wielding the curved falx blade.'
        }
    ]
});

// Create a unique building for Dacia
const goldMine = new ConstructibleBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    constructible: {
        constructibleType: 'BUILDING_DACIA_GOLD_MINE'
    },
    building: {},
    typeTags: [
        CONSTRUCTIBLE_TYPE_TAG.UNIQUE,
        CONSTRUCTIBLE_TYPE_TAG.ECONOMY
    ],
    constructibleYieldChanges: [
        { yieldType: 'YIELD_GOLD', yieldChange: 5 }
    ],
    localizations: [
        { 
            name: 'Dacian Gold Mine', 
            description: 'Rich gold mines that made Dacia famous throughout antiquity.'
        }
    ]
});

// Bind these unique elements to the civilization
dacia.bind([falxWarrior, goldMine]);
```

## Age-Specific Civilizations

You can define different versions of your civilization for different Ages:

```typescript
// Antiquity Age version
const ancientDacia = new CivilizationBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    civilization: {
        domain: 'AntiquityAgeCivilizations',
        civilizationType: 'CIVILIZATION_DACIA_ANCIENT'
    },
    // Other properties...
});

// Exploration Age version
const modernDacia = new CivilizationBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_EXPLORATION,
    civilization: {
        domain: 'ExplorationAgeCivilizations',
        civilizationType: 'CIVILIZATION_DACIA_MODERN'
    },
    // Modified properties for this age...
});

// Define the transition between ages
const daciaTransition = new CivilizationUnlockBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_EXPLORATION,
    from: { 
        civilizationType: 'CIVILIZATION_DACIA_ANCIENT',
        ageType: AGE.ANTIQUITY 
    },
    to: { 
        civilizationType: 'CIVILIZATION_DACIA_MODERN',
        ageType: AGE.EXPLORATION 
    }
});

// Add all components to the mod
mod.add([ancientDacia, modernDacia, daciaTransition]);
```

## Related Resources

For more information about TypeScript modding in Civilization VII, check:
- [TypeScript Modding Tools Overview](/guides/typescript/typescript-overview.md)
- [Environment Setup](/guides/typescript/howto/environment-setup.md)
- [Creating Units](/guides/typescript/howto/creating-units.md)
- [Creating Buildings](/guides/typescript/howto/creating-buildings.md)
- [Leaders and Ages](/guides/typescript/howto/leaders-and-ages.md) 