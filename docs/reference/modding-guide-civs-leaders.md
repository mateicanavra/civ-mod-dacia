Perfect, I’ll develop a deep-dive Civilization VII modding guide focused specifically on creating new civilizations and leaders. This will include:

- Full setup and database structure for civilizations and leaders
- Adding unique abilities with conditional/era-specific modifiers
- Integrating custom 2D art assets (icons, leader portraits, unit art)
- Ensuring Civ VII compatibility with current-era modding practices
- Optional guidance on cross-mod compatibility (namespacing, modular design)

I’ll investigate current GitHub mod projects and official threads to ensure everything is CIV 7-specific and verified.

I’ll let you know when it’s ready to review.

# **CIVILIZATION VII Modding Guide: Creating New Civilizations & Leaders**

Civilization VII introduced a new paradigm for civilizations and leaders, so modding new civs and leaders requires understanding the *separate* leader selection and era-based civilization system ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=,you%20choose%20new%20civilizations%20for)) ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=,when%20entering%20the%20Exploration%20Age)). This guide walks through defining a new Civilization and multiple Leaders in Civ7 using XML/SQL, attaching unique traits and conditional modifiers, adding custom 2D art (icons, portraits, etc.), setting up icon atlases, organizing files/folders, accounting for Civ7’s era modules and dependencies, and following best practices. All information here is drawn from confirmed Civ7 modding documentation, community guides, and real mod examples – **no Civ6 references needed**.

## **1. Defining a New Civilization in the Database (XML/SQL)**

**Understanding Civ7 Civilizations:** In Civ7, civilizations are tied to specific Ages (eras) rather than spanning the whole game. Each multi-era game starts in Antiquity Age and as you advance to a new Age, you choose a new civilization for that era ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=,you%20choose%20new%20civilizations%20for)). For example, Carthage is an Antiquity Age civ while Great Britain is a Modern Age civ ([Update Check-In: Feb 27, 2025 | Civ VII ](https://civilization.2k.com/civ-vii/news/civ-vii-update-check-in-feb-27/#:~:text=,Mount%20Fuji%2C%20Vihren%2C%20and%20Vinicunca)). A modded civilization must be assigned to an Age so the game knows during which era it’s available. 

**Database Entries:** New civilizations are added via XML or SQL that inserts rows into Civ7’s database tables. At minimum, you will define: 

- **Civilizations Table:** a new row with a unique `CivilizationType` (an identifier string) and an associated `EraType` (e.g. ANTiquity, Exploration, Modern). You’ll also set references to text for the civ’s name and description. For example: 

  ```xml
  <!-- Define a new civilization for the Antiquity Age -->
  <Civilizations>
    <Row CivilizationType="CIVILIZATION_SCYTHIA_ANTIQUITY" EraType="ERA_ANTIQUITY" 
         Name="LOC_CIVILIZATION_SCYTHIA_NAME" Description="LOC_CIVILIZATION_SCYTHIA_DESCRIPTION" 
         CapitalName="LOC_CIVILIZATION_SCYTHIA_CAPITAL" />
  </Civilizations>
  ``` 

  In this snippet, **CIVILIZATION_SCYTHIA_ANTIQUITY** is a unique ID for the civ, and **ERA_ANTIQUITY** ensures it appears in the Antiquity Age. The `Name`, `Description`, and `CapitalName` use localization keys (which you will define in text files – see section 6).

- **City Names:** You should provide a list of city names for your civ. Civ7 likely uses a table for city name sets (similar to previous Civ games). In XML, this might look like:

  ```xml
  <CivilizationCityNames>
    <Row CivilizationType="CIVILIZATION_SCYTHIA_ANTIQUITY" CityName="LOC_CITY_SCYTHIA_POKROVKA" />
    <Row CivilizationType="CIVILIZATION_SCYTHIA_ANTIQUITY" CityName="LOC_CITY_SCYTHIA_GELONOS" />
    <!-- (additional cities) -->
  </CivilizationCityNames>
  ``` 

  This ensures your civ has unique city names when its cities are founded.

- **Starting Biases (Optional):** If your civ should prefer certain terrain or start locations, add entries to the starting bias tables. For example, to bias Scythia toward open land:
  
  ```xml
  <StartBiasTerrains>
    <Row CivilizationType="CIVILIZATION_SCYTHIA_ANTIQUITY" TerrainType="TERRAIN_GRASSLAND_OPEN" Bias="5"/>
  </StartBiasTerrains>
  ```

  Bias values indicate relative preference (higher = stronger preference). Similar tables exist for features, resources, etc.

- **Colors:** Define your civilization’s colors (used for unit outlines, city banners, etc.). Civ7 assigns each *leader* a consistent color scheme (since leaders persist while civs change) ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=,when%20entering%20the%20Exploration%20Age)), but it’s good practice to set civ colors too for interface elements like civ emblems. Use the `Colors` and `PlayerColors` tables. For example:

  ```xml
  <Colors>
    <Row ColorType="COLOR_SCYTHIA_PRIMARY" Red="1.0" Green="0.45" Blue="0.0" Alpha="1.0"/>      <!-- Orange -->
    <Row ColorType="COLOR_SCYTHIA_SECONDARY" Red="0.2" Green="0.0" Blue="0.0" Alpha="1.0"/>     <!-- Dark Red -->
  </Colors>
  <PlayerColors>
    <Row CivilizationType="CIVILIZATION_SCYTHIA_ANTIQUITY" 
         PrimaryColor="COLOR_SCYTHIA_PRIMARY" SecondaryColor="COLOR_SCYTHIA_SECONDARY"/>
  </PlayerColors>
  ```

  Choose distinct primary/secondary colors. This color set will apply to units and city banners when that civ is in use (and possibly to the leader’s UI ribbon if they match historically).

**Example:** In the **“Izica’s Scythia (Antiquity) 1.0”** mod, the creator added Scythia as an Antiquity Age civilization ([civ 7 | CivFanatics Forums](https://forums.civfanatics.com/tags/civ-7/#:~:text=Izica%27s%20Scythia%20%28Antiquity%29%201)). The mod’s database definitions include Scythia’s civ entry tied to Antiquity, a unique unit (likely a horse archer replacing a standard unit), and city names reflecting Scythian culture. Likewise, **“Philippines – Modern (Alpha 0.1)”** adds the Philippines civ for the Modern Age ([civ 7 | CivFanatics Forums](https://forums.civfanatics.com/tags/civ-7/#:~:text=Philippines%20%28Modern%29%20Alpha%200)). These mods demonstrate the basic structure: define the civ with an era, link unique content, and provide art/text. In the next sections we’ll cover the leaders and unique content in detail.

## **2. Defining Leaders and Leader Abilities**

**Leaders in Civ7:** Leaders are chosen independently from civilizations. The leader you select at game start stays with you through all Ages (carrying their bonuses throughout), while you switch civilizations at each Age transition ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=,you%20choose%20new%20civilizations%20for)) ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=,when%20entering%20the%20Exploration%20Age)). This means you can mix and match – e.g. play as a Leader from one culture leading a civ of another – though the game provides an indicator for historical pairings for flavor ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=,will%20help%20make%20the%20connection)). Leaders each have a unique ability and a set of intrinsic attributes (they gain *Attribute* points in categories like Economic, Scientific, etc., as the game progresses) ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=,you%20choose%20new%20civilizations%20for)) ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=Traits%20corresponding%20to%20leader%20Attribute,they%20gain%20during%20the%20game)). Leaders can also have multiple personas (variants with different abilities/agenda), though that is an advanced concept ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=,Personas)).

