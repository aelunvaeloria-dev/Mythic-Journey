# Mythic Journey — Foundry VTT System Redesign

Mythic Journey is the controlled replacement for the frozen Ecclesia v0.1.102 rules engine. The repository currently contains both the unchanged legacy gameplay path and the schema-v2 compatibility shell that will receive the redesigned Narrative and Combat engines.

## Redesign status

- Phases 0–2 are closed; the foundational rules, growth model and complete owner mandate are approved.
- Phase 3 registers versioned Foundry `TypeDataModel` classes for v2 `character`/`npc` Actors and eight canonical Item types.
- Existing legacy documents remain loadable and unchanged. A document uses v2 only after explicit GM opt-in and a complete schema identity check.
- The public namespace is `game.mythicJourney`; no `game.ecclesia` alias is installed.
- The manifest targets Foundry v13 and v14. Live v13/v14 smoke evidence remains a separately tracked release obligation.
- Phase 3 defines storage, validation, authority, provenance and dispatch boundaries; later phases implement the new resolvers and user-facing workflows.

See [`programme/PHASE-3-SCHEMA-SPEC.md`](programme/PHASE-3-SCHEMA-SPEC.md) for the canonical schema contract and [`programme/README.md`](programme/README.md) for programme status.

## Frozen legacy changelog

The release notes below describe the retained Ecclesia v0.1.102 baseline. They are historical compatibility evidence, not the redesigned v2 rules contract.

## v0.1.102 — Post-Use Target Confirmation

- Clicking **Use** on a Single, Up To, or All Selected Technique/Spell now opens a non-modal canvas targeting panel instead of requiring targets in advance.
- Shows native Foundry target markers, a live selected-target list, the current count/maximum, explicit deselect controls, and Confirm/Cancel actions.
- Allows Up To abilities such as Split Pistols to confirm below their maximum, including one target out of a possible two.
- Rejects ineligible or over-limit selections immediately and keeps the panel open when range or line-of-sight validation fails.
- Starts each activation with a clean creature target set, restores the player's previous creature targets on cancellation, and retains confirmed targets visibly after resolution.
- Routes target-attached areas through the same post-use selector. Self, No Target, auras, and ordinary placed areas retain their automatic or placement-specific flows.
- Keeps target choices and boss-part choices ahead of the existing atomic action/resource spend, so cancellation never pays a cost.
- Adds live-session, execution, and presentation regressions; all 128 automated checks pass.

## v0.1.101 — Stable Area Exit Cleanup

- Ignores synthetic Actor/effect updates in the persistent-area movement hook, preventing exit cleanup from being mistaken for a fresh area entry.
- Keeps geometry updates authoritative, including token movement and footprint resizing, without allowing Blinded removal to reapply itself.
- Adds a regression for Foundry's nested `updateToken` event during synthetic-Actor condition removal.

## v0.1.100 — Immediate Area Boundary Detection

- Evaluates persistent-area membership against the authoritative destination coordinates supplied by Foundry's `updateToken` hook.
- Applies entry conditions on the first move into an area and removes them on the first move out, even while the rendered Token or Scene collection still exposes its previous position.
- Uses the same incoming coordinates when caster- or target-attached templates follow a moving token.

## v0.1.99 — Live Persistent-Area Lifecycle Repair

- Stores affected Active Effects as records instead of UUID-keyed objects, and decodes the nested flags produced by Foundry from older synthetic Actor UUIDs.
- Applies condition-bearing persistent areas on entry when exit cleanup is enabled, repairing the one-way `Immediate`-only lifecycle found in existing Smoke Bomb data.
- Expires round-based templates created by scene-less Foundry combats by locating scenes through each template's combat ownership flag.
- Repairs the stranded schema-8 `Independent / 1` stationary-area default to `Independent / 0` in Items and builder mirrors, with a runtime guard for unmigrated data.
- Advances the rules schema to version 9 and adds live-shaped regressions for scene-less expiry, synthetic Actor effect ownership, entry application, and repeated Smoke Bomb placement.

## v0.1.98 — Targeting Runtime Repair

- Persistent-area expiry is processed by the GM client that actually advances combat, rather than depending on a separately selected primary-GM session.
- Expiry is executed independently of start/end-turn trigger failures and is covered by a Foundry-shaped `updateCombat` lifecycle test.
- Adds a new `0.1.98-targeting-repair` migration that repairs stationary persistent areas already upgraded to schema 7 with the accidental `Replace / 1` default.
- Adds a runtime compatibility guard so an unmigrated schema-7 stationary area still creates an independent second copy.
- Condition checkboxes now submit through canonical Foundry Item paths, while the multi-page builder preserves conditions when the Conditions page is not currently rendered.
- Raises the rules schema to version 8 and passes 120 automated checks, including live hook and template-creation regression tests.

## v0.1.96 — Targeting and Persistent Areas

- Adds structured area targeting for circle, square, and line abilities, with diameter/edge/length/width controls, grid snapping, wall propagation, and token-centre or overlap inclusion.
- Adds ally, enemy, caster, unconscious, and no-target eligibility rules to canonical ability execution.
- Adds live pre-spend placement previews, automatic area membership, and cancellation without spending actions or resources.
- Adds instant and persistent areas with immediate, entry, exit, start-turn, and end-turn activation; fixed-round, next-turn, dismissed, and scene durations; and stationary, caster-following, or selected-target-following placement.
- Creates persistent areas as system-owned Measured Templates, tracks only the conditions they create, removes while-inside conditions correctly, and supports independent, replace, refresh, and strongest-copy stacking.
- Extends the Item sheet and Spell & Technique Builder with the full targeting contract, adds the additive `0.1.96-targeting-overhaul` migration, and raises the rules schema to version 6.
- Adds `TARGETING.md` and 110 passing automated checks covering geometry, filters, no-target execution, persistence contracts, and migration.

