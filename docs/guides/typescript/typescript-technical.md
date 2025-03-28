# Civilization VII TypeScript Modding Tools: Technical Implementation

This document provides a deep technical analysis of the Civilization VII TypeScript Modding Tools, focusing on the internal architecture, implementation patterns, and relationship with the game's data structures.

## Documentation Structure

The TypeScript Modding Tools documentation is divided into three complementary parts:

1. **[Main Documentation](/guides/typescript/typescript-overview.md)** - Covers general concepts, features, and limitations
2. **This Technical Guide** - Details the toolkit's architecture and internal implementation
3. **How-To Guides**: A series of practical tutorials for common modding tasks:
   - [Environment Setup](/guides/typescript/howto/environment-setup.md)
   - [Creating Units](/guides/typescript/howto/creating-units.md)
   - [Creating Buildings](/guides/typescript/howto/creating-buildings.md)
   - [Creating Civilizations](/guides/typescript/howto/creating-civilizations.md)
   - [Leaders and Ages](/guides/typescript/howto/leaders-and-ages.md)
   - [Unique Quarters](/guides/typescript/howto/unique-quarters.md)
   - [Traditions](/guides/typescript/howto/traditions.md)
   - [Progression Trees](/guides/typescript/howto/progression-trees.md)
   - [Modifiers and Effects](/guides/typescript/howto/modifiers-and-effects.md)
   - [Assets and Icons](/guides/typescript/howto/assets-and-icons.md)
   - [Advanced Techniques](/guides/typescript/howto/advanced-techniques.md)

## Repository Structure

The modding toolkit is organized with a clear separation of concerns:

```
civ7-modding-tools/
├── src/
│   ├── builders/        # Builder classes for game entities
│   ├── constants/       # Game-defined constants and enums
│   ├── core/            # Core functionality
│   ├── nodes/           # XML node representations
│   └── utils/           # Utility functions
├── examples/            # Example implementations
├── assets/              # Sample assets for examples
├── build.ts             # Main build script for examples
└── package.json         # Project configuration
```

## Core Architecture

The toolkit uses a layered architecture combining builder pattern, factory methods, and composable nodes:

1. **Builders** - High-level abstractions to create game entities
2. **Nodes** - XML node representations that map to database entries
3. **Files** - File generators that combine nodes into proper XML files
4. **Mod** - Top-level container that manages files and builds the mod

### Mod Lifecycle

The typical lifecycle of a mod created with this toolkit:

1. Create a `Mod` instance
2. Define entities using builders
3. Bind related entities together
4. Add entities to the mod
5. Build the mod, generating all required files

## Builder Implementation

All builder classes extend the `BaseBuilder` class which provides core functionality:

```typescript
// Simplified BaseBuilder implementation
export class BaseBuilder {
    constructor(options: BaseBuilderOptions) {
        this.actionGroupBundle = options.actionGroupBundle;
        this.initialize();
    }

    // Creates nodes and manages relationships
    initialize() { }
    
    // Binds related entities
    bind(entities: BaseBuilder[]) {
        // Implementation
        return this;
    }
    
    // Returns files to be added to the mod
    getFiles(): (XmlFile | ImportFile)[] {
        // Implementation
        return [];
    }
}
```

### CivilizationBuilder

The `CivilizationBuilder` is one of the most complex implementations:

```typescript
export class CivilizationBuilder extends BaseBuilder {
    civilization: CivilizationNode;
    civilizationTag: TypeNode;
    // Other properties...

    initialize() {
        // Create civilization node
        this.civilization = new CivilizationNode(this.options.civilization);
        
        // Create type node
        this.civilizationTag = new TypeNode({
            type: this.civilization.civilizationType,
            kind: KIND.CIVILIZATION
        });
        
        // Create trait nodes, city names, etc.
        // ...
    }
    
    bind(entities: BaseBuilder[]) {
        // Process each entity to establish relationships
        entities.forEach(entity => {
            if (entity instanceof UnitBuilder) {
                this.bindUnit(entity);
            } else if (entity instanceof ConstructibleBuilder) {
                this.bindConstructible(entity);
            }
            // Other bindings...
        });
        
        return this;
    }
    
    // Helper methods for binding specific entity types
    private bindUnit(unit: UnitBuilder) { /* Implementation */ }
    private bindConstructible(constructible: ConstructibleBuilder) { /* Implementation */ }
    // Other helpers...
    
    getFiles() {
        // Generate database file
        const database = new DatabaseNode({
            types: [this.civilizationTag],
            civilizations: [this.civilization],
            // Other collections...
        });
        
        // Create civilization file
        const civFile = new XmlFile({
            path: `/civilizations/${this.civilization.civilizationType}.xml`,
            content: database.toXmlElement(),
            // Other properties...
        });
        
        return [civFile, /* Other files... */];
    }
}
```

### UnitBuilder

The `UnitBuilder` demonstrates how specialized builders are implemented:

```typescript
export class UnitBuilder extends BaseBuilder {
    unit: UnitNode;
    unitTag: TypeNode;
    // Other properties...
    
    initialize() {
        // Create unit node
        this.unit = new UnitNode(this.options.unit);
        
        // Create type node
        this.unitTag = new TypeNode({
            type: this.unit.unitType,
            kind: KIND.UNIT
        });
        
        // Create cost, stat, and other nodes
        // ...
    }
    
    getFiles() {
        // Generate database file
        const database = new DatabaseNode({
            types: [this.unitTag],
            units: [this.unit],
            // Other collections...
        });
        
        // Create unit file
        const unitFile = new XmlFile({
            path: `/units/${this.unit.unitType}.xml`,
            content: database.toXmlElement(),
            // Other properties...
        });
        
        return [unitFile];
    }
}
```

## Constants and Game Data Mapping

The constants in the toolkit are directly mapped from game files to TypeScript enums. These ensure type safety and help with autocomplete.

### Constants Structure

Constants are organized into categories matching game systems:

```typescript
// Example of UNIT_CLASS constants
export const UNIT_CLASS = {
    RECON: 'RECON',
    MELEE: 'MELEE',
    RANGED: 'RANGED',
    CAVALRY: 'CAVALRY',
    SIEGE: 'SIEGE',
    NAVAL_MELEE: 'NAVAL_MELEE',
    NAVAL_RANGED: 'NAVAL_RANGED',
    SUPPORT: 'SUPPORT',
    CIVILIAN: 'CIVILIAN',
    RECON_ABILITIES: 'RECON_ABILITIES',
    // ...
} as const;
```

### Mapping to Game Data

To understand how constants are derived from game data, let's compare with actual game files:

1. **CONSTRUCTIBLE_TYPE_TAG** - Maps to `TypeTags` in constructibles.xml:

```typescript
// In constants/CONSTRUCTIBLE_TYPE_TAG.ts
export const CONSTRUCTIBLE_TYPE_TAG = {
    AGELESS: 'AGELESS',
    PRODUCTION: 'PRODUCTION',
    FOOD: 'FOOD',
    GOLD: 'GOLD',
    SCIENCE: 'SCIENCE',
    CULTURE: 'CULTURE',
    HAPPINESS: 'HAPPINESS',
    DIPLOMACY: 'DIPLOMACY',
    RELIGION: 'RELIGION',
    UNIQUE: 'UNIQUE',
    // ...
} as const;
```

```xml
<!-- In constructibles.xml -->
<TypeTags>
    <Row Type="BUILDING_FISHING_QUAY" Tag="FOOD"/>
    <Row Type="BUILDING_FISHING_QUAY" Tag="WAREHOUSE"/>
    <Row Type="BUILDING_FISHING_QUAY" Tag="AGELESS"/>
    <Row Type="BUILDING_FISHING_QUAY" Tag="PERSISTENT"/>
    <Row Type="BUILDING_FISHING_QUAY" Tag="WATER"/>
    <!-- ... -->
</TypeTags>
```