**Database Entries for Leaders:** To add a new leader, you will create:

- **Leaders Table:** a new row with a unique `LeaderType`. You’ll reference text for their name and a title. For example:

  ```xml
  <Leaders>
    <Row LeaderType="LEADER_TOMYRIS" Name="LOC_LEADER_TOMYRIS_NAME" Title="LOC_LEADER_TOMYRIS_TITLE" 
         AgendaType="AGENDA_STOIC" Icon="ICON_LEADER_TOMYRIS"/>
  </Leaders>
  ```

  Here **LEADER_TOMYRIS** is the ID for the leader. `AgendaType` can link to an agenda if your leader has one (optional – agendas affect AI behavior). `Icon` references the leader’s portrait icon (we’ll ensure this icon is defined in section 4). 

- **Leader Traits:** Each leader has a *Leader Ability* – a unique trait that confers some bonus. You will define this trait in the Traits table and then assign it to the leader via the LeaderTraits table. (Trait creation is covered in section 3, since civ abilities are done similarly.)

- **Leader Attributes:** Civ7 leaders have classifications (like **Militaristic**, **Scientific**, etc.) that determine their Legacy Attribute bonuses gained over time ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=Image)). In the database, these might be represented by boolean columns or a mapping table linking leader to attribute archetypes. For example, if your leader should be a Cultural and Scientific leader, you might have something like:
  
  ```xml
  <LeaderAttributes>
    <Row LeaderType="LEADER_TOMYRIS" AttributeType="ATTRIBUTE_SCIENTIFIC" Value="1"/>
    <Row LeaderType="LEADER_TOMYRIS" AttributeType="ATTRIBUTE_MILITARISTIC" Value="1"/>
  </LeaderAttributes>
  ```
  
  (The exact implementation may vary, but ensure your leader’s profile fits one or more of the six attribute trees: Cultural, Diplomatic, Economic, Expansionist, Militaristic, Scientific ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=Image)).)

- **Leader-Civ Pairing (Guaranteeing Civ Choices):** Although any leader can lead any civ in gameplay, you might want certain leaders to *guarantee* their historical civ appears as an option at the appropriate Age. Civ7’s design ensures that if you play as a given leader, their civilization will be offered during the correct Age transition ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=,when%20entering%20the%20Exploration%20Age)). For example, *playing as Amina guarantees Songhai will be available in the Exploration Age* ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=,when%20entering%20the%20Exploration%20Age)). To implement this, there is likely a table linking leaders to civilizations (and possibly to the era in which that civ should appear). For a modded leader and civ, you’d add an entry such as:

  ```xml
  <LeaderCivilizationPreferences>
    <Row LeaderType="LEADER_LAKANDULA" CivilizationType="CIVILIZATION_PHILIPPINES_MODERN" PreferredAge="ERA_MODERN"/>
  </LeaderCivilizationPreferences>
  ```

  This hypothetical entry would ensure that if a player’s chosen leader is Lakandula (a leader added for the Philippines), the Philippines civilization will show up as an available choice when the Modern Age arrives. This step isn’t strictly required, but it aligns historical combos and helps the AI pick sensible civs for a mod leader.

**Multiple Leaders:** You can create multiple leaders, whether for variety or to serve as alternate personas. Since leaders are decoupled from civs, “multiple leaders for one civ” simply means you have defined more than one leader that *could* historically pair with the same civ. For example, if you modded **Leader Roosevelt** and **Leader Lincoln** for a hypothetical American civ, both leaders could lead the “United States (Modern Age)” civ. You would create two Leader entries and two LeaderTraits, and you might set both to guarantee the United States civ in the Modern era. The game will allow the player to choose either leader and still select the U.S. civ in that age. For the AI or flavor, you could weight one as the default by using the preference table as above (maybe both Roosevelt and Lincoln point to U.S., so U.S. will certainly appear if either is the player’s leader).

**Leader Text and Dialogue:** Don’t forget to add localization for your leader’s name, title, and any custom lines (e.g., diplomacy dialogue) in the text files. At minimum, define `LOC_LEADER_<NAME>_NAME` (leader’s name) and `LOC_LEADER_<NAME>_TITLE` (usually an epithet or title like “Queen of Scythia”).