## v0.1.95 — Foundry 13.351 Movement Bypass Fix

- Stops treating Foundry's deprecated `DatabaseUpdateOperation.teleport` compatibility property as a voluntary-movement bypass. Ordinary drag and ruler movement can no longer skip Speed enforcement or ledger charging.
- Keeps bypasses explicit: the GM's wall-ignoring Unconstrained Movement mode, the optional GM-always-bypasses setting, or an Ecclesia forced/teleport API request.
- Correlates accepted and completed movements by token, with the accepted movement ID retained for idempotent charging even when Foundry reconstructs its post-update operation.
- Adds a direct origin-to-destination measurement fallback so an incomplete third-party movement record cannot become a zero-cost move.

## v0.1.94 — Movement Enforcement Hotfix

- Enforces a route against Foundry v13's authoritative pending square count before token movement begins, including multi-waypoint routes longer than the token's remaining Speed.
- Correctly charges completed movements when Foundry supplies the requesting User document, so later moves in the same turn use the reduced remaining budget.
- Limits the native unconstrained bypass check to actual wall-ignoring GM movement; ordinary GM movement is subject to the same budget as player movement.
- Replaces the duplicated Movement/Speed/Override summary with one concise Speed card and an optional override field.

## v0.1.93 — Integrated Range & Squares-per-Turn Movement

- Replaces the one-move-action limit with a per-token combat ledger: any number of moves may be made until the token has traversed its Speed in squares.
- Uses Foundry's completed movement measurement, native wall constraints, and native waypoint/pathfinding workflow instead of rerouting token updates through a second pathfinder.
- Leaves movement budgets unrestricted outside combat while retaining normal Foundry wall collision.
- Makes Foundry's GM Unconstrained Movement control bypass walls, turn order, and the Ecclesia movement budget; adds a GM token-HUD reset control and a forced/teleport movement API.
- Restricts player door interaction to one square from the exact current-turn token in combat or an owned controlled token outside combat.
- Centralizes range and footprint-aware line-of-sight validation before ability costs are spent.
- Adds structured movement effects to the Spell & Technique Builder for Dash-style immediate grants and Haste-style Speed bonuses, multipliers, set values, durations, recipients, and stacking.
- Migrates old Actor move-action fields and turns the former standalone range/movement module into a no-hook compatibility shim.

See `MOVEMENT.md` for rules semantics, authoring examples, and integration APIs.

## v0.1.92 — Action-First Combat Page

- Makes Actions the only top summary line so Techniques and Spells appear immediately below it.
- Moves Defenses and Resistances into a compact paired footer at the bottom of the Combat page.
- Keeps late Resistance-module injection isolated in the footer without disturbing the action-first layout.
- Shows each Technique's live resolved range on its compact card. Proper Weapon Use now visibly reflects the equipped weapon's Technique range rather than stale legacy Item text.
- Treats the canonical Paired weapon property as the `weapon-paired` compatibility tag, allowing it to satisfy Techniques that require Paired.

## v0.1.91 — Combat Summary Integration Fix

- Adds an explicit Resistance host to the Combat summary instead of relying on injected markup to become an implicit grid item.
- Normalizes late module injection so Resistance headings and pills cannot overlap Defenses.
- Keeps Actions beneath Defenses when Resistances are present, and beside Defenses when they are not.

## v0.1.90 — Stable Compact Character Sheets

- Keeps the Main page's Core Skills, Secondary Skills, and character detail rails in a persistent three-column structure at practical sheet widths.
- Keeps Techniques and Spells side by side; narrow windows preserve their relationship instead of stacking the lists.
- Makes action cards shorter by removing an internal information row and tightening vertical padding while restoring readable labels and controls.
- Rebuilds the Actions summary so Standard, Move, Speed, and Override remain aligned and legible.
- Adds a persistent Compact/Comfortable density control to the character header. Density changes sizing without changing the sheet's information architecture.
- Retains the clearer Technique, Spell, and Weapon summaries introduced by the visual overhaul.

## v0.1.89 — Compact Sheet Correction

- Restored the established compact Main-tab presentation without reverting the improved Combat action or Equipment designs.
- Returned Core Skills, Secondary Skills, Traits, Disciplines, Temporary Traits, and Bond Features to clear, space-efficient rows. Secondary Skill formulae are again kept inside their expanded details instead of competing with the skill name and score.
- Normalised resource card widths across classes: two-resource characters now use the same card and bar geometry as three-resource characters rather than stretching to fill the ribbon.
- Repacked Defences, Resistances, and Actions into the compact Combat summary area so Techniques and Spells appear substantially earlier.
- Reduced—but retained—the improved Technique, Spell, and Weapon card hierarchy, labels, status information, and named controls.
- Removed blank Weapon-grant controls and suppressed the separate Damage button for actions with no damage or ward profile.

## v0.1.88 — Visual System Overhaul

- Rebuilt the Character Combat tab around a compact, responsive hierarchy with labelled resources, defences, action economy, resistances, abilities, equipment, and consequences.
- Separated common play controls from edit/delete maintenance controls, removed empty resource and ability columns, and added usable small-window layouts.
- Replaced ambiguous icon-only equipment and ability actions with named controls and clearer status, cost, range, damage, and attachment information.
- Reworked combat chat cards for immediate outcome scanning, condition-only results, collapsible calculations, and GM-only undo controls without internal execution identifiers.
- Rebuilt the Rules manager as a high-contrast searchable catalogue with category counts, archived filtering, labelled metadata, and clear primary actions.
- Reorganised the Weapon editor into persistent disclosure sections with a visible quality budget, accessible checkboxes, labelled damage fields, and advanced settings kept out of the normal workflow.
- Replaced the accumulated stylesheet overrides with a unified responsive visual system, consistent typography and focus states, and reliable checkbox sizing.