2. **EFFECT** - Maps to modifier effects defined in the game:

```typescript
// In constants/EFFECT.ts (simplified)
export const EFFECT = {
    UNIT_ADJUST_COMBAT_STRENGTH: 'EFFECT_ADJUST_UNIT_COMBAT_STRENGTH',
    UNIT_ADJUST_MOVEMENT: 'EFFECT_ADJUST_UNIT_MOVEMENT',
    CITY_ADJUST_YIELD: 'EFFECT_ADJUST_CITY_YIELD',
    // ...
} as const;
```

3. **ACTION_GROUP** - Controls when files are loaded:

```typescript
// In constants/ACTION_GROUP.ts
export const ACTION_GROUP = {
    AGE_ANTIQUITY_CURRENT: 'AGE_ANTIQUITY',
    AGE_EXPLORATION_CURRENT: 'AGE_EXPLORATION',
    AGE_MODERN_CURRENT: 'AGE_MODERN',
    // ...
} as const;
```

### Comprehensive Constants Coverage

The toolkit includes constants for:

- **Basic Game Elements**: Units, buildings, districts, civilizations
- **Modifiers**: Collections, effects, requirements
- **Yields**: Production, food, gold, science, culture, etc.
- **Terrains and Features**: Mountains, rivers, forests, etc.
- **Resources**: Strategic and luxury resources
- **Action Groups**: File loading and database actions

## Node System Implementation

The node system represents the backbone of the XML generation process. Each node corresponds to a database entry or XML element.

### Node Base Class

All nodes inherit from a base class that provides XML conversion:

```typescript
export class BaseNode {
    // Converts the node to an XML element
    toXmlElement(): XmlElement {
        // Implementation
    }
}
```

### DatabaseNode

The `DatabaseNode` represents a complete database XML file:

```typescript
export class DatabaseNode extends BaseNode {
    types: TypeNode[];
    units: UnitNode[];
    civilizations: CivilizationNode[];
    // Other collections...
    
    constructor(options: DatabaseNodeOptions) {
        super();
        this.types = options.types || [];
        this.units = options.units || [];
        // Assign other collections...
    }
    
    toXmlElement(): XmlElement {
        const database = new XmlElement('Database');
        
        // Add types section if not empty
        if (this.types.length) {
            const types = new XmlElement('Types');
            this.types.forEach(type => {
                types.addChild(type.toXmlElement());
            });
            database.addChild(types);
        }
        
        // Add units section if not empty
        if (this.units.length) {
            const units = new XmlElement('Units');
            this.units.forEach(unit => {
                units.addChild(unit.toXmlElement());
            });
            database.addChild(units);
        }
        
        // Process other collections...
        
        return database;
    }
}
```

### Specialized Nodes

Each game entity has its own node type:

```typescript
export class UnitNode extends BaseNode {
    unitType: string;
    baseMoves: number;
    baseSightRange: number;
    // Other properties...
    
    constructor(options: UnitNodeOptions) {
        super();
        this.unitType = options.unitType;
        this.baseMoves = options.baseMoves;
        this.baseSightRange = options.baseSightRange;
        // Assign other properties...
    }
    
    toXmlElement(): XmlElement {
        const element = new XmlElement('Row');
        element.addAttribute('UnitType', this.unitType);
        element.addAttribute('BaseMoves', this.baseMoves.toString());
        element.addAttribute('BaseSightRange', this.baseSightRange.toString());
        // Add other attributes...
        return element;
    }
}
```

## File Generation Process

The toolkit generates two primary types of files:

1. **XmlFile** - XML database files with game data
2. **ImportFile** - Asset files like icons or SQL scripts

