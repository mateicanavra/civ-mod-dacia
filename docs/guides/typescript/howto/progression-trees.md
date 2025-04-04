# TypeScript Modding Tools: Creating Progression Trees

This guide covers how to create progression trees using the Civilization VII TypeScript Modding Tools.

## Understanding Progression Trees

Progression Trees represent technology or civic advancement paths in Civilization VII. They allow players to unlock new capabilities, units, buildings, and other gameplay elements as they progress through the game. Examples include technology trees and civic trees.

## Basic Progression Tree Creation

Creating a progression tree involves several steps:
1. Create the individual tree nodes (technologies/civics)
2. Define the benefits for each node
3. Set up the prerequisites between nodes
4. Create the progression tree itself
5. Bind the nodes to the tree

Here's a complete example:

```typescript
import {
    ACTION_GROUP_BUNDLE,
    ADVISORY,
    Mod,
    ModifierBuilder,
    ProgressionTreeBuilder,
    ProgressionTreeNodeBuilder,
    COLLECTION,
    EFFECT,
    YIELD,
    AGE
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-civics-mod',
    version: '1.0',
});

// Step 1: Create first civic node
const tribalUnity = new ProgressionTreeNodeBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    progressionTreeNode: {
        progressionTreeNodeType: 'NODE_CIVICS_TRIBAL_UNITY',
    },
    progressionTreeAdvisories: [ADVISORY.CLASS_CULTURE],
    localizations: [{
        name: 'Tribal Unity'
    }]
});

// Step 2: Create first node modifier
const unityModifier = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.OWNER,
        effect: EFFECT.PLAYER_ADJUST_CONSTRUCTIBLE_YIELD,
        arguments: [
            { name: 'Tag', value: 'CULTURE' },
            { name: 'YieldType', value: 'YIELD_CULTURE' },
            { name: 'Amount', value: 5 },
        ],
    },
    localizations: [{
        description: '+5 Culture from cultural buildings and improvements'
    }]
});

// Step 3: Create second civic node
const warCouncil = new ProgressionTreeNodeBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    progressionTreeNode: {
        progressionTreeNodeType: 'NODE_CIVICS_WAR_COUNCIL',
    },
    progressionTreeAdvisories: [ADVISORY.CLASS_MILITARY],
    localizations: [{
        name: 'War Council'
    }]
});

// Step 4: Create second node modifier
const warCouncilModifier = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.OWNER,
        effect: EFFECT.PLAYER_ADJUST_CONSTRUCTIBLE_YIELD,
        arguments: [
            { name: 'Tag', value: 'MILITARY' },
            { name: 'YieldType', value: 'YIELD_PRODUCTION' },
            { name: 'Amount', value: 10 },
        ],
    },
    localizations: [{
        description: '+10 Production from military buildings'
    }]
});

// Step 5: Bind modifiers to nodes
tribalUnity.bind([unityModifier]);
warCouncil.bind([warCouncilModifier]);

// Step 6: Create the progression tree
const dacianCivics = new ProgressionTreeBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    progressionTree: {
        progressionTreeType: `TREE_CIVICS_DACIA`,
        ageType: AGE.ANTIQUITY
    },
    // Define prerequisites (War Council requires Tribal Unity)
    progressionTreePrereqs: [{
        node: warCouncil.progressionTreeNode.progressionTreeNodeType,
        prereqNode: tribalUnity.progressionTreeNode.progressionTreeNodeType
    }],
    localizations: [{
        name: 'Dacian Traditions'
    }]
});

// Step 7: Bind nodes to tree
dacianCivics.bind([tribalUnity, warCouncil]);

// Step 8: Add everything to mod
mod.add([dacianCivics, tribalUnity, warCouncil]);

// Build the mod
mod.build('./dist');
```

## Key Components of Progression Trees

When creating progression trees, these are the essential components:

1. **Tree Type**: The overall category of the tree (civics, technology, etc.)
2. **Nodes**: Individual elements in the tree that can be researched or unlocked
3. **Prerequisites**: Relationships between nodes that define the progression path
4. **Modifiers**: Benefits or effects that are unlocked when a node is completed
5. **Advisories**: Categories that help classify and organize nodes
6. **Age Association**: The age in which the tree and its nodes are available

## Creating Complex Progression Trees