## v0.1.87 — Phase 12 Release Cleanup

- Made the canonical class + equipped Weapon + ability resolver the only player-style execution path.
- Removed chat-roll guessing, button capture, non-d20 compatibility resolution, former module settings/library shims, former flag reads, old API aliases, duplicate catalogues, and the superseded Spell Library.
- Preserved separate custom execution for NPC, Hostile NPC, and Boss NPC abilities, plus the Conditions, Resistances, and Warder integrations.
- Added **Release Readiness** under Configure Settings. Its read-only audit blocks finalization for unresolved player data, invalid Builder records, migration failures, obsolete active modules, or failed system checks.
- Historical external-module flags and settings are reported but never interpreted, rewritten, or deleted automatically.
- Added the idempotent `0.1.87-release-readiness` migration, 86 automated checks, and a repeatable finalization fingerprint.

See `RELEASE-READINESS.md` for the complete audit, cleanup boundary, and release checklist.

## v0.1.86 — Phase 11 Advanced Weapon Functionality

- Added stable Unique Weapon definitions with one-per-Actor identity, complete player read-only protection, and explicit GM-owned Weapon review.
- Added reusable equip/use/conditional adaptive properties with execution, turn, scene, rest, and until-unequipped durations.
- Added Elemental Gloves as a complete equip-choice example: Fire, Lightning, Ice, Water, or Wind damage plus the matching Focus permission.
- Added level, GM-approval, prerequisite, override, and branching progression contracts. The five Lance lineages are present with progression deliberately inactive and no invented nodes.
- Added automatic grant adoption/creation, equipped-Weapon availability, stale-grant cleanup, and transfer/delete lifecycle handling.
- Added the **Ecclesia: Unique Weapons** compendium and idempotent `0.1.86-establish-advanced-weapons` migration. Automatic migration never rewrites Actors.

See `ADVANCED-WEAPONS.md` for the complete Phase 11 contract and live checks.

## v0.1.85 — Phase 10 Existing-World Migration

- Added the idempotent `0.1.85-migrate-existing-world-content` migration for recognised world Items and integrated Builder records.
- Added two generated world compendiums—**Ecclesia: Canonical Abilities** and **Ecclesia: Starting Weapons**—with class folders and stable canonical identities.
- Added a GM-only Actor migration review gate. No Actor is converted automatically; the GM must explicitly check each eligible player-style Actor.
- Converted selected Actor abilities in place while retaining Item IDs, narrative customisations, valid Core Skill 3 choices, external flags, and compact pre-migration backups.
- Added safe starting-Weapon inference and Focus adaptation for selected Actors that have no configured Weapon, including cross-class Spell damage types.
- Added explicit Boss custom-ability isolation: Boss inline and embedded abilities never require Class or Weapon attachment and continue using Boss flat-bonus/part-resource execution.
- NPC, Hostile NPC, Boss NPC, and all unchecked player-style Actors remain untouched by Actor conversion.
- See `WORLD-MIGRATION.md` for the complete safety and review contract.

## v0.1.84 — Phase 9 Canonical Content Conversion

- Established one runtime catalogue of 83 canonical abilities: the original 81, Split Pistols, and the new Union flight Spell.
- Corrected Twin Pistols and the Lance Sacred Weapon attacks to Techniques. The former Union attack retains its stable record ID as Sacred Assault.
- Added 34 configured starting Weapon presets, 25 canonical Weapon Types, 9 additional Weapon Tags, and 8 structured Weapon Properties.
- Updated the Charactermancer to grant the selected canonical abilities and one compatible, equipped starting Weapon. Twin Pistols also grants Split Pistols.
- Added five unique Lance Relic Weapons. Their attacks are linked as Weapon-granted Techniques; Sacred Mantle grants Sacred Assault and Union.
- Added explicit manual split-dice execution for Split Pistols without guessing the player's allocation.
- Added the idempotent `0.1.84-convert-built-in-content` migration. It seeds registries and world Weapon templates but deliberately does not rewrite existing Actors or owned Items; that remains Phase 10.
- See `CONTENT-CONVERSION.md` for the catalogue, migration boundary, and acceptance checks.

## v0.1.83 — Condition Application Hotfix

- Stopped the Phase 8 structured executor from calling the Conditions integration when an ability has no selected conditions.
- Condition application now requires both a non-empty snapshotted condition list and at least one eligible effect target.
- Added a second defensive check against the live Item for compatibility callers and older execution payloads.

## v0.1.82 — Weapon and Ability Damage Editor Hotfix

- Fixed Weapon Technique and Improvised profiles reverting visually and resolving as defaults when Foundry stored numbered form rows as indexed objects rather than arrays.
- Made canonical Weapon and Ability normalizers accept both arrays and Foundry's indexed-object representation while preserving numeric row order.
- Fixed Spell and Technique Builder damage rows writing to temporary UI aliases instead of the canonical Spell, bonus-damage, and replacement-damage paths.
- Restored editing, adding, and removing Spell components/options and Technique bonus/replacement components/options.
- Existing affected data is recovered dynamically; no migration or manual repair is required.

## v0.1.81 — Phase 8 Unified Attack and Damage Execution

