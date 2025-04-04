# Civilization VII TypeScript Modding Tools

## Overview

The Civilization VII TypeScript Modding Tools are a developer-friendly framework that provides a declarative approach to mod creation, eliminating the need to manually edit complex XML files. Created by GitHub user "izica" in March 2025, these tools use TypeScript's type safety and object-oriented features to simplify the mod development process.

As the creator explains: "I almost lost my mind after spending several evenings editing XML files, trying to create my own civilization. This led me to the thought that I'd like to have a more declarative way to develop content mods."

## Key Features

- **Declarative API**: Define game elements using strongly-typed builder classes
- **Auto-generation**: Automatically generates all required XML files in the correct structure
- **Type Safety**: Leverages TypeScript's type system to prevent errors and provide autocomplete
- **Component Binding**: Easily link related mod elements (civilizations, units, buildings, etc.)
- **Age-aware**: Built with Civilization VII's Ages system in mind

## Documentation Structure

To help you make the most of the TypeScript Modding Tools, we provide three complementary document sections:

1. **This Overview Document** - Covers general concepts, features, and limitations
2. **[Technical Implementation Guide](/guides/typescript/typescript-technical.md)** - Details the toolkit's architecture and internal workings
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

## Installation Options

### Option 1: From NPM

```bash
npm install civ7-modding-tools
```

```typescript
import { Mod } from 'civ7-modding-tools';
// or import from 'civ7-modding-tools/src' for full typescript source

let mod = new Mod({
    id: 'my-mod',
    version: '1',
});
// Add mod content here
mod.build('./output-directory');
```

### Option 2: From GitHub Repository

1. Install Node.js from [https://nodejs.org/](https://nodejs.org/)
2. Clone or download the repository:
   ```bash
   git clone https://github.com/izica/civ7-modding-tools
   ```
3. Navigate to the repository directory and install dependencies:
   ```bash
   cd civ7-modding-tools
   npm install
   ```
4. Modify the `build.ts` file to define your mod
5. Run the build command:
   ```bash
   npm run build
   ```

## Supported Game Elements

The toolkit currently supports creating and managing:

- **Mod Information** - Basic mod metadata
- **Civilizations** - Full civilization definitions with traits and abilities
- **Leaders** - Leader definitions and civilization associations
- **Units** - Custom and replacement units
- **Buildings** - Standard buildings and improvements
- **Unique Quarters** - Special district types
- **Traditions** - Custom player bonuses
- **Progression Trees** - Technology and civic advancement trees
- **Game Effects** - Custom game modifiers and effects
- **Icons and Assets** - Import and reference custom images
- **SQL Files** - Import custom SQL for advanced modifications

## Basic Usage Pattern

The toolkit follows a builder pattern workflow:

1. Create a `Mod` instance
2. Define game elements using builder classes
3. Add elements to the mod
4. Build the mod to generate files

For detailed examples of how to create specific elements, see the [How-To Guide](/guides/typescript/howto/index.md).

## Examples Directory

The toolkit includes several example files demonstrating various features:

- [Civilization Creation](https://github.com/izica/civ7-modding-tools/blob/main/examples/civilization.ts)
- [Unit Creation](https://github.com/izica/civ7-modding-tools/blob/main/examples/unit.ts) 
- [SQL File Import](https://github.com/izica/civ7-modding-tools/blob/main/examples/import-sql-file.ts)
- [Custom Icon Import](https://github.com/izica/civ7-modding-tools/blob/main/examples/import-custom-icon.ts)
- [Progression Tree Creation](https://github.com/izica/civ7-modding-tools/blob/main/examples/progression-tree.ts)
- [Unique Quarter Creation](https://github.com/izica/civ7-modding-tools/blob/main/examples/unique-quarter.ts)

## Current Limitations

As of March 2025, the toolkit covers approximately 60-80% of modding use cases. Areas not yet supported include:

- **Advanced Visual Assets** - Creating new 3D models or animations
- **UI Modding** - Custom interface elements and screens
- **Map Scripts** - Custom map generation algorithms
- **Great People** - Custom great people definitions (in development)
- **AI Behavior** - Detailed AI personality adjustments
- **Wonders** - Wonder definitions (planned)
- **Unit Abilities** - Detailed unit ability configurations (planned)
- **Scenarios** - Custom scenario creation and management

## Troubleshooting

### Common Issues

- **TypeScript Errors**: Ensure you have the correct version of TypeScript installed (`npm install typescript@latest`)
- **Missing Icons**: Verify file paths to custom icons are correct relative to your build script
- **Build Failures**: Check the console output for specific error messages
- **In-Game Issues**: Look for mod loading errors in the game's log files at `<GAME_DATA>/Logs/modding.log`

### Getting Help

For assistance with the toolkit:
- Post in the [CivFanatics Forum Thread](https://forums.civfanatics.com/threads/modding-tools-framework-written-on-typescript.696255/)
- Open an issue on the [GitHub repository](https://github.com/izica/civ7-modding-tools)

## Integration with Other Modding Techniques

The TypeScript toolkit can be used alongside other modding approaches:

1. **Generate base files** with the toolkit
2. **Manually refine** specific XML files if needed
3. **Package the results** for distribution

This hybrid approach allows for both the efficiency of the toolkit and the precision of manual editing when required.

## Future Development

According to the toolkit's roadmap, planned features include:

- [ ] Great People nodes and builder
- [ ] AI nodes and builder
- [ ] Additional localization support
- [ ] Unit abilities nodes and builder
- [ ] Wonder nodes and builder

## Conclusion

The Civilization VII TypeScript Modding Tools represent a significant step forward in streamlining the modding process. By leveraging modern web development practices, they reduce the complexity of creating mods while maintaining the flexibility that advanced modders expect.

For modders familiar with TypeScript or JavaScript, this toolkit provides an accessible entry point into Civilization VII modding. Even those new to these technologies will likely find the declarative approach more intuitive than direct XML editing.

## Further Reading

- [Modding Architecture Overview](/guides/modding-architecture.md)
- [Database Schemas Reference](/guides/database-schemas.md)
- [General: Creating New Civilizations](/guides/general-creating-civilizations.md)
- [General: Creating New Leaders](/guides/general-creating-leaders.md)
- [General: Modifying Existing Content](/guides/general-modifying-content.md)
- [TypeScript Technical Implementation](/guides/typescript/typescript-technical.md)
- [TypeScript How-To Guides](/guides/typescript/howto/index.md) 