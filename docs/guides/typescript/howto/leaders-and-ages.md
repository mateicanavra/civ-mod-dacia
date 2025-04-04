# TypeScript Modding Tools: Leaders and Ages

This guide covers how to create leader-civilization associations and work with age-specific content using the Civilization VII TypeScript Modding Tools.

## Creating Leader-Civilization Associations

In Civilization VII, the TypeScript Modding Tools primarily support associating existing game leaders with civilizations rather than creating entirely new leaders. The toolkit provides the `LeaderUnlockBuilder` class for this purpose.

```typescript
import {
    ACTION_GROUP_BUNDLE,
    CivilizationUnlockBuilder,
    LeaderUnlockBuilder,
    Mod,
    AGE
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-leader-mod',
    version: '1.0',
});

// Define a leader-civilization unlock
// This associates an existing game leader with your custom civilization
const decebalusLeadsDacia = new LeaderUnlockBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    
    // Define the association
    leaderUnlock: {
        leaderType: 'LEADER_DECEBALUS',       // An existing leader
        type: 'CIVILIZATION_DACIA',           // Your custom civilization
        ageType: AGE.ANTIQUITY,
    },
    
    // AI leader preference for this civilization
    leaderCivilizationBias: {
        bias: 10,  // Higher bias = more likely to be chosen by AI
    },
    
    // Tooltip text that appears in the leader selection screen
    localizations: [{
        tooltip: `[B]Decebalus[/B] ruled [B]Dacia[/B].`
    }]
});

// Add to mod
mod.add([decebalusLeadsDacia]);
```

You can also define civilization transitions between Ages:

```typescript
// Create a civilization unlock for Age transitions
const daciaTransitionsToRome = new CivilizationUnlockBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_EXPLORATION,
    
    // Define which civilization transitions to what
    from: { 
        civilizationType: 'CIVILIZATION_DACIA',
        ageType: AGE.ANTIQUITY, 
    },
    to: { 
        civilizationType: 'CIVILIZATION_ROME',
        ageType: AGE.EXPLORATION, 
    },
});

// Add to mod
mod.add([daciaTransitionsToRome]);
```

## Creating Entirely New Leaders

To create entirely new leaders (rather than just associating existing ones), you'll need to use direct XML manipulation, as the toolkit doesn't provide a dedicated `LeaderBuilder` class. Here's how to approach this:

1. **Create XML Files Directly**: Define leaders using the database schema structure
2. **Import XML Files**: Use `ImportFileBuilder` to include your custom XML files

For example:

```typescript
import {
    ACTION_GROUP_BUNDLE,
    ACTION_GROUP_ACTION,
    ImportFileBuilder,
    Mod
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-custom-leader-mod',
    version: '1.0',
});

// Create leader XML content
const leaderXML = `<?xml version="1.0" encoding="utf-8"?>
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
</Database>`;

// Import the leader XML file
const leaderDefinition = new ImportFileBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    content: leaderXML,
    name: 'leaders/your_leader.xml',
    actionGroupActions: [ACTION_GROUP_ACTION.UPDATE_DATABASE]
});

// Add to mod
mod.add([leaderDefinition]);
```

## Working with Age-Specific Content

Civilization VII's Age system affects how leaders and civilizations evolve throughout the game. Here's how to work with age-specific content:

### Age-Specific Leader Abilities

You can create leader abilities that only apply in specific Ages:

```typescript
import {
    ACTION_GROUP_BUNDLE,
    ModifierBuilder,
    Mod,
    COLLECTION,
    EFFECT,
    REQUIREMENT,
    AGE
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-age-specific-mod',
    version: '1.0',
});

// Create an Antiquity Age-specific leader ability
const ancientLeaderBonus = new ModifierBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    modifier: {
        collection: COLLECTION.PLAYER_UNITS,
        effect: EFFECT.UNIT_ADJUST_COMBAT_STRENGTH,
        permanent: true,
        requirements: [
            {
                type: REQUIREMENT.PLAYER_IS_IN_AGE,
                arguments: [{ name: 'AgeType', value: AGE.ANTIQUITY }]
            }
        ],
        arguments: [{ name: 'Amount', value: 5 }]
    },
    localizations: [{
        description: 'Military Legacy: +5 Combat Strength for all units in the Antiquity Age.'
    }]
});

// Add to mod
mod.add([ancientLeaderBonus]);
```

### Leader Transitions Between Ages

Similar to civilizations, you can define how leaders transition between Ages:

```typescript
import {
    ACTION_GROUP_BUNDLE,
    LeaderUnlockBuilder,
    Mod,
    AGE
} from "civ7-modding-tools";

const mod = new Mod({
    id: 'my-leader-transition-mod',
    version: '1.0',
});

// Define how a leader should transform in a new Age
const decebalusToTrajan = new LeaderUnlockBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_EXPLORATION,
    leaderUnlock: {
        leaderType: 'LEADER_TRAJAN',          // New leader for new Age
        type: 'CIVILIZATION_ROME',            // New civilization
        ageType: AGE.EXPLORATION,             // New Age
        fromLeaderType: 'LEADER_DECEBALUS',   // Previous leader
        fromAgeType: AGE.ANTIQUITY            // Previous Age
    },
    localizations: [{
        tooltip: `As Rome conquered Dacia, [B]Trajan[/B] now rules the [B]Roman[/B] empire.`
    }]
});

// Add to mod
mod.add([decebalusToTrajan]);
```

## Best Practices for Age and Leader Content

When working with leaders across multiple Ages, keep these points in mind:

1. **Historical Accuracy**: Leaders should generally transition in ways that make historical sense
2. **Gameplay Balance**: Consider how a leader's abilities interact across different Ages
3. **Civilization Synergy**: Ensure leader abilities complement their civilization's strengths
4. **XML Documentation**: Thoroughly document any direct XML leader definitions
5. **Leader Traits**: Use appropriate traits to guide AI behavior in different Ages

## Multiple Leaders for the Same Civilization

You can define multiple leaders for the same civilization:

```typescript
// First leader for Dacia
const decebalusLeadsDacia = new LeaderUnlockBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    leaderUnlock: {
        leaderType: 'LEADER_DECEBALUS',
        type: 'CIVILIZATION_DACIA',
        ageType: AGE.ANTIQUITY,
    },
    // Configuration...
});

// Second leader for Dacia
const burebistLeadsDacia = new LeaderUnlockBuilder({
    actionGroupBundle: ACTION_GROUP_BUNDLE.AGE_ANTIQUITY,
    leaderUnlock: {
        leaderType: 'LEADER_BUREBISTA',
        type: 'CIVILIZATION_DACIA',
        ageType: AGE.ANTIQUITY,
    },
    // Configuration...
});

// Add both to mod
mod.add([decebalusLeadsDacia, burebistLeadsDacia]);
```

This approach gives players more choices when selecting a leader-civilization combination. 