- Added one canonical Use pipeline that snapshots the class, equipped Weapon, ability, targets, choices, formulas, roll modes, and costs before rolling.
- Added explicit self, single, up-to, all-selected, and area/manual target contracts; separate or shared attack rolls; and separate or shared typed damage components.
- Made costs revalidate and spend atomically after all pre-roll choices. Cancelling spends nothing, while misses retain their cost; GM Force Use bypasses attachment only.
- Damage and conditions now consume the same hit results: damage never applies on a miss, while non-damage effects may opt into effects-on-miss.
- Added direct no-defence resolution for no-roll abilities, healing, and Warder walls, plus structured boss-part, resistance, healing, and condition application.
- Replaced canonical multi-card use/damage output with one structured execution card and a guarded GM Undo operation for costs, Vitality, boss parts, walls, and created effects.
- Retained the Damage button and legacy/chat-detection automation as compatibility fallbacks through Phase 12.
- Added execution controls to the Technique/Spell Builder and direct Item editor. See `COMBAT-EXECUTION.md` for the complete contract.

## v0.1.80 — Phase 7 Builder Rendering Hotfix

- Fixed the actor-scoped Spell/Technique Builder failing to render because its Core Skill preview referenced a missing label helper.
- Added lifecycle coverage that instantiates the builder and renders Page 1, preventing another API-only test from missing this failure.

## v0.1.79 — Phase 7 Editors and Character Creation

- Reworked the integrated Technique/Spell Builder around the canonical ability resolver, with actor-scoped creation and in-place editing from character sheets.
- Made Core Skill 1 class-resolved and Core Skill 2 Weapon-resolved in every authoring UI; only Core Skill 3 is player-selected.
- Added live preview against any owned Weapon, searchable registry-backed All/Any Weapon Tags, and structured Technique/Spell damage and range profiles.
- Added blocking structural validation, non-blocking contextual compatibility warnings, duplicate-name choices, and optional independent personal-library copies.
- Updated the Charactermancer review and added creation-time normalization for generic Foundry and retained compatibility import paths.
- See `EDITOR-INTEGRATION.md` for the complete editor contract and acceptance boundary.

## v0.1.78 — Phase 6 Ability and Live Weapon Integration

- Converted all 81 canonical class abilities to the versioned ability/Weapon contract while preserving custom legacy abilities.
- Added Proper Weapon Use, Unconventional/Improvised Use, and explicit Independent Action dependency modes.
- Made equipped Weapon attachment, Core Skill 2, Technique damage/type/range, Focus permissions, scoped modifiers, and Weapon Properties resolve live on Actor sheets and rolls.
- Added structured Spell profiles, selectable and split damage handling, additional Weapon dice, deterministic modifier order, unavailable-row enforcement, and GM Force Use.
- Added a canonical Unarmed Style Weapon Type and world Weapon Item without automatically granting Weapons through the Charactermancer.
- Added the idempotent `0.1.78-integrate-abilities-and-live-weapon-rolls` migration and Phase 6 regression coverage.

## v0.1.77 — Phase 5 Item Sheet Hotfix

- Replaced Handlebars-incompatible numeric lookup paths in the GM Weapon override editor with named view fields, restoring Weapon Item sheet rendering in Foundry VTT.

## v0.1.76 — Phase 5 Weapon Chassis

- Added the GM-only Ecclesia Rules manager for Weapon Types, Weapon Tags, Focus Tags, and costed structured Weapon Properties.
- Added visible Improvised, Standard, Fine, Masterwork, and Relic quality bands with budgets `0 / 2 / 3 / 6 / unrestricted` and grey/white/blue/purple/gold sheet borders.
- Rebuilt the Weapon Item editor around Technique/improvised profiles, Focus, properties, direct modifiers, granted Item UUIDs, and collapsed GM overrides.
- Players may rename, view, equip, and use granted abilities, but cannot create Weapons or alter their mechanics and descriptions.
- Added mutually-exclusive live equip controls with one free change per turn and Standard Action spending for later changes.
- Added ability Weapon Tag/Focus selectors and live configured-Weapon attachment feedback without changing legacy attack resolution.
- Added migration `0.1.76-establish-weapon-chassis`; existing Weapons become Standard and remain marked Needs Configuration until reviewed by a GM.
- See `WEAPON-CHASSIS.md` for the complete contract.

This repository retains the working **Aelun Vaeloria: Ecclesia** v0.1.102 implementation as its compatibility baseline. Legacy-only document types continue to use `template.json`; the redesigned `character`, `npc`, `weapon`, `armor`, and `bond` shapes use dual `TypeDataModel` classes so old source fields and v2 blocks can coexist without cross-engine fallback.

## Installation

1. Extract the `mythic-journey` folder into your Foundry data directory:
   `FoundryVTT/Data/systems/mythic-journey`
2. Restart Foundry.
3. Create a disposable test world using the system **Mythic Journey**.

Combat automation and the Spell & Technique Builder are included in the system. Do not enable the former `ecclesia-combat-automation` or `ecclesia-spell-technique-builder` modules; v0.1.92 treats either active module as a release blocker. Historical external-module data is retained for backup purposes but is no longer read by gameplay code.

The system also omits the obsolete damage formula, impact, defense, and free-text property controls from the Weapon editor. Existing stored values are retained as historical data, while newly created Weapons contain only the canonical `weaponRules` chassis.

## Retained legacy compatibility features

### Integrated Combat and Content Tools

- Resistance-aware damage and healing automation, multi-target attacks, combat resource bars, action refresh, and rest/dawn resource workflows are part of the system.
- The Spell & Technique Builder, its shared/class/GM/user libraries, built-in class records, drag-and-drop actor import, and JSON/template import tools are part of the system.
- The retained tools are exposed beneath `game.mythicJourney`; they do not receive a legacy namespace alias.

### Canonical Weapon and Ability Rules

