# TypeScript Modding Tools: Advanced Techniques

This guide covers advanced techniques for creating sophisticated mods using the Civilization VII TypeScript Modding Tools.

## Binding Related Entities

One of the most powerful features of the TypeScript Modding Tools is the ability to bind related entities together, simplifying the creation of cohesive mod components that work together seamlessly.

### Understanding Entity Binding

Binding allows you to create relationships between different game elements without manually managing references. For example, when binding a unit to a civilization:

- The unit is automatically associated with the civilization
- The civilization automatically gains access to the unit
- Cross-references are maintained correctly in the generated XML

### Basic Binding Example

```typescript
import {
    ACTION_GROUP_BUNDLE,
    CivilizationBuilder,
    Mod,
    UnitBuilder
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'binding-example-mod',
    version: '1.0',
});

// Create a civilization
const dacia = new CivilizationBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    civilization: {
        type: 'CIVILIZATION_DACIA',
        name: 'Dacia',
        // Other properties...
    }
});

// Create a unique unit
const falxWarrior = new UnitBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    unit: {
        unitType: 'UNIT_FALX_WARRIOR',
        name: 'Falx Warrior',
        // Other properties...
    }
});

// Bind the unit to the civilization
dacia.bind([falxWarrior]);

// Add to mod
mod.add([dacia, falxWarrior]);

// Build the mod
mod.build('./dist');
```

### Binding Multiple Entities

You can bind multiple entities in a single operation:

```typescript
// Create multiple entities
const dacia = new CivilizationBuilder({...});
const falxWarrior = new UnitBuilder({...});
const dacianFortress = new BuildingBuilder({...});
const dacianWarfare = new ModifierBuilder({...});

// Bind all entities to the civilization in one call
dacia.bind([falxWarrior, dacianFortress, dacianWarfare]);

// Now all entities are properly associated with the Dacia civilization
```

### Chain Binding

You can create chains of bindings for more complex relationships:

```typescript
// Create entities
const dacia = new CivilizationBuilder({...});
const falxWarrior = new UnitBuilder({...});
const combatBonus = new ModifierBuilder({...});

// Bind the combat bonus to the unit
falxWarrior.bind([combatBonus]);

// Bind the unit to the civilization
dacia.bind([falxWarrior]);

// The entire chain of relationships is maintained
```

## Age-Specific Content

Civilization VII's Age system allows for different versions of game elements across different time periods. The TypeScript Modding Tools provide robust support for creating age-specific content.

### Creating Age-Specific Elements

The key to creating age-specific content is using the appropriate `actionGroupBundle` when creating elements:

```typescript
import {
    ACTION_GROUP_BUNDLE,
    Mod,
    UnitBuilder
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'age-specific-mod',
    version: '1.0',
});

// Antiquity Age version of a unit
const antiquityWarrior = new UnitBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    unit: {
        unitType: 'UNIT_WARRIOR',
        name: 'Warrior',
        combatStrength: 15,
        // Other properties specific to Antiquity Age
    }
});

// Exploration Age version of the same unit
const explorationWarrior = new UnitBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_EXPLORATION,
    unit: {
        unitType: 'UNIT_WARRIOR',
        name: 'Warrior',
        combatStrength: 25,
        // Updated properties for Exploration Age
    }
});

// Modern Age version of the same unit
const modernWarrior = new UnitBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_MODERN,
    unit: {
        unitType: 'UNIT_WARRIOR',
        name: 'Warrior',
        combatStrength: 35,
        // Updated properties for Modern Age
    }
});

// Add all versions to the mod
mod.add([antiquityWarrior, explorationWarrior, modernWarrior]);
```

### Managing Age Transitions

To properly manage age transitions, you need to ensure that:

1. Each age version uses the same type identifier
2. Each version is assigned to the correct age using `actionGroupBundle`
3. Changes between ages are balanced and appropriate

### Age-Specific Civilizations

You can create civilizations that change their abilities across different ages:

```typescript
// Antiquity Age civilization
const daciaAntiquity = new CivilizationBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    civilization: {
        type: 'CIVILIZATION_DACIA',
        name: 'Dacia',
        // Antiquity-specific properties
    }
});

// Exploration Age civilization (same type, different abilities)
const daciaExploration = new CivilizationBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_EXPLORATION,
    civilization: {
        type: 'CIVILIZATION_DACIA',
        name: 'Dacia',
        // Exploration-specific properties
    }
});

// Bind age-specific units to their respective civilization versions
daciaAntiquity.bind([antiquityUnit]);
daciaExploration.bind([explorationUnit]);
```