For more complex trees with multiple branches and nodes:

```typescript
import {
    ACTION_GROUP_BUNDLE,
    ADVISORY,
    Mod,
    ProgressionTreeBuilder,
    ProgressionTreeNodeBuilder,
    AGE
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-complex-tree-mod',
    version: '1.0',
});

// Create multiple nodes
const node1 = new ProgressionTreeNodeBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    progressionTreeNode: {
        progressionTreeNodeType: 'NODE_TECH_MINING',
    },
    progressionTreeAdvisories: [ADVISORY.CLASS_PRODUCTION],
    localizations: [{ name: 'Mining' }]
});

const node2 = new ProgressionTreeNodeBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    progressionTreeNode: {
        progressionTreeNodeType: 'NODE_TECH_BRONZE_WORKING',
    },
    progressionTreeAdvisories: [ADVISORY.CLASS_PRODUCTION],
    localizations: [{ name: 'Bronze Working' }]
});

const node3 = new ProgressionTreeNodeBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    progressionTreeNode: {
        progressionTreeNodeType: 'NODE_TECH_IRON_WORKING',
    },
    progressionTreeAdvisories: [ADVISORY.CLASS_MILITARY],
    localizations: [{ name: 'Iron Working' }]
});

const node4 = new ProgressionTreeNodeBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    progressionTreeNode: {
        progressionTreeNodeType: 'NODE_TECH_WHEEL',
    },
    progressionTreeAdvisories: [ADVISORY.CLASS_INFRASTRUCTURE],
    localizations: [{ name: 'Wheel' }]
});

const node5 = new ProgressionTreeNodeBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    progressionTreeNode: {
        progressionTreeNodeType: 'NODE_TECH_HORSEBACK_RIDING',
    },
    progressionTreeAdvisories: [ADVISORY.CLASS_MILITARY],
    localizations: [{ name: 'Horseback Riding' }]
});

// Create complex tree with multiple branches
const technologyTree = new ProgressionTreeBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    progressionTree: {
        progressionTreeType: `TREE_TECHNOLOGY_DACIA`,
        ageType: AGE.ANTIQUITY
    },
    // Define complex prerequisites with multiple branches
    progressionTreePrereqs: [
        // Branch 1: Mining -> Bronze Working -> Iron Working
        {
            node: node2.progressionTreeNode.progressionTreeNodeType,
            prereqNode: node1.progressionTreeNode.progressionTreeNodeType
        },
        {
            node: node3.progressionTreeNode.progressionTreeNodeType,
            prereqNode: node2.progressionTreeNode.progressionTreeNodeType
        },
        
        // Branch 2: Wheel -> Horseback Riding
        {
            node: node5.progressionTreeNode.progressionTreeNodeType,
            prereqNode: node4.progressionTreeNode.progressionTreeNodeType
        }
    ],
    localizations: [{
        name: 'Dacian Technologies'
    }]
});

// Bind nodes to tree
technologyTree.bind([node1, node2, node3, node4, node5]);

// Add everything to mod
mod.add([technologyTree, node1, node2, node3, node4, node5]);
```

## Unlockable Content

One of the primary purposes of progression trees is to unlock content when nodes are completed:

```typescript
import {
    ACTION_GROUP_BUNDLE,
    ADVISORY,
    Mod,
    ModifierBuilder,
    ProgressionTreeNodeBuilder,
    UnitBuilder,
    ConstructibleBuilder,
    COLLECTION,
    EFFECT,
    REQUIREMENT,
    UNIT_CLASS
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-unlockable-mod',
    version: '1.0',
});

// Create a unit to be unlocked
const falxWarrior = new UnitBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    unit: {
        unitType: 'UNIT_FALX_WARRIOR'
    },
    unitCost: { cost: 90 },
    unitStat: { combat: 35 },
    typeTags: [UNIT_CLASS.MELEE],
    localizations: [
        { name: 'Falx Warrior', description: 'Powerful Dacian warrior.' }
    ]
});

// Create a tech node that unlocks the unit
const ironWorkingTech = new ProgressionTreeNodeBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    progressionTreeNode: {
        progressionTreeNodeType: 'NODE_TECH_IRON_WORKING',
    },
    progressionTreeAdvisories: [ADVISORY.CLASS_MILITARY],
    localizations: [{ name: 'Iron Working' }]
});

// Create a modifier that enables the unit
const unitUnlockModifier = new ModifierBuilder({
    modifier: {
        collection: COLLECTION.OWNER,
        effect: EFFECT.PLAYER_ENABLE_UNIT,
        arguments: [
            { name: 'UnitType', value: falxWarrior.unit.unitType }
        ],
    },
    localizations: [{
        description: 'Unlocks the Falx Warrior unit'
    }]
});

// Bind the unlock modifier to the tech node
ironWorkingTech.bind([unitUnlockModifier]);

// Add a requirement to the unit (requires the tech)
falxWarrior.addRequirement({
    type: REQUIREMENT.PLAYER_HAS_PROGRESSION_TREE_NODE,
    arguments: [{ 
        name: 'ProgressionTreeNodeType', 
        value: ironWorkingTech.progressionTreeNode.progressionTreeNodeType 
    }]
});

// Add everything to mod
mod.add([ironWorkingTech, falxWarrior]);
```