- A versioned Phase 3 contract separates Weapon Type applicability from Weapon Item performance.
- World registries provide stable, GM-managed Weapon Tags, Focus Tags, and Weapon Types.
- Pure resolvers handle dynamic Core Skill 2, all/any tag requirements, Focus coverage, weapon-granted abilities, Technique profiles, and equip-change costs.
- Phase 9 supplies 83 canonical abilities and a compatible starting Weapon for every Charactermancer class choice.
- The compatibility runtime is available at `game.mythicJourney.rules`; release auditing is available at `game.mythicJourney.rules.release`.
- Player-style Actors use only configured canonical abilities. NPC, Hostile NPC, and Boss NPC custom abilities remain on their separate custom rulesets.

### Canonical Class Contract

- Every character resolves exactly one of ten stable canonical class definitions.
- Each class owns one fixed Class Anchor used as Core Skill 1; classes do not restrict access to Techniques or Spells.
- Class identity synchronizes Heir/Scion role, while GM-only Actor overrides can replace the normal anchor for exceptional cases.
- Character Affinities are reserved as an inactive future extension point for branching specialisations.
- The compatibility class API is available at `game.mythicJourney.rules.classes`; see `CLASS-CONTRACT.md`.

### Guided Character Building

Character sheets open with a build guidance panel until marked complete.

Starting assumptions:

- Character Level starts at **1**.
- Technical Rank starts at **0**.
- Technical Rank maximum starts at **2**.
- Core Skill creation spread: one at 3, two at 2, four at 1, rest at 0.
- Players begin with **one Discipline**.

The sheet includes a build checker and a “Mark Build Complete” button.

### Core Skills

The sheet supports all 13 Core Skills:

- Intellect, Charm, Confidence, Heart, Awareness
- Strength, Precision, Stamina, Technique, Sturdiness
- Mana, Favour, Luck

### Disciplines

Disciplines are stored directly on the character sheet and can be added/removed by players.

### Secondary Skills

Secondary Skills can be created and removed within Foundry.

Each Secondary Skill can be set as either:

- **Narrative Dyad**: 4dF + two Core Skills + matching Discipline
- **Combat Triad**: d20 + three Core Skills + matching Discipline

### Milestone Buttons

The character sheet has buttons for:

- **Minor Milestone**: narrative change, optional temporary Trait.
- **Significant Milestone**: Level +1, one Core Skill +1 up to cap, Technical Rank +1 up to maximum.
- **Major Milestone**: Tier +1, Core cap +1, Discipline cap +1, Technical Rank maximum +1.

There are no skill pyramids or skill columns.

### Techniques and Spells

Actors can hold Techniques and Spells as owned Items.

- Techniques spend **Effort**.
- Spells spend **Mana** or **Favour**.
- Both support Rank, Cost, Actions, Core Triad, Discipline, Target Defense, Damage/Ward formula, effects, limits, and tags.

The sheet includes **Build Technique** and **Build Spell** entry points. Actor-owned abilities open in the integrated builder and are written through the canonical resolver contract.

Character-sheet spell/technique rows use compact native action rows with semantic icons, muted category accents, short cost/action chips, icon-only controls, and a GM-only icon override.

### Resources

The character sheet displays:

- Vitality
- Effort
- Mana
- Favour
- Conviction

Derived maximums:

- Effort max = `ceil(Stamina × 12.5)`
- Mana max = `ceil(Mana × 12.5)`
- Favour max = `ceil(Favour × 12.5)`

### Consequences

The sheet supports Mild, Moderate, and Severe Consequences with states:

- Clear
- Open
- Bound
- Healing

### Defenses

Derived defenses are currently calculated as:

- Guard = 10 + Strength + Sturdiness + Stamina + Guard bonus
- Evasion = 10 + Awareness + Precision + Technique + Evasion bonus
- Resolve = 10 + Confidence + Heart + Intellect + Resolve bonus
- Ward = 10 + max(Mana, Favour) + Heart + Sturdiness + Ward bonus

### Spell and Technique Library

The integrated builder provides personal, General, Class, and GM library pages for both Spells and Techniques. Entries can be dragged or added to an owned Actor as independent canonical Items, and actor-scoped building can optionally save a separate personal-library copy.

## Notes

The legacy implementation remains available only to protect existing data and provide a regression baseline. New development follows the phase controls in `programme/`; it does not extend the old Core Skill, Technical Rank, formula-string, or critical/fumble systems.

## v0.1.75

- Added the canonical single-class contract for all ten Heir and Scion classes with their final Class Anchors.
- Synchronized canonical class IDs, class names, and roles without restricting Techniques or Spells by class.
- Added GM-only Class Anchor replacement overrides and automatic override clearing when class changes.
- Reserved inactive Character Affinities for future branching specialisations without introducing multiclassing.
- Connected weapon/ability document attachment to canonical class resolution and exposed diagnostics and mutation APIs through `game.ecclesia.rules.classes`.
- Added an idempotent Actor migration, class-contract documentation, and automated coverage.

## v0.1.74

- Added the canonical Phase 3 Weapon Type, Weapon Item, and Ability contracts without binding ordinary abilities permanently to weapons.
- Added unified, stable world registries for Weapon Tags, Focus Tags, and Weapon Types with GM-only mutation, revision tracking, and soft archiving.
- Added dynamic attachment resolution for class Core Skill affinities, duplicate prevention, Technique/Spell capability, all/any tag requirements, Focus coverage, and weapon-granted abilities.
- Added structured single/selectable/split weapon profiles, Technique damage/range modification modes, Relic Action exceptions, and atomic one-weapon equip plans with turn-cost rules.
- Added canonical Item/Actor storage paths, document adapters, `game.ecclesia.rules`, a detailed rules contract, fixtures, and automated coverage.

## v0.1.73