## Working with Multiple Files

For larger mods, organizing your code across multiple files improves maintainability and allows for better collaboration.

### Project Structure Example

A typical organized mod project might have this structure:

```
my-mod/
├── src/
│   ├── civilizations/
│   │   └── dacia.ts
│   ├── units/
│   │   ├── falx-warrior.ts
│   │   └── mountain-scout.ts
│   ├── buildings/
│   │   └── dacian-fortress.ts
│   └── modifiers/
│       └── dacian-warfare.ts
├── assets/
│   ├── civ-icon.png
│   └── unit-icons/
│       └── falx-warrior.png
├── build.ts
└── package.json
```

### Component Files

Each component can be defined in its own file:

**src/civilizations/dacia.ts**:
```typescript
import { CivilizationBuilder, ACTION_GROUP_BUNDLE } from "civ7-modding-tools";

export function createDacianCivilization() {
    return new CivilizationBuilder({
        actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
        civilization: {
            type: 'CIVILIZATION_DACIA',
            name: 'Dacia',
            // Other properties...
        }
    });
}
```

**src/units/falx-warrior.ts**:
```typescript
import { UnitBuilder, ACTION_GROUP_BUNDLE } from "civ7-modding-tools";

export function createFalxWarrior() {
    return new UnitBuilder({
        actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
        unit: {
            unitType: 'UNIT_FALX_WARRIOR',
            name: 'Falx Warrior',
            // Other properties...
        }
    });
}
```

### Main Build File

The main build file imports and combines all components:

**build.ts**:
```typescript
import { Mod } from "civ7-modding-tools";
import { createDacianCivilization } from "./src/civilizations/dacia";
import { createFalxWarrior } from "./src/units/falx-warrior";
import { createMountainScout } from "./src/units/mountain-scout";
import { createDacianFortress } from "./src/buildings/dacian-fortress";
import { createDacianWarfareModifier } from "./src/modifiers/dacian-warfare";

// Create the mod
const mod = new Mod({
    id: 'dacia-complete-mod',
    version: '1.0',
});

// Get all components
const dacia = createDacianCivilization();
const falxWarrior = createFalxWarrior();
const mountainScout = createMountainScout();
const dacianFortress = createDacianFortress();
const dacianWarfare = createDacianWarfareModifier();

// Bind components to civilization
dacia.bind([falxWarrior, mountainScout, dacianFortress, dacianWarfare]);

// Add everything to mod
mod.add([
    dacia,
    falxWarrior, 
    mountainScout, 
    dacianFortress, 
    dacianWarfare
]);

// Build mod
mod.build('./dist');
```

### Benefits of Multi-File Organization

This approach offers several advantages:

1. **Modularity**: Each component is self-contained and can be developed independently
2. **Reusability**: Components can be easily reused across different mods
3. **Maintenance**: Easier to find and fix issues in smaller files
4. **Collaboration**: Different team members can work on different files without conflicts
5. **Testing**: Individual components can be tested in isolation

## Manually Configuring Nodes

For maximum control, you can directly work with XML nodes instead of using the higher-level builders.

### Working with Low-Level Node API

The low-level node API gives you direct access to the XML structure:

```typescript
import {
    ACTION_GROUP,
    ACTION_GROUP_ACTION,
    DatabaseNode,
    KIND,
    Mod,
    TypeNode,
    UnitNode,
    XmlFile
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'manual-node-mod',
    version: '1.0',
});

// Create unit node
const unit = new UnitNode({
    unitType: 'UNIT_CUSTOM_SCOUT',
    baseMoves: 2,
    baseSightRange: 10,
    maintenanceCost: 1,
    combatStrength: 15,
    rangedCombatStrength: 0,
    range: 0,
    domain: 'DOMAIN_LAND'
});

// Create type node
const typeNode = new TypeNode({ 
    type: unit.unitType, 
    kind: KIND.UNIT 
});

// Create database node
const database = new DatabaseNode({
    types: [typeNode],
    units: [unit]
    // Other collections...
});

// Create XML file
const unitFile = new XmlFile({
    path: `/units/${unit.unitType}.xml`,
    name: 'unit.xml',
    content: database.toXmlElement(),
    actionGroups: [ACTION_GROUP.AGE_ANTIQUITY_CURRENT],
    actionGroupActions: [ACTION_GROUP_ACTION.UPDATE_DATABASE]
});

// Add file to mod
mod.addFiles([unitFile]);

// Build mod
mod.build('./dist');
```

