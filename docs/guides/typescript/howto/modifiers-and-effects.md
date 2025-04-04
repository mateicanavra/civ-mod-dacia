# TypeScript Modding Tools: Creating Modifiers and Effects

This guide covers how to create modifiers and effects using the Civilization VII TypeScript Modding Tools.

## Understanding Modifiers and Effects

Modifiers are at the heart of gameplay effects in Civilization VII. They define how game elements interact with each other and enable you to create unique gameplay mechanics for your mod. A modifier consists of several key components:

1. **Collection**: What entities the modifier affects (units, cities, players, etc.)
2. **Effect**: What the modifier actually does (adjust combat strength, change yields, etc.)
3. **Requirements**: Optional conditions that must be met for the modifier to apply
4. **Arguments**: Values that configure how the effect works
5. **Localization**: Text descriptions that explain the modifier to players

## Basic Modifier Creation

Here's how to create a simple modifier that provides a flat bonus:

```typescript
import {
    ACTION_GROUP_BUNDLE,
    COLLECTION,
    EFFECT,
    Mod,
    ModifierBuilder
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-modifier-mod',
    version: '1.0',
});

// Simple modifier: +2 Combat Strength for all units
const simpleModifier = new ModifierBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    modifier: {
        collection: COLLECTION.PLAYER_UNITS,
        effect: EFFECT.UNIT_ADJUST_COMBAT_STRENGTH,
        permanent: true,
        arguments: [{ name: 'Amount', value: 2 }]
    },
    localizations: [{
        description: '+2 Combat Strength for all units'
    }]
});

// Add to mod
mod.add([simpleModifier]);

// Build the mod
mod.build('./dist');
```

## Creating Complex Modifiers with Requirements

For more targeted effects, you can add requirements to your modifiers:

```typescript
import {
    ACTION_GROUP_BUNDLE,
    COLLECTION,
    EFFECT,
    Mod,
    ModifierBuilder,
    REQUIREMENT,
    UNIT_CLASS
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-complex-modifier-mod',
    version: '1.0',
});

// Complex modifier: +5 Combat Strength for Melee units when defending in hills
const complexModifier = new ModifierBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    modifier: {
        collection: COLLECTION.PLAYER_UNITS,
        effect: EFFECT.UNIT_ADJUST_COMBAT_STRENGTH,
        permanent: true,
        requirements: [
            {
                type: REQUIREMENT.UNIT_TAG_MATCHES,
                arguments: [{ name: 'Tag', value: UNIT_CLASS.MELEE }]
            },
            {
                type: REQUIREMENT.UNIT_IS_DEFENDING
            },
            {
                type: REQUIREMENT.COMBAT_IS_IN_HILLS
            }
        ],
        arguments: [{ name: 'Amount', value: 5 }]
    },
    localizations: [{
        description: '+5 Combat Strength for Melee units when defending in hills'
    }]
});

// Add to mod
mod.add([complexModifier]);
```

## Common Collections

Collections define what game entities are affected by a modifier. Here are some commonly used collections:

```typescript
import { COLLECTION } from "civ7-modding-tools";

// Available collections
const collections = {
    // Units
    PLAYER_UNITS: COLLECTION.PLAYER_UNITS,        // All units belonging to the player
    
    // Cities
    PLAYER_CITIES: COLLECTION.PLAYER_CITIES,      // All cities belonging to the player
    ALL_CITIES: COLLECTION.ALL_CITIES,            // All cities in the game
    CITY_SELF: COLLECTION.CITY_SELF,              // The city itself (for city-specific effects)
    
    // Players
    OWNER: COLLECTION.OWNER,                      // The player who owns the entity
    ALL_PLAYERS: COLLECTION.ALL_PLAYERS,          // All players in the game
    
    // Tiles
    ADJACENT_PLOTS: COLLECTION.ADJACENT_PLOTS,    // Plots adjacent to a specific location
    PLAYER_PLOTS: COLLECTION.PLAYER_PLOTS         // All plots controlled by the player
};
```

## Common Effects

Effects define what the modifier actually does. Here are some commonly used effects:

```typescript
import { EFFECT } from "civ7-modding-tools";

// Unit effects
const unitEffects = {
    ADJUST_COMBAT_STRENGTH: EFFECT.UNIT_ADJUST_COMBAT_STRENGTH,   // Change unit combat strength
    ADJUST_MOVEMENT: EFFECT.UNIT_ADJUST_MOVEMENT,                // Change unit movement points
    ADJUST_SIGHT: EFFECT.UNIT_ADJUST_SIGHT,                      // Change unit sight range
    ADJUST_MAINTENANCE: EFFECT.UNIT_ADJUST_MAINTENANCE           // Change unit maintenance cost
};

// City effects
const cityEffects = {
    ADJUST_YIELD: EFFECT.CITY_ADJUST_YIELD,                      // Change city yield (production, food, etc.)
    ADJUST_GROWTH: EFFECT.CITY_ADJUST_GROWTH,                    // Change city growth rate
    ADJUST_BUILDING_PRODUCTION: EFFECT.CITY_ADJUST_BUILDING_PRODUCTION // Change production for buildings
};

// Player effects
const playerEffects = {
    ADJUST_CONSTRUCTIBLE_YIELD: EFFECT.PLAYER_ADJUST_CONSTRUCTIBLE_YIELD,  // Change yields from constructibles
    ENABLE_UNIT: EFFECT.PLAYER_ENABLE_UNIT,                      // Allow the player to build a unit
    ENABLE_BUILDING: EFFECT.PLAYER_ENABLE_BUILDING               // Allow the player to build a building
};
```

## Common Requirements

Requirements define conditions that must be met for the modifier to apply:

```typescript
import { REQUIREMENT } from "civ7-modding-tools";

// Unit requirements
const unitRequirements = {
    UNIT_TAG_MATCHES: REQUIREMENT.UNIT_TAG_MATCHES,              // Unit has a specific tag
    UNIT_IS_DEFENDING: REQUIREMENT.UNIT_IS_DEFENDING,            // Unit is in a defensive position
    UNIT_IS_ATTACKING: REQUIREMENT.UNIT_IS_ATTACKING             // Unit is attacking
};

// Terrain requirements
const terrainRequirements = {
    COMBAT_IS_IN_HILLS: REQUIREMENT.COMBAT_IS_IN_HILLS,          // Combat occurs in hills
    PLOT_IS_TERRAIN_TYPE: REQUIREMENT.PLOT_IS_TERRAIN_TYPE,      // Plot is a specific terrain type
    PLOT_HAS_FEATURE: REQUIREMENT.PLOT_HAS_FEATURE               // Plot has a feature (forest, etc.)
};

// Player requirements
const playerRequirements = {
    PLAYER_IS_CIVILIZATION_TYPE: REQUIREMENT.PLAYER_IS_CIVILIZATION_TYPE,  // Player is a specific civilization
    PLAYER_IS_AT_WAR: REQUIREMENT.PLAYER_IS_AT_WAR,                        // Player is at war
    PLAYER_IS_IN_AGE: REQUIREMENT.PLAYER_IS_IN_AGE                         // Player is in a specific age
};

// City requirements
const cityRequirements = {
    CITY_HAS_BUILDING: REQUIREMENT.CITY_HAS_BUILDING,            // City has a specific building
    CITY_HAS_UNIQUE_QUARTER: REQUIREMENT.CITY_HAS_UNIQUE_QUARTER // City has a specific unique quarter
};
```

## Creating Modifiers for Different Game Elements

### Unit Modifiers

```typescript
// Modifier for ranged units to get +1 range
const rangeModifier = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.PLAYER_UNITS,
        effect: EFFECT.UNIT_ADJUST_RANGE,
        requirements: [{
            type: REQUIREMENT.UNIT_TAG_MATCHES,
            arguments: [{ name: 'Tag', value: 'RANGED' }]
        }],
        arguments: [{ name: 'Amount', value: 1 }]
    },
    localizations: [{
        description: '+1 Range for Ranged units'
    }]
});
```

### City Modifiers

```typescript
// Modifier for cities near mountains to get +1 Science
const mountainScienceModifier = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.PLAYER_CITIES,
        effect: EFFECT.CITY_ADJUST_YIELD,
        requirements: [{
            type: REQUIREMENT.PLOT_ADJACENT_TO_TERRAIN_TYPE,
            arguments: [{ name: 'TerrainType', value: 'TERRAIN_MOUNTAIN' }]
        }],
        arguments: [
            { name: 'YieldType', value: 'YIELD_SCIENCE' },
            { name: 'Amount', value: 1 }
        ]
    },
    localizations: [{
        description: '+1 Science for cities adjacent to Mountains'
    }]
});
```

### Civilization Modifiers

```typescript
// Modifier that gives a civilization a unique bonus
const civModifier = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.OWNER,
        effect: EFFECT.PLAYER_ADJUST_CONSTRUCTIBLE_YIELD,
        requirements: [{
            type: REQUIREMENT.PLAYER_IS_CIVILIZATION_TYPE,
            arguments: [{ name: 'CivilizationType', value: 'CIVILIZATION_DACIA' }]
        }],
        arguments: [
            { name: 'Tag', value: 'PRODUCTION' },
            { name: 'YieldType', value: 'YIELD_PRODUCTION' },
            { name: 'Amount', value: 20 },
            { name: 'Percent', value: true }
        ]
    },
    localizations: [{
        description: '+20% Production for production buildings (Dacia only)'
    }]
});
```