- Added separate world and client migration ledgers with stable migration ids, primary-GM execution, failure diagnostics, and automatic retry.
- Added a migration that safely copies retained builder flags on world and actor-owned Items into the `mythic-journey` namespace without deleting legacy data.
- Exposed migration status and controlled reruns through `game.ecclesia.migrations`.
- Added dependency-free automated tests, legacy fixtures, package validation, and a live-world acceptance matrix in `TESTING.md`.

## v0.1.72

- Fixed Foundry v13 rejecting legacy `getFlag()` calls after the former Spell & Technique Builder module is disabled.
- Restored PC sheet rendering for actors containing spell or technique Items.
- Restored the integrated builder ready lifecycle so built-in and migrated library records load normally.

## v0.1.71

- Consolidated Ecclesia Combat Automation into the game system.
- Consolidated the Ecclesia Spell & Technique Builder and its import template into the game system.
- Added one-time migration of legacy world/client combat settings and world/user builder libraries.
- Moved new builder item flags and chat automation flags to the `mythic-journey` namespace while retaining legacy read/guard compatibility.
- Added unified `game.ecclesia.combat` and `game.ecclesia.builder` APIs and retained the previous macro aliases.
- Added permanent in-world warnings when either superseded external module is still active.


## v0.1.55

- Boss NPC sheets now remember the active boss tab when actor data changes.
- Editing a boss part no longer sends the sheet back to the Overview tab.
- Boss sheets also preserve the boss body scroll position across re-renders where possible.

## v0.1.3 update

- Replaced the front-page builder checklist with an interactive Charactermancer.
- Added a guided step-by-step builder for Role/Class, Core Skills, starting Discipline, Traits, first Secondary Skill, Resources, and Review.
- Charactermancer writes directly to the Actor and validates the opening build.
- Character sheet now shows a compact launch panel instead of a static checklist.


## v0.1.5 update

- Reworked the character sheet to reduce tab friction.
- Added a top combat resource ribbon under the header.
- Moved Core Skills into a left-hand vertical list grouped by Mental, Physical, and Divine.
- Placed Secondary Skills beside Core Skills in the centre rail.
- Gave Traits, Temporary Traits, and Bond Features the right half of the main sheet.
- Reduced the tab count to Main, Combat, Techniques & Spells, and Milestones & Notes.
- Added styled Vitality, Effort, Mana, and Favour resource cards with icons.
- Added a circular Conviction display.


## v0.1.6 update

- Made the top character header more compact.
- Increased resource icon and number size for readability.
- Added a faded blended character-art background layer using the actor portrait.
- Reclaimed vertical space for the main sheet content.


## v0.1.7 update

- Reworked Secondary Skills into compact rows showing only the skill name and current base roll bonus.
- Left-click a Secondary Skill row to roll it.
- Right-click a Secondary Skill row to open its editor.
- Renamed Secondary Skill modes to 2-source skill and 3-source skill.
- 2-source skill editing now hides the third Core field.
- Removed stored Discipline from Secondary Skills; a Discipline is chosen from a dropdown at roll time and its rank is added automatically.


## v0.1.8 update

- Styled Ecclesia secondary-skill dialogs to use the dark Aelun-style glass theme.
- Improved readability for editor and roll prompt dialogs with higher-contrast text, fields, and buttons.


## v0.1.9 update

- Added the full default Secondary Skill list to the actor template.
- New characters now start with Notice, Investigation, Insight, Knowledge, Lore, Arcana, Religion, Athletics, Acrobatics, Stealth, Thievery, Survival, Medicine, Persuasion, Deception, Intimidation, Performance, Command, Endurance, Resolve, Craft, Engineering, Streetwise, and Initiative.
- Added a Secondary Skills “Defaults” button to add any missing default skills to existing actors without duplicating skills by name.


## v0.1.10 update

- Reduced the default character sheet size.
- Enabled normal resizing on character sheets.
- Added minimum sheet sizing rules so the layout remains usable while resizing.


## v0.1.11 update

- Added a first-render sheet-size normaliser to fix old oversized remembered window dimensions.
- Added a Reset Size button to the sheet header.
- Ensured the Foundry resize handle remains visible.


## v0.1.12 update

- Forces a sane initial sheet size on first render to override old maximised remembered dimensions.
- Stops the setPosition normaliser from fighting user resizing.
- Reworks the character sheet form/body into a flex layout so resizing does not break the content area.
- Adds compact top-section rules for shorter resized windows.


## v0.1.13 update

- Adds a stronger post-render opening-size correction for sheets that Foundry tries to reopen maximised.
- Caps character sheets to a sensible maximum size while keeping them resizable smaller.
- Makes the top header, build warning, resource ribbon, and tabs much more compact.
- Stabilises the main sheet area with a fixed flex body and scrollable tabs.


## v0.1.14 update

- Hotfixed Charactermancer opening after the sheet resize work.
- Charactermancer button is now bound before the actor-sheet editability gate.
- Charactermancer opening now has error handling and a safer app instance.
- Removed the fixed Charactermancer app id to avoid stale/hidden window conflicts.
- Added stable Charactermancer sizing and scroll behaviour.


## v0.1.15 update

- Removed the actor-sheet editability gate that could block Secondary Skill listeners.
- Secondary Skills can now be rolled with left-click and edited with right-click or double-click.
- Made Secondary Skill index detection more robust.
- Charactermancer actor updates now explicitly refresh the actor sheet after writing changes.


## v0.1.16 update

- Removed the Charactermancer from the system for now.
- Removed Charactermancer buttons and build-warning launch panels from the character sheet.
- Reworked Secondary Skill rows to use explicit Roll and Edit buttons.
- Left-clicking Roll now rolls immediately when no Disciplines exist.
- If Disciplines exist, Roll opens a Discipline selector before rolling.
- Editing no longer relies on right-click only; the visible pencil button opens the editor.