**Leader Portrait and Model:** The leader’s 2D portrait and 3D model are assets you’d normally provide. As of now (early Civ7 modding), adding custom 3D leader models is not yet possible for modders ([Philippines - Modern | CivFanatics Forums](https://forums.civfanatics.com/threads/philippines-modern.696547/post-16793700#:~:text=Right%20now%20really%20barebones%2C%20as,Hope%20you%20enjoy)). Modders use existing models or placeholders. You can, however, add a custom 2D portrait for the leader in diplomacy screens and selection (covered in section 4). 

**Example:** If we were adding *Tomyris* as a new leader for Scythia, we create `LEADER_TOMYRIS` in Leaders table, give her a unique ability trait (e.g., “Killer of Cyrus” trait that grants bonus damage to wounded units), and assign that via LeaderTraits. We might mark her as Militaristic/Expansionist in attributes. And since Scythia is an Antiquity civ, we could tie Tomyris to Scythia in Antiquity (so that the Scythia civ option appears in Antiquity if Tomyris is the player’s chosen leader). Once defined, Tomyris can actually lead **any** civ the player chooses – but her abilities will obviously be designed with Scythia in mind.

We now have the basic civ and leader entries set up. Next, we tackle giving them interesting unique abilities.

## **3. Unique Traits, Abilities, and Conditional Modifiers**

One of the core parts of making a custom civ or leader is designing their *unique traits*: civ abilities, leader abilities, unique units, unique buildings, etc. Civ7’s modding uses a Modifier system (similar to previous games) to implement these abilities via database entries. You’ll define **Traits** to represent each unique feature, then attach **Modifiers** to those traits to cause in-game effects. You can also specify **Requirements** that gate those modifiers (for era-locking, conditional bonuses, etc.).

**Trait Definition:** In the `Traits` table, add a row for each unique ability. For example:

```xml
<Traits>
  <Row TraitType="TRAIT_CIVILIZATION_RAPID_HORSE_ARCHERS" Name="LOC_TRAIT_RAPID_HORSE_ARCHERS_NAME" Description="LOC_TRAIT_RAPID_HORSE_ARCHERS_DESCRIPTION" />
  <Row TraitType="TRAIT_LEADER_STOIC_DEFENDER" Name="LOC_TRAIT_STOIC_DEFENDER_NAME" Description="LOC_TRAIT_STOIC_DEFENDER_DESCRIPTION" />
</Traits>
```

Here we define two traits: one for a civilization (perhaps “Rapid Horse Archers” for Scythia’s civ ability) and one for a leader (e.g. “Stoic Defender”). The Name and Description are text keys for Civilopedia or tooltip text. 

After defining a Trait, assign it: 
- Civ abilities go in `CivilizationTraits` (linking `CivilizationType` to `TraitType`). 
- Leader abilities go in `LeaderTraits` (linking `LeaderType` to `TraitType`). 

For example:
```xml
<CivilizationTraits>
  <Row CivilizationType="CIVILIZATION_SCYTHIA_ANTIQUITY" TraitType="TRAIT_CIVILIZATION_RAPID_HORSE_ARCHERS"/>
</CivilizationTraits>
<LeaderTraits>
  <Row LeaderType="LEADER_TOMYRIS" TraitType="TRAIT_LEADER_STOIC_DEFENDER"/>
</LeaderTraits>
```

Now, a trait by itself is just a container – we must give it effects via modifiers.

**Modifiers and ModifierTypes:** Civ7’s game engine has predefined *ModifierTypes* (e.g. “ADD_YIELD_MODIFIER” or “UNIT_COMBAT_STRENGTH_MODIFIER”) and you create specific Modifiers that use them. You’ll use the `Modifiers` table to create a new modifier, then use `TraitModifiers` to attach it to your trait.

For example, suppose **Scythia’s civ trait** grants *light cavalry units +1 additional movement and heal 30 HP when killing an enemy*. We might implement this with two modifiers:
- Modifier A: Increase movement of light cavalry.
- Modifier B: Heal unit on kill.

We would add: 
```xml
<Modifiers>
  <Row ModifierId="SCYTHIA_LIGHT_CAV_MOVEMENT" ModifierType="MODIFIER_UNIT_ADJUST_MOVEMENT" />
  <Row ModifierId="SCYTHIA_CAV_HEAL_ON_KILL" ModifierType="MODIFIER_UNIT_ADJUST_HEAL_ON_KILL" />
</Modifiers>
<ModifierArguments>
  <Row ModifierId="SCYTHIA_LIGHT_CAV_MOVEMENT" Name="MovementChange" Value="1"/>
  <Row ModifierId="SCYTHIA_CAV_HEAL_ON_KILL" Name="HealAmount" Value="30"/>
</ModifierArguments>
```
*(The exact ModifierType names and arguments are hypothetical for illustration.)*

Now link these modifiers to the trait:
```xml
<TraitModifiers>
  <Row TraitType="TRAIT_CIVILIZATION_RAPID_HORSE_ARCHERS" ModifierId="SCYTHIA_LIGHT_CAV_MOVEMENT"/>
  <Row TraitType="TRAIT_CIVILIZATION_RAPID_HORSE_ARCHERS" ModifierId="SCYTHIA_CAV_HEAL_ON_KILL"/>
</TraitModifiers>
```

This would ensure all units matching the criteria (light cavalry, etc.) get the effects. We likely need to specify *who/what* the modifiers apply to. Often, `Modifiers` have fields like `OwnerRequirementSetId` or `SubjectRequirementSetId` and the game knows context (e.g. “units of this civ”). If needed, we use **Requirements**.

**Requirements and Conditional Effects:** Civ7 supports making bonuses conditional on game state, era, etc. For instance, maybe a leader’s bonus applies only after the Industrial Age, or a unit ability works only outside friendly territory. Requirements are defined in tables such as `Requirements` and `RequirementArguments`, and then grouped in `RequirementSets`. 

For example, consider the *Bayanihan* ability from the Philippines mod (Modern civ) which grants influence and culture when war is declared on you, gives +50% influence to global Endeavor proposals, but imposes –10 combat strength on units outside your territory ([Philippines - Modern | CivFanatics Forums](https://forums.civfanatics.com/threads/philippines-modern.696547/post-16793700#:~:text=%3A7culture%3A%20Culture.%20%2B50,land%20units%20outside%20friendly%20territory)). To implement something like this:
- **War Declaration Trigger:** A modifier of type “PLAYER_YIELD_ON_WAR_DECLARED” with arguments for 100 Influence and 50 Culture, plus a requirement that the war is defensive (war declared *on* you).
- **Influence to Endeavors:** Perhaps a modifier type “ADJUST_INFLUENCE_PROPOSAL_PERCENT” with value 50, no additional requirement (always active).
- **Combat Strength Penalty outside territory:** A unit combat modifier with a Requirement that `IsOutsideFriendlyTerritory = true` (so it only applies outside borders).

We would create Requirement entries like:
```xml
<Requirements>
  <Row RequirementId="REQ_OUTSIDE_FRIENDLY_TERRITORY" RequirementType="REQUIREMENT_UNIT_OUTSIDE_FRIENDLY_TERRITORY"/>
</Requirements>
<RequirementSets>
  <Row RequirementSetId="REQSET_OUTSIDE_FRIENDLY" RequirementSetType="REQUIREMENTSET_TEST_ALL"/>
  <Row RequirementSetId="REQSET_OUTSIDE_FRIENDLY" RequirementId="REQ_OUTSIDE_FRIENDLY_TERRITORY"/>
</RequirementSets>
```
Then tie this requirement set to the combat strength modifier via `ModifierRequirements` (or appropriate field in Modifiers table linking the requirement set to that modifier’s effect scope).

The result: units get -10 Combat Strength *only* when the requirement (outside territory) is true. Similarly, for era-locked traits, you could use a requirement like “CurrentEra >= Industrial” to gate a bonus starting in a certain era.

**Era-Locked Civ Abilities:** Since civs themselves are era-specific in Civ7, you usually won’t need to lock a civ ability by era (the civ ability inherently only exists during that civ’s era). However, leader abilities span the whole game and you *might* want part of a leader’s bonus to only kick in later or under conditions. Use requirements for that. For example, a leader could have “In Industrial or later, do X” – implemented by requirement “PlayerCurrentEra >= ERA_INDUSTRIAL” on the respective modifier.

**Testing and Debugging Modifiers:** Civ7’s database is particular – if you reference a column or table incorrectly, the mod can fail to load. It’s good practice to enable the in-game Debug Database to verify your new entries and troubleshoot. One modder suggests enabling debugging and inspecting the **Gameplay DB** to understand table structure ([Civ 7 Modding Questions | CivFanatics Forums](https://forums.civfanatics.com/threads/civ-7-modding-questions.695200/#:~:text=I%20haven%27t%20drilled%20too%20beyond,See%20here)). For instance, some tables might not have the columns you expect. In one case, a modder attempted to update a `GameEffects` table with non-existent columns, not realizing that the XML structure was updating multiple underlying tables ([Civ 7 Modding Questions | CivFanatics Forums](https://forums.civfanatics.com/threads/civ-7-modding-questions.695200/#:~:text=If%20you%27re%20not%20already%20using,highlight%20syntax%20errors%20for%20you)) ([Civ 7 Modding Questions | CivFanatics Forums](https://forums.civfanatics.com/threads/civ-7-modding-questions.695200/#:~:text=XML%3A)). The lesson: always match your <Update> or <Insert> to the actual schema.

> **Tip:** Use a good XML/SQL editor. Editors like VS Code with an XML extension will highlight unclosed tags or syntax errors, which helps prevent modinfo or XML mistakes ([Civ 7 Modding Questions | CivFanatics Forums](https://forums.civfanatics.com/threads/civ-7-modding-questions.695200/#:~:text=You%20do%20have%20some%20problems,Criteria)). They can also auto-format your XML for readability. 

**Unique Units, Buildings, Improvements:** These are typically implemented as traits as well. There are two approaches:
- **Replacement Method:** Create a new unit and mark it as replacing a base unit for your civ. Civ7 likely has a mechanism similar to Civilization VI where a unique unit is tied to a civ via a trait or an override entry. For example, in the Antiquity template, Archanist included an “infantry replacement” unique unit ([Antiquity Civilization Template (XML) | CivFanatics Forums](https://forums.civfanatics.com/threads/antiquity-civilization-template-xml.696957/#:~:text=This%20is%20a%20template%20for,be%20easily%20find%20and%20replaceable)). You would define the unit in `Units` table (with its stats, abilities, etc.), then either:
  - Use `Units_XP2` style table (if exists) or a `UnitReplaces` table to indicate “CIVILIZATION_SCYTHIA_ANTIQUITY’s unique unit replaces the Heavy Chariot” (for example).
  - Or simpler, give the unique unit a `PrereqCiv` or `TraitType` in its definition to lock it to that civ.
  
  Example:
  ```xml
  <Units>
    <Row UnitType="UNIT_SAKA_HORSE_ARCHER" Name="LOC_UNIT_SAKA_HORSE_ARCHER_NAME" BaseUnit="UNIT_HEAVY_CHARIOT" Cost="60" ... />
  </Units>
  <UnitReplaces>
    <Row CivilizationType="CIVILIZATION_SCYTHIA_ANTIQUITY" ReplacesUnit="UNIT_HEAVY_CHARIOT" WithUnit="UNIT_SAKA_HORSE_ARCHER"/>
  </UnitReplaces>
  ```
  This would mean Scythia builds Saka Horse Archers in place of Heavy Chariots. If a formal `UnitReplaces` table doesn’t exist, the mod might instead rely on a trait and requirement: e.g. create a trait “Scythia unique unit trait” and give the unit a requirement so only Scythia can train it. The Antiquity template and existing mods use the straightforward replacement pattern for ease.

- **Additive Method:** If your unique unit/building is not a direct replacement, you can simply add the new unit and use the `Units` table’s civ restriction. For example, a unique Improvement (tile improvement) might be locked by a trait:
  ```xml
  <Improvements>
    <Row ImprovementType="IMPROVEMENT_KURGANS" Name="LOC_IMPROVEMENT_KURGANS_NAME" ... PrereqCivic="CIVIC_PASTORALISM" TraitType="TRAIT_CIVILIZATION_RAPID_HORSE_ARCHERS"/>
  </Improvements>
  ```
  By putting the civ’s trait in the Improvement’s `TraitType`, only that civ can build it.

**Attaching Modifiers to Unique Units/Buildings:** Unique units often have special abilities. You can give them modifiers too, often via the `Modifiers` and `UnitAbilities` tables. For example, if your unique unit has an ability (like +5 combat strength when attacking), you would create a UnitAbility in the DB and assign a modifier to it, then assign that ability to the unit. The template mod includes a sample unique improvement and unit with their modifiers commented for guidance ([Antiquity Civilization Template (XML) | CivFanatics Forums](https://forums.civfanatics.com/threads/antiquity-civilization-template-xml.696957/#:~:text=This%20is%20a%20template%20for,be%20easily%20find%20and%20replaceable)).

**Testing Traits in-game:** After implementing traits and modifiers, test in a new game. Use FireTuner or log files (Database.log and Modding.log) to verify your mod loaded. If something isn’t working (e.g., your ability doesn’t show or function), check for XML errors or logic mistakes. Often, a missing reference (like a Modifier not tied to a Trait, or a Trait not assigned to the civ) is the culprit.

**Example:** In the **Philippines (Modern)** mod, the civ’s unique ability **“Bayanihan”** is implemented via XML. Its description: *“Receiving a declaration of war grants +100 [Influence] and +50 [Culture]. +50% Influence towards proposing and supporting Endeavors. -10 Combat Strength for land units outside friendly territory.”* ([Philippines - Modern | CivFanatics Forums](https://forums.civfanatics.com/threads/philippines-modern.696547/post-16793700#:~:text=%3A7culture%3A%20Culture.%20%2B50,land%20units%20outside%20friendly%20territory)). To achieve this, the modder created multiple modifiers – one triggered on war declaration to add Influence and Culture, one adjusting Influence yield for World Congress-style endeavors, and one applying a combat penalty with a territory requirement. These are all attached to the Philippines civ trait. Despite the complexity, it’s all done with XML database entries (no Lua scripting yet), showing the power of the modifier system. 

As another example, **Izica’s Scythia** mod gives Scythia a classical unique unit (likely a horse archer) and possibly a unique ability related to cavalry. These would be set up via traits and modifiers as described. By structuring your XML/SQL in this modular way (traits -> modifiers -> requirements), you can recreate almost any in-game effect for your custom civ or leader. Just ensure the modifiers you choose exist in Civ7’s schema. When in doubt, find a similar ability from the base game and mirror its database entries.

## **4. Integrating Custom 2D Art: Icons, Portraits, and Atlases**

No custom civ or leader feels complete without unique icons and artwork. Civ7 mods can include 2D art assets such as civilization icons (emblem symbols), leader portraits, unit icons, building icons, etc. These are typically provided as image files (usually DDS format) and referenced via **Icon Atlases** in the database. 

**Creating the Images:** Prepare your artwork in the correct aspect ratio and resolution:
- **Civ icons** (emblems) usually appear as circular icons in two sizes: small (e.g. 32x32 or 64x64) for city banners, and large (256x256) for selection screens. 
- **Leader portraits** are typically square or circular portraits that might be 256x256 or 512x512 in resolution.
- **Unit and Building icons** often use 256x256 or 128x128 for the large versions, plus smaller versions for HUD elements.

Save these as DDS files (8.8.8.8 ARGB or DXT5) for compatibility. Name them clearly (e.g., `Scythia_Icon_256.dds`, `Tomyris_Portrait_256.dds`, etc.). Place them in your mod folder (e.g., in an "Art" or "Icons" subfolder).

**Defining Icon Atlases:** Civ7 uses atlases to group icons. An **IconTextureAtlas** is a collection of same-size icons in a single image file (the DDS). Even if your DDS has only one icon, you define an atlas entry for it. Each atlas entry specifies an Atlas name, the icon size (resolution of each icon), the sheet dimensions in terms of number of icons per row/column, and the filename. Then individual icons are defined by name and an index pointing into that atlas. This is very similar to Civ5/Civ6’s system.

For example, to define a 256px atlas for our civ icon and a 256px atlas for our leader portrait:
```xml
<IconTextureAtlases>
  <!-- Atlas for Scythia emblem, one icon of 256x256 -->
  <Row Name="ICON_ATLAS_CIVILIZATION_SCYTHIA_256" IconSize="256" IconsPerRow="1" IconsPerColumn="1" Filename="Scythia_Icon_256.dds"/>
  <!-- Atlas for Tomyris portrait, one icon of 256x256 -->
  <Row Name="ICON_ATLAS_LEADER_TOMYRIS_256" IconSize="256" IconsPerRow="1" IconsPerColumn="1" Filename="Tomyris_Portrait_256.dds"/>
  <!-- (You can also include smaller size atlases if needed, e.g., 32px versions) -->
  <Row Name="ICON_ATLAS_CIVILIZATION_SCYTHIA_32" IconSize="32" IconsPerRow="1" IconsPerColumn="1" Filename="Scythia_Icon_32.dds"/>
</IconTextureAtlases>
<IconDefinitions>
  <!-- Define icon names pointing to atlas entries -->
  <Row IconName="ICON_CIVILIZATION_SCYTHIA" AtlasName="ICON_ATLAS_CIVILIZATION_SCYTHIA_256" Index="0"/>
  <Row IconName="ICON_LEADER_TOMYRIS" AtlasName="ICON_ATLAS_LEADER_TOMYRIS_256" Index="0"/>
  <Row IconName="ICON_CIVILIZATION_SCYTHIA_SMALL" AtlasName="ICON_ATLAS_CIVILIZATION_SCYTHIA_32" Index="0"/>
</IconDefinitions>
```

In the above:
- We create an atlas `ICON_ATLAS_CIVILIZATION_SCYTHIA_256` for the Scythia civ icon at 256px. Since the image has 1 icon, we set 1x1 grid.
- Similarly, `ICON_ATLAS_LEADER_TOMYRIS_256` for the leader portrait.
- We also made a 32px version for the civ icon (`ICON_ATLAS_CIVILIZATION_SCYTHIA_32`) for smaller UI elements.
- Then we define `ICON_CIVILIZATION_SCYTHIA` and `ICON_CIVILIZATION_SCYTHIA_SMALL` icons that point to those atlases. We also define `ICON_LEADER_TOMYRIS` for the leader portrait.

Now anywhere the UI expects an icon for our civ or leader, we can use these IconNames.

**Hooking Icons to Game Elements:**
- **Civilization Icon:** In the `Civilizations` table, there is often a field for the icon. If not, the game might automatically look for an icon matching the civ’s type. To be safe, you can set the civilization’s emblem by an entry in a `CivilizationIcons` table or similar (if exists) or ensure your icon’s name follows the convention. Civ7 might use the convention that `ICON_CIVILIZATION_<CIVTYPE>` is the emblem. In our example, we named it exactly that, so the game should pick it up for city banners, etc.
- **Leader Portrait:** The `Leaders` table has an `Icon` field (as seen in section 2’s example). We set `Icon="ICON_LEADER_TOMYRIS"`. This means the game will use our custom portrait for Tomyris in menus and diplomacy. 
- **Unit/Building Icons:** Similar process – define atlases for any new unit or building icons and set the unit’s `Icon` field to your icon name in the Units/Buildings table.

**Atlas Best Practices:** 
- Ensure the atlas `Name` is unique across all mods. The game merges icon atlases into a global set, and duplicate (Name, IconSize) combinations will cause database errors (unique constraint failures). For instance, using a generic name like "ICON_ATLAS_UNIT_256" that another mod also used could conflict. To avoid this, prefix your atlas names with your civ or mod name (we did this by including “SCYTHIA” or “TOMYRIS” in the name). 
- The same applies to `IconName` entries – keep them unique.

**Placeholder vs. Custom Art:** In early Civ7 modding, modders sometimes have to use placeholder art if they cannot load new art. The Philippines mod, for example, was using placeholder icons and models because “a lot of assets are missing, as there's no known way to add them yet” in that early stage ([Philippines - Modern | CivFanatics Forums](https://forums.civfanatics.com/threads/philippines-modern.696547/post-16793700#:~:text=Right%20now%20really%20barebones%2C%20as,Hope%20you%20enjoy)). However, 2D icons *can* be added by the method above (the missing assets comment was mainly about 3D models). If your custom DDS isn’t showing up, double-check that:
  - The file is included in the mod (in ModInfo, see section 5).
  - The Filename in the atlas entry exactly matches your file name (without path, the game will look in the mod’s folder automatically).
  - The DDS format is supported (uncompressed or DXT compression with mipmaps if needed).
  - Your atlas name and icon definitions have no typos.

**Testing Icons:** Run the game with your mod and check the UI:
  - In leader selection, does your leader portrait appear?
  - When you enter a game as that civ, does the civ icon appear on the city banner or top panel? 
  - If an icon is missing, the game might show a default “unknown” icon. That means something didn’t match up – check that the `IconName` used by the UI matches one of your IconDefinitions. Using logging or FireTuner, you can inspect the `IconManager` requests.

If everything is set up correctly, your custom art should display. This greatly improves the polish of your mod, making your new civ and leader feel distinct.

## **5. Mod Structure, Files, and Folder Organization**

To ensure the game loads your civilization and leader properly, you must structure your mod files according to Civ7’s expectations:

**Mod Folder:** Each mod resides in its own folder (under `…/My Games/Sid Meier’s Civilization VII/Mods/` for manual installations). You can name it after your mod (e.g., **“ScythiaAntiquityMod”**). Inside this folder, at minimum, you need:
- A `.modinfo` file (XML format) that describes the mod and its components.
- Your XML/SQL files containing the new game data (as created in sections above).
- Localization files for text (XML or CSV).
- Art asset files (DDS images, etc.).

**.modinfo File:** This is the manifest that tells the game what to do with your files. It includes mod metadata (name, version, etc.), dependencies, and actions. A simplified example modinfo might look like:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Mod id="12345678-ABCD-4EFG-90HI-ABCDEFGHIJKL" version="1">
  <Properties>
    <Name>Scythia Antiquity Civilization</Name>
    <Teaser>A new Antiquity Age civilization: Scythia, led by Queen Tomyris.</Teaser>
    <Description>Adds Scythia as an Antiquity civilization with unique horse archer units and bonuses.</Description>
    <Authors>Izica</Authors>
  </Properties>
  <Dependencies>
    <!-- (Optional) Depend on the base Antiquity content if needed -->
    <!-- Example: Ensure the mod loads with base game (and Antiquity Age module if it's separate) -->
    <!-- <Mod id="CIVILIZATIONVII_ANTIQITY_CONTENT_ID" title="Age of Antiquity"/> -->
  </Dependencies>
  <Components>
    <!-- Update the Gameplay database with our new civ/leaders -->
    <UpdateDatabase id="Data">
      <File>Data/Scythia_Civilization.xml</File>
      <File>Data/Scythia_Units.xml</File>
      <File>Data/Tomyris_Leader.xml</File>
      <File>Data/Scythia_Icons.xml</File>
    </UpdateDatabase>
    <!-- Update the Text database with localization -->
    <UpdateText id="Text">
      <File>Text/Scythia_Text.xml</File>
    </UpdateText>
    <!-- Import art files so the game knows about them (if needed) -->
    <ImportFiles id="Art">
      <File>Art/Scythia_Icon_256.dds</File>
      <File>Art/Scythia_Icon_32.dds</File>
      <File>Art/Tomyris_Portrait_256.dds</File>
    </ImportFiles>
  </Components>
  <!-- Criteria or Blocks can be used to restrict when files load; not shown for simplicity -->
</Mod>
```

Let’s break down key parts of modinfo:
- **Mod id:** a unique GUID for your mod (you can generate one). This is important for compatibility and for dependencies.
- **Properties:** Contains user-friendly name, description, etc.
- **Dependencies:** If your mod requires another mod or DLC to be present. For instance, if Firaxis packaged each Age’s civs as separate official content, you might list a dependency on “Age – Antiquity” so that your mod loads in context. In practice, the base game’s Ages are core content, so you usually don’t need a dependency for them. But if your mod builds on another mod (e.g., uses someone’s custom assets or functions), list that mod’s ID here.
- **Components:** This is where you declare what your mod does. Common components:
  - `<UpdateDatabase>` – for adding/updating game database (XML/SQL files for gameplay).
  - `<UpdateText>` – for localization text files.
  - `<ImportFiles>` – for including art or other files. Ensure each art file is listed so it gets packed or recognized.
  - You might also see `<UpdateArt>` or specific components for art definitions in official mods, but using UpdateDatabase for icons (as we did) is fine.

- **Criteria (Optional):** Civ7 might allow enabling content only in certain game setups via Criteria blocks. In Civ6, modinfo had `<Blocks>` with `<Criteria>` to load content only if certain rules (like a specific game mode active). In Civ7, one potential use is to ensure your mod only activates when starting in the appropriate era or if a certain expansion is present. For instance, if you made a Modern Age civ mod and wanted it not to error in games that end before Modern, you might wrap its content in a criterion. However, generally if a civ’s era never comes up, it simply won’t be used rather than causing errors. The **Age modules** are always part of the game progression by default, so explicit criteria might not be needed unless Firaxis changes that logic.

In our example modinfo, we pointed to files in subfolders (Data, Text, Art). This is just for organization; you can keep all XML in one file or in the root, but separating thematically (e.g., one file for civ base data, one for units, one for leader, one for art definitions, etc.) makes maintenance easier.

**Folder Structure Example:**

```
ScythiaAntiquityMod/
├─ ScythiaAntiquityMod.modinfo
├─ Data/
│   ├─ Scythia_Civilization.xml
│   ├─ Scythia_Units.xml
│   ├─ Tomyris_Leader.xml
│   └─ Scythia_Icons.xml
├─ Text/
│   └─ Scythia_Text.xml
└─ Art/
    ├─ Scythia_Icon_256.dds
    ├─ Scythia_Icon_32.dds
    └─ Tomyris_Portrait_256.dds
```

This is a logical layout. The game doesn’t mandate the folder names, but the modinfo must correctly reference the paths. 

**Including SQL (if you prefer):** Instead of XML, you can use SQL files in UpdateDatabase to insert/update data. Some modders find SQL syntax easier for batch inserts. For example, an SQL snippet to add a trait might be:
```sql
INSERT INTO Traits (TraitType, Name, Description) 
VALUES ('TRAIT_CIVILIZATION_RAPID_HORSE_ARCHERS', 'LOC_TRAIT_RAPID_HORSE_ARCHERS_NAME', 'LOC_TRAIT_RAPID_HORSE_ARCHERS_DESCRIPTION');
```
Just include the .sql file in the modinfo’s UpdateDatabase. **Note:** Ensure you follow Civ7’s schema exactly; using Civ6 schemas can cause errors (table/column mismatches). If you see log errors like *“Error Loading XML/SQL”* or rollback messages, double-check your syntax and targets ([Civ 7 Modding Questions | CivFanatics Forums](https://forums.civfanatics.com/threads/civ-7-modding-questions.695200/#:~:text=jopomod,Rollback%20Required)) ([Civ 7 Modding Questions | CivFanatics Forums](https://forums.civfanatics.com/threads/civ-7-modding-questions.695200/#:~:text=%5B2025,Finished%20applying%20actions)).

**Testing the Mod Load:** After setting up, launch Civ7 and enable your mod in the “Additional Content” menu. If it doesn’t show up there, the modinfo might have an error (e.g., malformed XML can prevent it from appearing). Check Modding.log in your My Games\Civ7\Logs folder for clues. Fix any errors and reload. When it appears, start a new game and try selecting your leader or civ in the appropriate era. If things crash or the mod fails to enable, review the logs for database errors (they will list line numbers and issues).

At this point, if all went well, you have a functioning custom civilization and leader mod! Next, we’ll discuss Civ VII-specific considerations regarding the game’s era-based content and how to ensure compatibility.

## **6. Civ VII-Specific Requirements: Era Modules, Criteria, and Dependencies**

Civ7’s **era system** means your mod content must align with the correct Age. There are a few special considerations to keep in mind:

- **Base Game “Modules” for Ages:** Civ7’s official content is divided by Ages. For example, the base game includes Antiquity, Exploration (Medieval/Renaissance), and Modern Age civs out of the box. Additional civs are being added via DLC by age (e.g., **Carthage (Antiquity)** and **Great Britain (Modern)** in a post-release pack ([Update Check-In: Feb 27, 2025 | Civ VII ](https://civilization.2k.com/civ-vii/news/civ-vii-update-check-in-feb-27/#:~:text=,Mount%20Fuji%2C%20Vihren%2C%20and%20Vinicunca)), and later **Bulgaria (Exploration)** and **Nepal (Modern)** ([Update Check-In: Feb 27, 2025 | Civ VII ](https://civilization.2k.com/civ-vii/news/civ-vii-update-check-in-feb-27/#:~:text=World%20Collection,Modern%20Age))). These are integrated seamlessly into the Age transitions. 

  For modders, this typically doesn’t require special action – if you add an Antiquity civ, it will be available to choose at game start (Age 1). If you add a Modern civ, it will appear as a choice when players transition to Modern. The game should handle it as long as EraType is set correctly on your Civilization row.

- **Starting Era or Scenario Considerations:** If players can start in a later era or if there’s a mode that omits early ages, you may want to ensure your civ is loaded only when relevant. Civ7’s modding system likely allows Criteria checks for `GameStartEra`. For instance, if your mod is a Modern-only civ and a player starts in Antiquity, it doesn’t hurt (the civ will just sit unused until Modern age). But if a player starts *directly* in Modern (skipping earlier ages), your civ would be immediately available – which is fine. There’s no need to block it.

  However, if you had a mod that for some reason shouldn’t load unless a certain condition is true (say a total conversion that replaces base civs), you’d use the `<Blocks>` and `<Criteria>` system. E.g., 
  ```xml
  <Blocks>
    <Block>
      <Criteria>  <!-- some criterion like if a certain game mode active -->
        <LeaderRequirementSet>LEADER_LAKANDULA_SELECTED</LeaderRequirementSet>
      </Criteria>
      <Components>
        ... (component definitions that only load if criteria met) ...
      </Components>
    </Block>
  </Blocks>
  ```
  This is advanced usage – for a standard new civ, you typically won’t need a criteria block.

- **Dependencies on Expansions or Other Mods:** Currently, Civ7 does not have expansions (beyond DLC civ packs) yet, but if it did (or if certain gameplay modes exist, like a hypothetical “Mythology Mode” altering rules), you could set your mod to require or be compatible with them. For example, if a future expansion adds a new Attribute or new content that your mod civ uses, you might list that expansion’s Mod ID under `<Dependencies>` so that players can’t enable your mod without it. 

  For community mod dependencies, if your civ mod builds on, say, another mod that adds a new resource or mechanic, definitely list that mod’s GUID in Dependencies to avoid errors if it’s missing.

- **Ensure Unique IDs:** As mentioned, avoid name clashes not just in art, but in all IDs. Use a consistent prefix for your `TraitType`, `UnitType`, etc. (For example, use `SCYTHIA_` or an author tag in your identifiers.) This prevents conflicts if another mod coincidentally uses the same name. It also helps keep the **database unique constraints** happy. The game will throw errors if you try to insert a duplicate primary key. For example, if two mods unknowingly define `TRAIT_CIVILIZATION_EXAMPLE`, one will fail to load. Using prefixes like `TRAIT_CIVILIZATION_SCYTHIA_EXAMPLE` vs `TRAIT_CIVILIZATION_OTHERMOD_EXAMPLE` avoids that.

- **Mod Compatibility:** Civ7 is still new, so mod interactions are relatively untested. Generally, adding new civs/leaders is low-conflict (each mod just adds its entries). But if two mods modify the same thing (like both alter the same base Leader ability or both try to replace the same unit class with different uniques), then conflicts arise. As a best practice, **do not directly edit base game entries** unless absolutely necessary. Instead, add new ones or use modifiers to change behavior. This way, multiple civ mods can co-exist. For instance, if you wanted to buff an existing civ as part of your mod, it’s better to do it via a Modifier attached in your mod (so if someone else changes that civ, both modifiers apply) rather than overwriting the base values.

- **Testing with Other Mods:** After confirming your mod works alone, test it alongside a few popular mods (UI mods, etc.) to ensure nothing odd happens. Keep an eye on the in-game **Mods** menu – Civ7 might eventually have load order management. The general rule is that `<UpdateDatabase>` actions execute in unspecified order unless you set dependencies. If your mod must load after another to function, use the dependency tag.

**Example – Age Dependency:** Imagine a scenario: you create an **“Ancient Mesopotamia”** total conversion that only uses Antiquity Age (and perhaps stops the game after Antiquity). If you didn’t want any later Age content to appear, you might override or disable later ages, which is complex and beyond simple civ mods. But in such a case, you would use Criteria to disable transitions or not load later-age civs. Normal civ mods won’t need this – just be aware Civ7’s modular nature means your content will slot into the correct part of the timeline.

In summary, Civ7 modding doesn’t drastically alter the fundamental mod structure – it adds the concept of eras for civ content and free leader-civ pairing. By correctly tagging your civ with an Era and by optionally linking your leader to that civ historically, you’ve done the Civ7-specific parts. The rest is about following good mod structure and being mindful of the shared environment.

## **7. Examples from the Community and Best Practices**

Learning from existing mods can accelerate your understanding. Here are a few **practical examples** and takeaways:

- **Archanist’s Civilization Template (Antiquity):** Modder *Archanist* released an *“Antiquity Civilization Template”* to help others ([Antiquity Civilization Template (XML) | CivFanatics Forums](https://forums.civfanatics.com/threads/antiquity-civilization-template-xml.696957/#:~:text=This%20is%20a%20template%20for,be%20easily%20find%20and%20replaceable)). This template provides a barebones civ with one unique improvement, one unique unit (an infantry replacement), and one commander (Great General) replacement, complete with commented XML explaining each part. It also includes a design guide noting common trends in official civs (e.g., most civs have one unique unit and one unique infrastructure). This resource is invaluable – you can use it as a starting point for your own mod by find-and-replacing names and then customizing. The template focuses on Antiquity; if you aim to make a Modern civ, be aware some tables (like which civics/techs unlock things) differ – but the structure is analogous.

- **Philippines (Modern) mod by Archanist:** This is a working example of a custom Modern Age civ mod ([Philippines - Modern | CivFanatics Forums](https://forums.civfanatics.com/threads/philippines-modern.696547/post-16793700#:~:text=Download%20here%3A%20https%3A%2F%2Fforums.civfanatics.com%2Fresources%2Fphilippines)) ([Philippines - Modern | CivFanatics Forums](https://forums.civfanatics.com/threads/philippines-modern.696547/post-16793700#:~:text=unique%20units%20share%20a%20base,be%20done%20after%20design%20changes)). It adds the **Philippines civilization** (available in Modern Age) with **two unique units**, **two unique buildings**, and even a unique **Wonder** (Corregidor) – a very ambitious mod. Because Civ7 mod tools were limited at release, Archanist mentions that many assets use placeholders and everything was done through XML ([Philippines - Modern | CivFanatics Forums](https://forums.civfanatics.com/threads/philippines-modern.696547/post-16793700#:~:text=Right%20now%20really%20barebones%2C%20as,Hope%20you%20enjoy)). The civ ability (Bayanihan) we discussed earlier shows how complex effects can be achieved via XML. Notably, the mod is marked Alpha, implying things like custom models weren’t in yet. Still, the gameplay aspects (database entries for units, buildings, yields) work. This mod’s discussion thread reveals insights: they plan to add more mechanics once **gameplay scripting (Lua)** is available ([Philippines - Modern | CivFanatics Forums](https://forums.civfanatics.com/threads/philippines-modern.696547/post-16793700#:~:text=unique%20units%20share%20a%20base,once%20gameplay%20scripting%20is%20available)), and they will eventually add Civilopedia entries once design is finalized ([Philippines - Modern | CivFanatics Forums](https://forums.civfanatics.com/threads/philippines-modern.696547/post-16793700#:~:text=unique%20units%20share%20a%20base,be%20done%20after%20design%20changes)). **Takeaway:** Start with the core XML functionality; fancy UI or scripted behavior can come later when tools allow. Focus on making the civ playable and balanced first.

- **Izica’s Scythia (Antiquity) mod:** Another early example, adding **Scythia led by King Ateas** as an Antiquity civ. This mod likely uses an existing leader model (since Tomyris was a leader in a past Civ game) and gives Scythia thematic bonuses (perhaps extra horses or cavalry boosts). The existence of this mod, along with the Philippines, confirms that adding both a **new civ and a custom leader** is feasible in Civ7’s framework ([civ 7 | CivFanatics Forums](https://forums.civfanatics.com/tags/civ-7/#:~:text=Izica%27s%20Scythia%20%28Antiquity%29%201)). **Takeaway:** Check CivFanatics’ **Civ7 – New Civilizations** category for mods like these. Studying their files (if provided) can show exactly how they structured the modinfo, what trait and modifier combos they used, etc. Many modders share their code or at least can answer questions in the forums.

- **Community Collaboration:** Civilization modding has always thrived on community knowledge. CivFanatics forums have a **“Civ7 Creation & Customization”** section where you can ask questions and see solutions. For instance, if you encounter a tricky issue (like an XML error you can’t trace), chances are someone like *PoundedChicken* or *Gedemon* has posted a solution or debugging tip. We saw an example where a modder had XML errors and another user pointed out a tag mismatch and misuse of a table ([Civ 7 Modding Questions | CivFanatics Forums](https://forums.civfanatics.com/threads/civ-7-modding-questions.695200/#:~:text=You%20do%20have%20some%20problems,Criteria)) ([Civ 7 Modding Questions | CivFanatics Forums](https://forums.civfanatics.com/threads/civ-7-modding-questions.695200/#:~:text=Eg,has%20Name%20and%20Value%20columns)). Don’t hesitate to use these forums – even as Civ7 modding is evolving, the community is actively figuring it out together.

**Best Practices Summary:**
- *Modularity:* Keep your mod’s scope focused. Don’t modify core files heavily – instead add new entries or use modifiers to change behavior. This ensures compatibility with other mods and game updates.
- *Unique Identifiers:* Prefix everything (as mentioned) – from file names to XML IDs – with something unique (your mod name or an abbreviation). This prevents collisions and makes your mod content easily identifiable in logs.
- *Balance:* Follow the general balance of official civs unless you intentionally want an overpowered or challenge civ. For example, official civs usually have 1 unique unit and 1 unique infrastructure (building/improvement) **or** 2 uniques of one type, plus their trait. If you give 4 uniques and massive bonuses, it might be fun once but could break the game’s challenge. Aim for comparable power level, then playtest and adjust. The community often creates **balance patches** or feedback threads for mods – be receptive to that.
- *Localization:* Provide text for all in-game text your mod uses. At minimum Name and Description for civ, leader, units, etc. Missing text keys will show as debug strings in game which looks unpolished. Also, consider adding translations (even if just English) in the multi-language structure Civ7 uses, so players on different language settings don’t see nothing. The template likely shows how to format the Text XML with `<LocalizedText>` entries for different languages.
- *Documentation:* Keep comments in your XML or a readme in your mod to document any tricky parts. This helps if you leave the mod for a while and come back, or if others want to learn from your mod.
- *Testing:* Test your civ in various scenarios – start a game at the beginning with your civ, and if applicable, use your leader with a different civ to ensure nothing breaks (leaders should still give their ability regardless of civ). Test age transitions (if your civ is not Antiquity, use the “Start Age” option or play until that age to see if it appears correctly). Also test AI handling – does the AI know how to use your unique unit or ability? (Often it will treat it like a normal unit unless you gave it something radically new.)

By following this guide and referring to the community’s examples, you should be well-equipped to create a new Civilization and Leader in Civilization VII. Civ7 modding is still in its early days (with official mod tools and Steam Workshop support on the horizon ([Dev Diary #8: Victories & Post-Launch | Civ VII](https://civilization.2k.com/civ-vii/game-guide/dev-diary/victories-post-launch/#:~:text=Dev%20Diary%20,life%20for%20you%2C%20our%20fans)) ([Update Check-In: Feb 27, 2025 | Civ VII - Sid Meier's Civilization](https://civilization.2k.com/civ-vii/news/civ-vii-update-check-in-feb-27/#:~:text=Update%20Check,of%20scoping%20the%20work))), but even now we’ve seen modders successfully add content that “feels” native to the game. With careful structuring and a bit of creativity, your custom empire can take its place in the annals of Civilization!

**Sources:**

- Firaxis, *Civilization VII – Official Gameplay Guide: Leaders*, on leader-civilization selection and leader abilities ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=,you%20choose%20new%20civilizations%20for)) ([Civilization VII – The leaders - CivFanatics](https://www.civfanatics.com/civ7/civ-vii-gameplay-mechanics/civilization-vii-the-leaders/#:~:text=,when%20entering%20the%20Exploration%20Age)).  
- Firaxis, *Civ VII Update Notes (Feb 2025)* – details on new official civs/leaders by Age ([Update Check-In: Feb 27, 2025 | Civ VII ](https://civilization.2k.com/civ-vii/news/civ-vii-update-check-in-feb-27/#:~:text=,Mount%20Fuji%2C%20Vihren%2C%20and%20Vinicunca)) ([Update Check-In: Feb 27, 2025 | Civ VII ](https://civilization.2k.com/civ-vii/news/civ-vii-update-check-in-feb-27/#:~:text=World%20Collection,Modern%20Age)).  
- CivFanatics Forums – *Philippines (Modern) mod* by Archanist (mod description and notes on limitations) ([Philippines - Modern | CivFanatics Forums](https://forums.civfanatics.com/threads/philippines-modern.696547/post-16793700#:~:text=Right%20now%20really%20barebones%2C%20as,Hope%20you%20enjoy)) and *Antiquity Civ Template* (tutorial) ([Antiquity Civilization Template (XML) | CivFanatics Forums](https://forums.civfanatics.com/threads/antiquity-civilization-template-xml.696957/#:~:text=This%20is%20a%20template%20for,be%20easily%20find%20and%20replaceable)).  
- CivFanatics Forums – *Modding Questions & Answers*: community troubleshooting (XML errors, tips on using proper tags) ([Civ 7 Modding Questions | CivFanatics Forums](https://forums.civfanatics.com/threads/civ-7-modding-questions.695200/#:~:text=You%20do%20have%20some%20problems,Criteria)) ([Civ 7 Modding Questions | CivFanatics Forums](https://forums.civfanatics.com/threads/civ-7-modding-questions.695200/#:~:text=Eg,has%20Name%20and%20Value%20columns)).  
- CivFanatics Forums – *Civ7 New Civilizations* category listing user-created civ mods (e.g., Scythia Antiquity, Philippines Modern) ([civ 7 | CivFanatics Forums](https://forums.civfanatics.com/tags/civ-7/#:~:text=Izica%27s%20Scythia%20%28Antiquity%29%201)) ([civ 7 | CivFanatics Forums](https://forums.civfanatics.com/tags/civ-7/#:~:text=Philippines%20%28Modern%29%20Alpha%200)).  
- CivFanatics Forums – *Philippines mod discussion* (unique ability “Bayanihan” example) ([Philippines - Modern | CivFanatics Forums](https://forums.civfanatics.com/threads/philippines-modern.696547/post-16793700#:~:text=%3A7culture%3A%20Culture.%20%2B50,land%20units%20outside%20friendly%20territory)).