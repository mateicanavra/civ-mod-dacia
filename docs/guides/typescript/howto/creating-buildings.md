# TypeScript Modding Tools: Creating Buildings and Improvements

This guide covers how to create custom buildings and improvements using the Civilization VII TypeScript Modding Tools.

## Creating a Building

Buildings are constructed within districts and provide various bonuses to your cities:

```typescript
import { 
    ACTION_GROUP_BUNDLE, 
    CONSTRUCTIBLE_TYPE_TAG, 
    ConstructibleBuilder, 
    DISTRICT, 
    Mod, 
    YIELD 
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-building-mod',
    version: '1.0',
});

// Create a building
const trainingGrounds = new ConstructibleBuilder({
    // Specify which Age this building belongs to
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    
    // Core building properties
    constructible: {
        constructibleType: 'BUILDING_TRAINING_GROUNDS',
    },
    
    // Indicate this is a building (vs improvement)
    building: {},
    
    // Tags affect how the building functions
    typeTags: [
        CONSTRUCTIBLE_TYPE_TAG.AGELESS,
        CONSTRUCTIBLE_TYPE_TAG.PRODUCTION,
        CONSTRUCTIBLE_TYPE_TAG.MILITARY
    ],
    
    // Where the building can be placed
    constructibleValidDistricts: [
        DISTRICT.URBAN,
        DISTRICT.CITY_CENTER,
    ],
    
    // Yields provided by the building
    constructibleYieldChanges: [
        { yieldType: YIELD.PRODUCTION, yieldChange: 2 },
    ],
    
    // Maintenance costs
    constructibleMaintenances: [
        { yieldType: YIELD.GOLD, amount: 2 },
    ],
    
    // Text that appears in-game
    localizations: [
        { 
            name: 'Training Grounds', 
            description: 'Military training facility providing production bonuses.',
            tooltip: 'Provides +2 Production but costs 2 Gold per turn to maintain.'
        },
    ]
});

// Add building to mod and build
mod.add([trainingGrounds]).build('./dist');
```

## Creating an Improvement

Improvements are constructed on tiles outside of cities and provide bonuses to those tiles:

```typescript
import { 
    ACTION_GROUP_BUNDLE, 
    CONSTRUCTIBLE_TYPE_TAG, 
    ConstructibleBuilder, 
    TERRAIN, 
    Mod, 
    YIELD 
} from "civ7-modding-tools";

// Create an improvement
const sacredGrove = new ConstructibleBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    
    constructible: {
        constructibleType: 'IMPROVEMENT_SACRED_GROVE',
    },
    
    // Define as an improvement instead of a building
    improvement: {
        resourceTier: 0,
        cityBuildable: true
    },
    
    typeTags: [
        CONSTRUCTIBLE_TYPE_TAG.AGELESS,
        CONSTRUCTIBLE_TYPE_TAG.CULTURE,
        CONSTRUCTIBLE_TYPE_TAG.HAPPINESS
    ],
    
    // Terrain types where this improvement can be built
    constructibleValidTerrains: [
        TERRAIN.GRASS,
        TERRAIN.PLAINS
    ],
    
    constructibleYieldChanges: [
        { yieldType: YIELD.CULTURE, yieldChange: 1 },
        { yieldType: YIELD.HAPPINESS, yieldChange: 1 },
    ],
    
    localizations: [
        { 
            name: 'Sacred Grove', 
            description: 'A sacred natural area providing cultural and happiness benefits.',
            tooltip: 'Provides +1 Culture and +1 Happiness.'
        },
    ]
});

mod.add([sacredGrove]);
```

## Key Components

When creating buildings and improvements, these are the important components:

### For Buildings:

1. **Type Tags**: Define the building's category and effects
2. **Valid Districts**: Where the building can be constructed
3. **Yield Changes**: Bonuses provided by the building
4. **Maintenance**: Upkeep costs
5. **Localization**: Name, description, and tooltip

### For Improvements:

1. **Type Tags**: Define the improvement's category
2. **Valid Terrains**: Where the improvement can be built
3. **Yield Changes**: Bonuses provided to the tile
4. **Improvement Properties**: Resource tier, buildability, etc.
5. **Localization**: Name, description, and tooltip

## Advanced Configuration

### Building Requirements

You can set prerequisites for buildings:

```typescript
const advancedBuilding = new ConstructibleBuilder({
    // Basic building properties...
    
    // Prerequisites (requires another building)
    constructiblePrereqs: [
        { constructibleType: 'BUILDING_WORKSHOP' }
    ],
    
    // Other properties...
});
```

### Improvement Resource Requirements

Create improvements that require specific resources:

```typescript
const resourceImprovement = new ConstructibleBuilder({
    // Basic improvement properties...
    
    // Can only be built on tiles with this resource
    constructibleValidResources: [
        RESOURCE.IRON
    ],
    
    // Other properties...
});
```

## Related Resources

For more information about TypeScript modding in Civilization VII, check:
- [TypeScript Modding Tools Overview](/guides/typescript/typescript-overview.md)
- [Environment Setup](/guides/typescript/howto/environment-setup.md)
- [Creating Units](/guides/typescript/howto/creating-units.md)
- [Creating Civilizations](/guides/typescript/howto/creating-civilizations.md) 