## v0.1.17 update

- Rebuilt Secondary Skills as expandable details rows instead of JavaScript-only right-click editors.
- Closed rows stay compact: name, current base bonus, and a dice roll button.
- Expanding a row reveals inline editable fields that use normal Foundry sheet data binding.
- 2-source skills hide Core 3; switching to 3-source and changing the sheet will reveal Core 3 after the sheet re-renders.
- Removed fragile row-click editing behaviour; rolling now uses the explicit dice button.


## v0.1.18 update

- Restored the missing `_onAddDefaultSecondarySkills` method, fixing the listener setup error that could break Secondary Skill interactions.
- Hardened Secondary Skill index lookup so blank or missing data-index values cannot accidentally target the first skill.
- Made the sheet opening-size correction unconditional on first render while the sheet layout is still under active development.
- Removed the leftover unused Charactermancer partial template.


## v0.1.19 update

- Added live recalculation for displayed Secondary Skill bonuses when Core Skill values change.
- Displayed Secondary Skill bonuses now also update when a Secondary Skill's source cores, type, or flat bonus are changed inline.
- Core 3 is now present but hidden for 2-source skills, allowing it to appear immediately when switching a skill to 3-source.


## v0.1.20 update

- Moved Disciplines from the left rail to the right rail, above Temporary Traits.
- Changed Discipline add/remove listeners to delegated handlers so the Add Discipline button works reliably after layout changes.
- Hardened Discipline add/remove methods and explicitly re-renders the sheet after updates.
- Made Secondary Skills more compact and arranged them in a two-column grid where space allows.
- Open Secondary Skill editors now span both columns for easier editing.

## v0.1.28 - Heir/Scion Class Sheet Overhaul

- Replaced the create-actor player type with separate Heir and Scion actor types, plus NPC.
- Added type labels so Foundry's Create Actor dropdown shows Heir, Scion, and NPC.
- Added a bespoke Heir/Scion charactermancer that opens automatically for unfinished Heir and Scion actors.
- Charactermancer flow: class preview, Class Choice 1, Class Choice 2, Class Choice 3, stat array suggestions, and traits.
- Finishing the charactermancer writes the class, household, traits, core array, starting discipline, and starting class actions to the actor.
- NPC sheets now hide role, class, household, Technical Rank, Core Cap, Discipline Cap, and build validation UI.
- Rollable class actions now support no-roll actions, range display, extra resource costs, and descriptive variable costs.

## v0.1.28 - Compact Combat Tab

- Removed the Build & Advancement panel from the Combat tab.
- Moved Techniques, Spells, Weapons, and Armor onto the Combat tab.
- Streamlined combat rows, action buttons, and consequence layout for a denser sheet.



## v0.1.33
- Compacts the dynamic Secondary Skill rows so the roll button remains fully visible.
- Moves generated core-pair text into the expanded details area instead of crowding the row.


## v0.1.35

- Added derived Vitality maximums using `class base + Sturdiness × 3`.
- Vitality max is now displayed as a calculated value instead of a manually edited resource maximum.
- Charactermancer completion now fills starting Vitality to the calculated maximum.
- Migration now recalculates stored Vitality maximums and only clamps current Vitality if it is above the new maximum.
- Hardened class lookups for movement and Vitality so class names are matched case-insensitively.
- Movement overlay now explicitly uses the actor sheet's current Speed value after class/override calculation, so highlighted movement range follows sheet movement.


## v0.1.36

- Fixed movement overlays still showing 6 squares for every class by ignoring the stale template default in `system.movement.squares` during overlay calculations.
- Movement range now calculates directly from the actor class speed unless a movement override is set on the sheet.
- Added migration support to rewrite stored movement class defaults and displayed Speed values for existing actors.
- Charactermancer completion now writes the selected class speed into the actor's movement data.
- Added an unlinked-token fallback so tokens created before class selection can still use the base actor's current class speed.

## v0.1.37

- Secondary Skills panel now generates all Core Skill pairings internally, selects the top 24 dynamic Secondary Skills by calculated score, and displays those instead of only non-zero pair combinations.
- Added Secondary Skill sort controls: score and alphabetical.
- Added Secondary Skill view toggles: Dynamic + Custom, Dynamic Only, and Custom Only.


## v0.1.41
- Fixed Vitality formula sync after compact sheet update.
- Bumped the internal migration version so existing actors recalculate Vitality again.
- Vitality now recalculates from class base + Sturdiness × 3 when class or Sturdiness changes.
- Existing actors that were full at their old maximum are kept full at their recalculated maximum.


## v0.1.43

- Added Ecclesia initiative: d20 + highest of Leadership, Readiness, or Notice.
- Initiative rolls can include an optional Discipline, such as Quickslinger.
- Initiative ties are stored with second-best and third-best skill decimal tie-breakers.
- GM initiative override inputs are added to the combat tracker sidebar.


## v0.1.44
- Made spell/technique/item editor content scrollable within the item sheet window.
- Compact item headers slightly so editor fields have more room.

## v0.1.48

- Reworked Vitality calculation to use class durability, Sturdiness, Significant Milestone count, and Major Milestone count.
- Level now means Significant Milestones received. Tier now means Major Milestones received.
- Unlevelled characters use starting class base + Sturdiness × 2.
- Significant and Major milestones now dynamically increase Vitality using class gains and bracketed Sturdiness gains.
- Major Milestones no longer increase Level; they only increase Tier, caps, and scale-of-play progression.
- Migration converts older actors from baseline Level 1 / Tier 1 into true milestone counts.

## v0.1.49

- Softened the character-sheet portrait background so the actor image is larger, clearer in the centre, and airbrushed to full transparency at the edges.
- Increased the blended portrait-art opacity by 7 percentage points across desktop and responsive sheet sizes.

