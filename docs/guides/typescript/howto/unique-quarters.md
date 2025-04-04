# TypeScript Modding Tools: Creating Unique Quarters

This guide covers how to create unique quarters using the Civilization VII TypeScript Modding Tools.

## Understanding Unique Quarters

Unique Quarters are special districts formed by combining two buildings. They provide powerful bonuses and can be a key feature of your civilization's unique gameplay.

## Basic Unique Quarter Creation

Creating a unique quarter involves several steps:
1. Create the component buildings
2. Define the unique quarter that combines them
3. Create gameplay modifiers for the quarter
4. Bind everything together

Here's a complete example:

```typescript
import {
    ACTION_GROUP_BUNDLE,
    CONSTRUCTIBLE_TYPE_TAG,
    ConstructibleBuilder,
    DISTRICT,
    Mod,
    ModifierBuilder,
    UniqueQuarterBuilder,
    COLLECTION,
    EFFECT,
    REQUIREMENT,
    YIELD
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-quarter-mod',
    version: '1.0',
});

// Step 1: Create first building
const warriorHall = new ConstructibleBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    constructible: {
        constructibleType: 'BUILDING_WARRIOR_HALL',
    },
    building: {},
    typeTags: [
        CONSTRUCTIBLE_TYPE_TAG.AGELESS,
        CONSTRUCTIBLE_TYPE_TAG.UNIQUE,
        CONSTRUCTIBLE_TYPE_TAG.MILITARY
    ],
    constructibleValidDistricts: [
        DISTRICT.URBAN,
        DISTRICT.CITY_CENTER,
    ],
    constructibleYieldChanges: [
        { yieldType: YIELD.PRODUCTION, yieldChange: 2 },
    ],
    localizations: [
        { 
            name: 'Warrior Hall', 
            description: 'Training hall for Dacian warriors.',
            tooltip: '+2 Production.'
        },
    ]
});

// Step 2: Create second building
const wolfShrine = new ConstructibleBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    constructible: {
        constructibleType: 'BUILDING_WOLF_SHRINE',
    },
    building: {},
    typeTags: [
        CONSTRUCTIBLE_TYPE_TAG.AGELESS,
        CONSTRUCTIBLE_TYPE_TAG.UNIQUE,
        CONSTRUCTIBLE_TYPE_TAG.CULTURE
    ],
    constructibleValidDistricts: [
        DISTRICT.URBAN,
        DISTRICT.CITY_CENTER,
    ],
    constructibleYieldChanges: [
        { yieldType: YIELD.CULTURE, yieldChange: 2 },
    ],
    localizations: [
        { 
            name: 'Wolf Shrine', 
            description: 'Sacred shrine to the wolf deity.',
            tooltip: '+2 Culture.'
        },
    ]
});

// Step 3: Create Unique Quarter
const dragonMountain = new UniqueQuarterBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    uniqueQuarter: {
        uniqueQuarterType: 'QUARTER_DRAGON_MOUNTAIN',
        buildingType1: warriorHall.constructible.constructibleType,
        buildingType2: wolfShrine.constructible.constructibleType,
    },
    localizations: [
        { 
            name: 'Dragon Mountain', 
            description: 'Sacred military training complex of the Dacians.'
        },
    ]
});

// Step 4: Create a modifier for the Unique Quarter
const quarterAbility = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.ALL_CITIES,
        effect: EFFECT.CITY_ADJUST_YIELD,
        permanent: true,
        requirements: [{
            type: REQUIREMENT.CITY_HAS_UNIQUE_QUARTER,
            arguments: [{ name: 'UniqueQuarterType', value: 'QUARTER_DRAGON_MOUNTAIN' }]
        }],
        arguments: [
            { name: "YieldType", value: YIELD.PRODUCTION },
            { name: "Amount", value: 2 },
            { name: "Tooltip", value: 'LOC_QUARTER_DRAGON_MOUNTAIN_NAME' }
        ]
    }
});

// Step 5: Bind the modifier to the Unique Quarter
dragonMountain.bind([quarterAbility]);

// Step 6: Add everything to the mod
mod.add([warriorHall, wolfShrine, dragonMountain]);

// Build the mod
mod.build('./dist');
```

## Key Components of a Unique Quarter

When creating a unique quarter, these are the essential components to consider:

1. **Component Buildings**: At least two buildings that will form the unique quarter
2. **Quarter Definition**: The unique quarter definition that combines the buildings
3. **Modifiers**: Gameplay effects that trigger when the quarter is built
4. **Placement Rules**: Districts where component buildings can be placed
5. **Yields**: Resource outputs from both component buildings and the quarter itself
6. **Localization**: Names and descriptions for buildings and the quarter

## Creating Civilization-Specific Unique Quarters

Unique quarters often serve as a unique feature for a specific civilization. Here's how to integrate a unique quarter with a civilization:

```typescript
import {
    CivilizationBuilder,
    UniqueQuarterBuilder,
    ConstructibleBuilder,
    ModifierBuilder,
    /* other imports */
} from "civ7-modding-tools";

// Create your civilization
const dacia = new CivilizationBuilder({
    /* civilization configuration */
});

// Create component buildings and unique quarter
const building1 = new ConstructibleBuilder({ /* configuration */ });
const building2 = new ConstructibleBuilder({ /* configuration */ });

const dacianQuarter = new UniqueQuarterBuilder({
    /* quarter configuration */
});

// Create a special modifier that only applies to this civilization
const civSpecificBonus = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.PLAYER_CITIES,
        effect: EFFECT.CITY_ADJUST_YIELD,
        permanent: true,
        requirements: [
            {
                type: REQUIREMENT.CITY_HAS_UNIQUE_QUARTER,
                arguments: [{ name: 'UniqueQuarterType', value: dacianQuarter.uniqueQuarter.uniqueQuarterType }]
            },
            {
                type: REQUIREMENT.PLAYER_IS_CIVILIZATION_TYPE,
                arguments: [{ name: 'CivilizationType', value: dacia.civilization.civilizationType }]
            }
        ],
        arguments: [
            { name: "YieldType", value: YIELD.GOLD },
            { name: "Amount", value: 5 },
        ]
    },
    localizations: [{
        description: '+5 Gold from Dragon Mountain (Dacia only)'
    }]
});

// Bind everything together
dacianQuarter.bind([civSpecificBonus]);
dacia.bind([building1, building2, dacianQuarter]);

// Add to mod
mod.add([dacia, building1, building2, dacianQuarter]);
```

## Advanced Unique Quarter Techniques

### Age-Specific Unique Quarters

Create unique quarters that are only available in specific Ages:

```typescript
import { AGE, /* other imports */ } from "civ7-modding-tools";

// Create an Antiquity Age unique quarter
const ancientQuarter = new UniqueQuarterBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    uniqueQuarter: {
        uniqueQuarterType: 'QUARTER_ANCIENT_COMPLEX',
        buildingType1: 'BUILDING_ANCIENT_TEMPLE',
        buildingType2: 'BUILDING_ANCIENT_MARKET',
    },
    // Other configuration...
});

// Create an Exploration Age unique quarter
const explorationQuarter = new UniqueQuarterBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_EXPLORATION,
    uniqueQuarter: {
        uniqueQuarterType: 'QUARTER_EXPLORATION_COMPLEX',
        buildingType1: 'BUILDING_EXPLORATION_TEMPLE',
        buildingType2: 'BUILDING_EXPLORATION_MARKET',
    },
    // Other configuration...
});

// Add a modifier that only applies in a specific Age
const ageSpecificModifier = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.PLAYER_CITIES,
        effect: EFFECT.CITY_ADJUST_YIELD,
        permanent: true,
        requirements: [
            {
                type: REQUIREMENT.CITY_HAS_UNIQUE_QUARTER,
                arguments: [{ name: 'UniqueQuarterType', value: 'QUARTER_ANCIENT_COMPLEX' }]
            },
            {
                type: REQUIREMENT.PLAYER_IS_IN_AGE,
                arguments: [{ name: 'AgeType', value: AGE.ANTIQUITY }]
            }
        ],
        arguments: [
            { name: "YieldType", value: YIELD.FAITH },
            { name: "Amount", value: 3 },
        ]
    }
});
```

### Unique Quarter with Conditional Bonuses

Create unique quarters with bonuses that depend on game conditions:

```typescript
// Create a unique quarter with bonuses based on terrain
const hillsBonus = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.PLAYER_CITIES,
        effect: EFFECT.CITY_ADJUST_YIELD,
        permanent: true,
        requirements: [
            {
                type: REQUIREMENT.CITY_HAS_UNIQUE_QUARTER,
                arguments: [{ name: 'UniqueQuarterType', value: 'QUARTER_MOUNTAIN_COMPLEX' }]
            },
            {
                type: REQUIREMENT.PLOT_IS_TERRAIN_TYPE,
                arguments: [{ name: 'TerrainType', value: 'TERRAIN_HILLS' }]
            }
        ],
        arguments: [
            { name: "YieldType", value: YIELD.PRODUCTION },
            { name: "Amount", value: 3 },
        ]
    },
    localizations: [{
        description: '+3 Production from Mountain Complex when built on Hills'
    }]
});

// Create a unique quarter with war-time bonuses
const warBonus = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.PLAYER_CITIES,
        effect: EFFECT.CITY_ADJUST_YIELD,
        permanent: true,
        requirements: [
            {
                type: REQUIREMENT.CITY_HAS_UNIQUE_QUARTER,
                arguments: [{ name: 'UniqueQuarterType', value: 'QUARTER_WAR_COMPLEX' }]
            },
            {
                type: REQUIREMENT.PLAYER_IS_AT_WAR,
            }
        ],
        arguments: [
            { name: "YieldType", value: YIELD.PRODUCTION },
            { name: "Amount", value: 5 },
        ]
    },
    localizations: [{
        description: '+5 Production from War Complex during wartime'
    }]
});
```

## Best Practices

When creating unique quarters, keep these best practices in mind:

1. **Balance**: Ensure the combined benefits aren't overpowered
2. **Thematic Consistency**: Make the buildings and quarter fit together thematically
3. **Clear Purpose**: Design the quarter with a clear gameplay purpose
4. **Civilization Fit**: For civilization-specific quarters, ensure they align with the civilization's focus
5. **Descriptive Localization**: Provide clear descriptions of what the quarter does
6. **Visual Distinction**: If possible, reference unique art assets for the quarter

## Conclusion

Unique Quarters are a powerful way to add distinctive gameplay elements to your mod. By combining buildings and adding special modifiers, you can create compelling gameplay opportunities that enhance your civilizations. 