## Binding Modifiers to Game Elements

Modifiers generally need to be bound to game elements to take effect:

```typescript
import {
    CivilizationBuilder,
    ModifierBuilder,
    TraditionBuilder,
    UnitBuilder
} from "civ7-modding-tools";

// Create a civilization
const dacia = new CivilizationBuilder({
    // Configuration...
});

// Create a modifier
const dacianBonus = new ModifierBuilder({
    // Combat strength bonus configuration...
});

// Bind the modifier to the civilization
dacia.bind([dacianBonus]);

// Example: Binding to a tradition
const tradition = new TraditionBuilder({
    // Configuration...
});
tradition.bind([dacianBonus]);

// Example: Binding to a unit
const unit = new UnitBuilder({
    // Configuration...
});
unit.bind([dacianBonus]);
```

## Temporary vs. Permanent Modifiers

You can create temporary or permanent modifiers:

```typescript
// Permanent modifier (always active)
const permanentModifier = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.PLAYER_UNITS,
        effect: EFFECT.UNIT_ADJUST_COMBAT_STRENGTH,
        permanent: true,  // This makes it permanent
        arguments: [{ name: 'Amount', value: 3 }]
    },
    localizations: [{
        description: '+3 Combat Strength for all units (permanent)'
    }]
});

// Temporary modifier (not always active)
const temporaryModifier = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.PLAYER_UNITS,
        effect: EFFECT.UNIT_ADJUST_COMBAT_STRENGTH,
        permanent: false,  // This is the default if omitted
        requirements: [{
            type: REQUIREMENT.PLAYER_IS_AT_WAR
        }],
        arguments: [{ name: 'Amount', value: 5 }]
    },
    localizations: [{
        description: '+5 Combat Strength for all units during wartime'
    }]
});
```

## Conditional Modifiers with Multiple Requirements

Create complex conditional logic using multiple requirements:

```typescript
// Modifier with multiple requirements in AND relationship (all must be true)
const andRequirements = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.PLAYER_UNITS,
        effect: EFFECT.UNIT_ADJUST_COMBAT_STRENGTH,
        requirements: [
            { type: REQUIREMENT.UNIT_IS_DEFENDING },
            { type: REQUIREMENT.COMBAT_IS_IN_HILLS },
            { type: REQUIREMENT.UNIT_TAG_MATCHES, arguments: [{ name: 'Tag', value: 'MELEE' }] }
        ],
        arguments: [{ name: 'Amount', value: 10 }]
    },
    localizations: [{
        description: '+10 Combat Strength for Melee units when defending in hills'
    }]
});

// Using OR relationships requires multiple modifiers with the same effect
const attackOr = new ModifierBuilder({
    // +5 when attacking
    modifier: {
        collection: COLLECTION.PLAYER_UNITS,
        effect: EFFECT.UNIT_ADJUST_COMBAT_STRENGTH,
        requirements: [{ type: REQUIREMENT.UNIT_IS_ATTACKING }],
        arguments: [{ name: 'Amount', value: 5 }]
    }
});

const hillsOr = new ModifierBuilder({
    // +5 in hills
    modifier: {
        collection: COLLECTION.PLAYER_UNITS,
        effect: EFFECT.UNIT_ADJUST_COMBAT_STRENGTH,
        requirements: [{ type: REQUIREMENT.COMBAT_IS_IN_HILLS }],
        arguments: [{ name: 'Amount', value: 5 }]
    }
});

// Then add both modifiers to your mod
mod.add([attackOr, hillsOr]);
```

## Best Practices

When creating modifiers and effects, follow these best practices:

1. **Clear Descriptions**: Write clear descriptions for your modifiers so players understand what they do
2. **Balanced Effects**: Keep effects balanced to avoid overpowered gameplay
3. **Meaningful Requirements**: Use requirements that make sense thematically
4. **Test Combinations**: Test how modifiers interact with each other
5. **Consider Performance**: Complex modifiers with many requirements may impact performance
6. **Organize by Theme**: Group related modifiers together for easier maintenance

## Debugging Modifiers

When debugging modifiers, keep these tips in mind:

1. Check that the collection targets the correct entities
2. Verify that requirements are not too restrictive
3. Ensure that argument names and values are correct
4. Test with different combinations of game conditions
5. Use clear localization to help track which modifiers are active

## Conclusion

Modifiers and effects are powerful tools for creating unique gameplay mechanics in your Civilization VII mods. By combining different collections, effects, requirements, and arguments, you can create a wide variety of gameplay alterations that make your mod stand out. 