### v0.1.56 — Spell/Technique Editor Cleanup
- Removed Cost Text, Discipline, Impact, Shape, Weapon Dice, Source, Method, Expression, and Target Defense from the Technique/Spell item editor.
- Attack Type now carries defence targeting directly, including a new Magic Homing Attack option that targets Ward only.
- Roll Mode now controls the visible core selectors: d20 shows three cores, d20 - 2 core shows two, and No roll hides all core selectors.
- Technique/Spell item rolls no longer include the removed item Discipline field.



### v0.1.59 — Boss Main Body Combat Turn

- Boss NPCs now keep a Main Body combatant in the tracker instead of deleting the parent combatant after creating part turns.
- Added a Main Initiative field to the Boss NPC overview.
- The boss combat button now prepares the Main Body turn and all active part turns without duplicating existing entries.
- Combat tracker labels now distinguish Main Body from individual boss part turns.

### v0.1.58 — Boss Ability Usability

- Boss custom abilities now have compact Use and Damage buttons directly on their collapsed summary rows.
- Part custom abilities now default to collapsed instead of fully expanded, making boss sheets less heavy during play.
- Removed the Skill selector from boss custom ability editors; boss abilities now use Flat Bonus only.
- Boss custom ability rolls now ignore stored Skill selections and always use the Flat Bonus field.

### v0.1.57 — Boss Sheet Non-Refreshing Edits
- Boss NPC sheets now save individual field changes without forcing a full sheet re-render.
- Editing boss parts should no longer jump back to the Overview tab or steal focus from the field being edited.
- Boss sheet tab and scroll restoration remains in place for structural changes such as adding/removing parts, skills, abilities, traits, and resistances.

## v0.1.62 Compact Action Rows + Warder Hooks Merge

- Preserves the v0.1.61 compact native spell/technique action row styling, semantic icons, GM icon overrides, actor-sheet drag payloads, and template/CSS layout changes.
- Re-applies the v0.1.60 Warder integration hooks on top of the v0.1.61 styling base.
- Character sheet no-roll item use emits `ecclesia.itemUsed` after normal action/resource spending and chat output.
- Character sheet attack use emits `ecclesia.itemAttackResolved` with structured hit/miss results, source token UUID, item UUID, boss target IDs, and defence data.


## v0.1.63 Warder compatibility hotfix

- Fires `ecclesia.itemUsed` for all item roll modes, allowing on-use Warder spells such as Wall and Fortify to trigger even if an older actor item was created before Warder flags existed.
- Native Warder class entries for Wall and Fortify are now no-roll support actions for newly-created characters.

## v0.1.64 Combat Sheet Spatial Economy

- Reworked the character sheet Combat tab into a single-column wide layout so spells and techniques use the full sheet width.
- Moved Defenses into a compact horizontal summary strip at the top of the Combat tab.
- Moved Action Economy into the same top strip, removed the explanatory text, and compacted Standard/Move/Speed controls.
- Kept the `.combat-side` container as a top summary strip so compatible modules that inject combat utility panels, such as resistances, can still attach there and display horizontally.
- Tightened the Combat Actions toolbar and reduced visual weight on secondary row buttons so spell and technique entries read more cleanly.
- Moved Consequences below the combat action lists as a compact footer to prioritise action-list space.


## v0.1.65 Combat Sheet Compact Refresh

- Further compresses the Combat tab top utility area so Defenses, compatible Resistance panels, and Action Economy can share a single tight row on wider sheets.
- Defense bonus fields are now compact inline + fields rather than full secondary labels.
- Action Economy is reduced to a small Actions panel with Standard, Move, Speed, Override, and Reset controls only.
- Spell and Technique action rows are now native collapsed `<details>` rows by default. Clicking the row expands it to show read-only Effect, Description, and Limit text without opening the item editor.
- Row action buttons stop propagation so Use/Cast, Damage, Conditions, Edit, Delete, and GM icon override do not accidentally expand/collapse the row.


## v0.1.66 Combat Action Breathing Room

- Keeps the compact top combat utility strip from v0.1.65.
- Gives Technique and Spell panels more internal padding and a wider gap between the two columns.
- Makes collapsed Technique/Spell cards roomier, with larger icons, larger names, more readable metadata, and better spacing around cost/action chips.
- Keeps primary play buttons visible while hiding maintenance buttons such as GM icon override, Edit, and Delete until hover/focus/open, reducing row clutter during play.
- Expanded Effect/Description/Limit panels now have more padding and line spacing.


## v0.1.67

- Native spell/technique attack summaries now include targeted Warder wall overlays alongside normal token targets.
- Warder wall hits are included in structured `ecclesia.itemAttackResolved` results without disrupting token targeting or boss target routing.

## v0.1.68 Warder wall direct damage targeting

- Warder walls are now treated as damageable battlefield objects rather than defending combatants.
- When only Warder walls are targeted, using a d20 spell/technique with a damage or ward formula now skips the to-hit roll and applies the damage/healing formula directly through Combat Automation.
- Native targeted-defence summaries no longer roll against Warder wall defences.

## v0.1.69 Averaged Core Roll Bonuses

- Two-core 4dF Secondary Skills now use the average of the two selected Core Skills, rounded up, plus any Roll Bonus.
- Three-core d20 spell/technique rolls now add the three selected Core Skills together, divide by two, round up, then add any Roll Bonus and condition roll modifiers.
- Two-core d20 spell/technique rolls now use the same averaged two-core formula, rounded up.
- Character-sheet Secondary Skill displays and item roll summaries now show the rounded-average/halved formula so the displayed bonus matches the roll.
- Defence values are unchanged.