### When to Use the Low-Level API

Consider using the low-level node API in these situations:

1. When you need to create structures not supported by the builders
2. When you're implementing experimental features
3. When working with custom database tables
4. When you need precise control over the XML structure

### Custom Database Modifications

You can directly modify database structures:

```typescript
// Create custom database nodes
const customTable = new DatabaseTableNode({
    name: 'CustomGameElements',
    columns: [
        { name: 'Type', type: 'TEXT', primary: true },
        { name: 'Value', type: 'INTEGER' },
        { name: 'Description', type: 'TEXT' }
    ],
    rows: [
        { Type: 'ELEMENT_ALPHA', Value: 10, Description: 'Alpha Element' },
        { Type: 'ELEMENT_BETA', Value: 20, Description: 'Beta Element' },
        { Type: 'ELEMENT_GAMMA', Value: 30, Description: 'Gamma Element' }
    ]
});

// Add custom table to database
const database = new DatabaseNode({
    // Standard collections
    types: [typeNode],
    units: [unit],
    // Custom collections
    customGameElements: [customTable]
});
```

## Creating Custom Builders

For truly advanced mods, you can extend the built-in tools by creating custom builders.

### Creating a Custom Builder Class

```typescript
import { BuilderBase, ACTION_GROUP_BUNDLE } from "civ7-modding-tools";

// Create a custom builder for a game element not directly supported
export class ReligionBuilder extends BuilderBase {
    constructor(config) {
        super(config);
        this.religion = config.religion;
        this.actionGroupBundle = config.actionGroupBundle || ACTION_GROUP_BUNDLE.AGE_ANTIQUITY;
    }

    // Override the build method
    build() {
        // Create required nodes
        const typeNode = new TypeNode({ 
            type: this.religion.type, 
            kind: 'KIND_RELIGION' 
        });

        const religionNode = new CustomNode({
            type: this.religion.type,
            name: this.religion.name,
            // Other properties...
        });

        // Create database with nodes
        const database = new DatabaseNode({
            types: [typeNode],
            religions: [religionNode]
        });

        // Create XML file
        const xmlFile = new XmlFile({
            path: `/religions/${this.religion.type}.xml`,
            name: 'religion.xml',
            content: database.toXmlElement(),
            actionGroups: this.getActionGroups(),
            actionGroupActions: [ACTION_GROUP_ACTION.UPDATE_DATABASE]
        });

        return [xmlFile];
    }
}
```

### Using a Custom Builder

```typescript
// Use your custom builder
const customReligion = new ReligionBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    religion: {
        type: 'RELIGION_CUSTOM',
        name: 'Custom Religion',
        // Other properties...
    }
});

// Add to mod
mod.add([customReligion]);
```

## Performance Optimization

When creating large mods, consider these performance optimization techniques:

### Lazy Loading

Load components only when needed:

```typescript
// Instead of importing everything at once
import { createAllDacianUnits } from "./src/units/all-units";

// Lazy load specific units as needed
const getSpecificUnit = () => {
    return import("./src/units/specific-unit")
        .then(module => module.createSpecificUnit());
};
```

### Reusing Components

Reuse components when appropriate:

```typescript
// Create a base warrior template
const baseWarrior = {
    baseMoves: 2,
    baseSightRange: 2,
    domain: 'DOMAIN_LAND',
    // Common properties...
};

// Create variants by extending the base
const meleeWarrior = new UnitBuilder({
    unit: {
        ...baseWarrior,
        unitType: 'UNIT_MELEE_WARRIOR',
        name: 'Melee Warrior',
        combatStrength: 15
    }
});

const rangedWarrior = new UnitBuilder({
    unit: {
        ...baseWarrior,
        unitType: 'UNIT_RANGED_WARRIOR',
        name: 'Ranged Warrior',
        combatStrength: 10,
        rangedCombatStrength: 15,
        range: 1
    }
});
```

## Conclusion

Advanced techniques in the Civilization VII TypeScript Modding Tools allow you to create sophisticated mods with complex interactions between game elements. By mastering entity binding, age-specific content, multi-file organization, and low-level node configuration, you can create mods that are both powerful and maintainable.

Remember that these advanced techniques build upon the foundation covered in the other how-to guides. As you become more comfortable with the tools, you'll be able to create increasingly complex and impressive mods for Civilization VII. 