### XmlFile Implementation

```typescript
export class XmlFile {
    path: string;
    name: string;
    content: XmlElement;
    actionGroups: string[];
    actionGroupActions: string[];
    
    constructor(options: XmlFileOptions) {
        this.path = options.path;
        this.name = options.name;
        this.content = options.content;
        this.actionGroups = options.actionGroups || [];
        this.actionGroupActions = options.actionGroupActions || [];
    }
    
    // Returns the file content as a string
    toString(): string {
        return '<?xml version="1.0" encoding="utf-8"?>\n' + 
               this.content.toString();
    }
    
    // Generates action group XML entries for modinfo file
    getActionGroupXml(): string {
        // Implementation
    }
}
```

### ImportFile Implementation

```typescript
export class ImportFile {
    path: string;
    name: string;
    content: string | Buffer;
    actionGroups: string[];
    actionGroupActions: string[];
    
    constructor(options: ImportFileOptions) {
        this.path = options.path;
        this.name = options.name;
        this.content = options.content;
        this.actionGroups = options.actionGroups || [];
        this.actionGroupActions = options.actionGroupActions || [];
    }
    
    // Gets file content
    getContent(): string | Buffer {
        return this.content;
    }
    
    // Generates action group XML entries for modinfo file
    getActionGroupXml(): string {
        // Implementation
    }
}
```

## Mod Class Implementation

The `Mod` class orchestrates the entire mod generation process:

```typescript
export class Mod {
    id: string;
    version: string;
    files: (XmlFile | ImportFile)[];
    
    constructor(options: ModOptions) {
        this.id = options.id;
        this.version = options.version;
        this.files = [];
    }
    
    // Adds builders to the mod
    add(builders: BaseBuilder[]) {
        builders.forEach(builder => {
            this.addFiles(builder.getFiles());
        });
        return this;
    }
    
    // Adds files directly to the mod
    addFiles(files: (XmlFile | ImportFile)[]) {
        this.files.push(...files);
        return this;
    }
    
    // Builds the mod, writing all files to disk
    build(outputPath: string) {
        // Create mod directory structure
        // Generate modinfo file
        // Write all files
        // Implementation...
    }
    
    // Generates the modinfo file
    private generateModinfo(): string {
        // Implementation
    }
    
    // Helper methods...
}
```

## Technical Case Study: Creating a Civilization

To illustrate the complete process, let's walk through the creation of a civilization with the toolkit:

### 1. Builder Initialization

```typescript
const civilization = new CivilizationBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    civilization: {
        domain: 'AntiquityAgeCivilizations',
        civilizationType: 'CIVILIZATION_DACIA'
    },
    civilizationTraits: [
        TRAIT.ANTIQUITY_CIV,
        TRAIT.ATTRIBUTE_MILITARISTIC,
    ],
    // Other properties...
});
```

### 2. Node Creation (Inside Initialize)

```typescript
initialize() {
    // Create civilization node
    this.civilization = new CivilizationNode({
        domain: 'AntiquityAgeCivilizations',
        civilizationType: 'CIVILIZATION_DACIA'
    });
    
    // Create type node
    this.civilizationTag = new TypeNode({
        type: 'CIVILIZATION_DACIA',
        kind: KIND.CIVILIZATION
    });
    
    // Create trait nodes
    this.civilizationTraits = [
        new CivilizationTraitNode({
            civilizationType: 'CIVILIZATION_DACIA',
            traitType: TRAIT.ANTIQUITY_CIV
        }),
        new CivilizationTraitNode({
            civilizationType: 'CIVILIZATION_DACIA',
            traitType: TRAIT.ATTRIBUTE_MILITARISTIC
        })
    ];
    
    // Other nodes...
}
```

### 3. Binding Related Entities