## Age-Specific Progression Trees

Create progression trees that are specific to certain Ages:

```typescript
import {
    ACTION_GROUP_BUNDLE,
    ADVISORY,
    Mod,
    ProgressionTreeBuilder,
    ProgressionTreeNodeBuilder,
    AGE
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-age-tree-mod',
    version: '1.0',
});

// Antiquity Age tree
const antiquityTree = new ProgressionTreeBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    progressionTree: {
        progressionTreeType: `TREE_CIVICS_ANTIQUITY`,
        ageType: AGE.ANTIQUITY
    },
    // Configuration...
    localizations: [{
        name: 'Ancient Civics'
    }]
});

// Exploration Age tree
const explorationTree = new ProgressionTreeBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_EXPLORATION,
    progressionTree: {
        progressionTreeType: `TREE_CIVICS_EXPLORATION`,
        ageType: AGE.EXPLORATION
    },
    // Configuration...
    localizations: [{
        name: 'Exploration Civics'
    }]
});

// Add trees to mod
mod.add([antiquityTree, explorationTree]);
```

## Progression Tree Visualization

While the TypeScript tools primarily define the data structures, you can provide hints for how nodes should be visualized in-game:

```typescript
import {
    ACTION_GROUP_BUNDLE,
    ADVISORY,
    Mod,
    ProgressionTreeBuilder,
    ProgressionTreeNodeBuilder,
    ProgressionTreeVisualsBuilder,
    AGE
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-visuals-mod',
    version: '1.0',
});

// Create nodes
const node1 = new ProgressionTreeNodeBuilder({
    // Configuration...
});

const node2 = new ProgressionTreeNodeBuilder({
    // Configuration...
});

// Create tree
const customTree = new ProgressionTreeBuilder({
    // Configuration...
});

// Create visuals for the tree
const treeVisuals = new ProgressionTreeVisualsBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    progressionTreeVisuals: {
        progressionTreeType: customTree.progressionTree.progressionTreeType,
    },
    // Define node positions
    nodeVisuals: [
        {
            progressionTreeNodeType: node1.progressionTreeNode.progressionTreeNodeType,
            x: 100,
            y: 100
        },
        {
            progressionTreeNodeType: node2.progressionTreeNode.progressionTreeNodeType,
            x: 250,
            y: 100
        }
    ]
});

// Add everything to mod
mod.add([customTree, node1, node2, treeVisuals]);
```

## Best Practices

When creating progression trees, keep these best practices in mind:

1. **Logical Progression**: Ensure the tree has a logical flow and progression
2. **Balance Research Costs**: Consider the time investment for different paths
3. **Clear Prerequisites**: Make the dependency between nodes clear and intuitive
4. **Meaningful Unlocks**: Each node should provide meaningful benefits
5. **Consistent Naming**: Use consistent naming conventions for nodes
6. **Age Appropriateness**: Ensure content fits historically with its age
7. **Branching Options**: Provide meaningful choice through multiple branches
8. **Visual Layout**: Consider how the tree will be visualized in-game

## Conclusion

Progression Trees are a powerful way to create advancement paths for players. By designing thoughtful trees with meaningful unlocks, you can guide player progression and provide engaging gameplay choices throughout the different Ages of Civilization VII. 