```typescript
bind(entities: BaseBuilder[]) {
    entities.forEach(entity => {
        if (entity instanceof ModifierBuilder) {
            // Add modifier to civilization
            this.modifiers.push(entity.modifier);
            
            // Add modifier to list of files
            this.modifierFiles.push(entity.getFiles()[0]);
        } else if (entity instanceof UnitBuilder) {
            // Set unit as a civilization unit
            entity.unit.civilizationType = this.civilization.civilizationType;
            
            // Add unit to list of files
            this.unitFiles.push(entity.getFiles()[0]);
        }
        // Other bindings...
    });
    
    return this;
}
```

### 4. File Generation

```typescript
getFiles() {
    // Create database node
    const database = new DatabaseNode({
        types: [this.civilizationTag],
        civilizations: [this.civilization],
        civilizationTraits: this.civilizationTraits,
        // Other collections...
    });
    
    // Create civilization file
    const civFile = new XmlFile({
        path: `/civilizations/${this.civilization.civilizationType}.xml`,
        name: `${this.civilization.civilizationType}.xml`,
        content: database.toXmlElement(),
        actionGroups: [ACTION_GROUP.AGE_ANTIQUITY_CURRENT],
        actionGroupActions: [ACTION_GROUP_ACTION.UPDATE_DATABASE]
    });
    
    // Return all files
    return [civFile, ...this.modifierFiles, ...this.unitFiles, ...this.localizationFiles];
}
```

### 5. Mod Building

```typescript
// Add to mod
mod.add([civilization]);

// Build the mod to output directory
mod.build('./output-directory');
```

### 6. Generated XML (Example)

```xml
<?xml version="1.0" encoding="utf-8"?>
<Database>
  <Types>
    <Row Type="CIVILIZATION_DACIA" Kind="KIND_CIVILIZATION"/>
  </Types>
  <Civilizations>
    <Row CivilizationType="CIVILIZATION_DACIA" Domain="AntiquityAgeCivilizations"/>
  </Civilizations>
  <CivilizationTraits>
    <Row CivilizationType="CIVILIZATION_DACIA" TraitType="TRAIT_ANTIQUITY_CIV"/>
    <Row CivilizationType="CIVILIZATION_DACIA" TraitType="TRAIT_ATTRIBUTE_MILITARISTIC"/>
  </CivilizationTraits>
  <!-- Other sections... -->
</Database>
```

## Performance Considerations

The toolkit's performance is highly optimized through several techniques:

1. **Lazy Evaluation** - Nodes are only converted to XML when needed
2. **Reuse of String Constants** - String constants are defined once and reused
3. **Minimal Dependencies** - No external dependencies for the core functionality
4. **Efficient File I/O** - Files are written in parallel when possible

## Extension Points

The toolkit is designed to be extended through several mechanisms:

1. **Creating Custom Builders** - Extend `BaseBuilder` for specialized builders
2. **Adding Custom Nodes** - Create new node types for game elements
3. **Extending Constants** - Add new constants as game content expands
4. **Custom File Types** - Implement additional file types beyond XML and assets

## Related Documentation

- [Main Documentation](/guides/typescript/typescript-overview.md) - General overview and features
- [How-To Guides](/guides/typescript/howto/) - Step-by-step tutorials for common tasks
- [Modding Architecture Overview](/guides/modding-architecture.md) - General Civilization VII modding framework

## Conclusion

The Civilization VII TypeScript Modding Tools represent a sophisticated implementation of the builder pattern, combined with strong typing and declarative APIs. By mapping closely to the game's internal data structure, the toolkit provides a powerful abstraction that simplifies mod development while maintaining the flexibility that advanced modders expect.

The layered architecture, with builders at the top level and nodes at the core, creates a system that is both easy to use for simple mods and powerful enough for complex ones. The comprehensive constants system ensures that modders can leverage autocomplete and type checking to avoid errors.

For developers looking to extend the toolkit or understand its inner workings, this technical implementation guide provides the foundation needed to navigate the codebase and build upon it. 