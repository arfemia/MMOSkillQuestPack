# CLAUDE.md - MMOSkillQuestPack

Zone questlines for MMO Skill Tree **1.6.1+** on ZiggfreedCommon **2.1.0+** (both hard
dependencies in `manifest.json`), plus the Scarak Brood Queen, the family's exemplar OVERWORLD boss
(a native encounter script the server owner stands up once; see "The Brood Queen" below).
The jar ships only the engine (quest givers, dialogues, zone scoping) plus a small starter set - the
five-quest onboarding chain (getting_started -> craft_starter_tools -> gather_the_basics ->
into_the_fray -> climbing_the_ranks), the repeatable forgot_something, four side quests under
Onboarding/SideQuests/, and the Forgotten Temple arc under Temple/ (what_the_stones_remember ->
the_watch_below -> keeper_of_the_deep, plus the temple_tribute daily) at a second standing of the
guide inside the temple; this pack ships the actual Orbis campaign content.
Without it the quest log is minimal by design (the bounty-pack philosophy).

**This pack is the reference model for the new-shape content layout**: quests, quest
generators and achievements are ziggfreed-common Pattern A assets under
`Server/ZiggfreedCommon/`, and the schema authority is
`additional-mods/ziggfreed-common/zc-progression` (`quest/asset/`, `achievement/asset/`,
`progress/asset/`, `progress/gate/`) - read those codecs, never guess a field name.

## Layout

```
manifest.json                  Group Ziggfreed, hard-deps Ziggfreed:MMOSkillTree ^1.6.1 (the
                               ENCOUNTER_DEFEATED kind the boss quest names is 1.6.1's) and
                               Ziggfreed:ZiggfreedCommon >=2.1.0 (the encounter stores are 2.1.0's)
build.ps1                      zips MMOSkillQuestPack-<Version>.zip (fwd-slash + dir entries)
Server/
  ZiggfreedCommon/             the shared framework's own stores: they merge BY ID and have no
                               add/replace control gate, so they are NOT listed in Control/
    Quests/MMOSkillTree/Zones/
        Zone_Gather_Base.json      Abstract skeletons the two generators write children against
        Zone_Slay_Base.json        (unmarked folder => plain filename ids)
        _Wilds/  (13 files)        a _-marked folder prefixes every id beneath it, so
        _Sands/  (6 files)         _Wilds/First_Camp.json is wilds_first_camp;
                                _Sands/Brood_Below.json (sands_brood_below) is the boss quest,
                                the only ENCOUNTER_DEFEATED step in the pack
    QuestGenerators/
        Zone_Gather.json           writes the 3 harvest contracts, one row each
        Zone_Slay.json             writes the 3 hunting contracts, one row each
    Achievements/MMOSkillTree/Zones/   11 files: 2 campaigns + orbis capstone + well_met + 2 hunt
                                chains + the 3-rung night-owl chain
    Lootables/                     4 loot tables: one per Kweebec daily (Mmo_Kweebec_Daily_Amateur /
                                _Nightmare / _Hardcore; each daily's Auto reward is a Lootable
                                entry naming one, so a win pays out in the instance) and
                                Mmo_Sands_Brood_Queen, the boss's own per-participant payout,
                                named by the binding row's Loot.OnDefeat
    Encounters/                    the boss's BINDING ROW (Sands_Brood_Queen_Encounter.json, a zc
                                EncounterBindingAsset named for its script): NameKey, Enabled,
                                Subject slot, party Scale, Timing, Loot, Leaderboard, Feedback,
                                Discovery. It re-states NOTHING the script says (no phase, no
                                threshold, no count); owner layer mods/ziggfreedcommon/encounters.json
    FeedbackMoments/               the boss's OWN four notices (Sands_Brood_Queen_Engaged /
                                _Phase_Changed / _Defeated / _Wiped), named from the row's
                                Feedback group. NEVER override a zc Encounter_* moment by id: a
                                moment id is process-global and the words would land on every
                                fight on the server
    NpcPlacements/                 3 givers, all anchored to a worldgen SpawnMarker
                                (Anchor.Structure, SpawnChance 1.0 + MaxPerWorld 1 = one
                                unique each): Guide_Wilds @ Kweebec village,
                                Quartermaster_Wilds @ Kweebec_Merchant, Guide_Sands @
                                Outlander_Hunter camp. Each names only its role by
                                Identity.Role, which IS its character id
    Dialogues/MMOSkillTree/        3 structured dialogue trees (Pattern A, no Payload wrapper:
                                Start/Nodes/Memories/Fragments are fields of the file itself):
                                Guide_Wilds + Guide_Sands + Quartermaster_Wilds (all
                                standalone, explicit + sugar). A set of givers sharing a
                                skeleton uses native "Parent":"<id>" inheritance.
  Item/Items/                    5 reward items, native Hytale assets (not an MMOSkillTree store,
                                so nothing in Control/ names them): Wilds_Prospectors_Pick plus the
                                Nightwarden Longsword/Hood/Cuirass/Greaves. Each Parents a vanilla
                                item, carries NO Recipe (a recipe never inherits through Parent, so
                                they stay earn-only) and adds MMO_* stats on the surface that owns
                                them - Utility for a held tool, Weapon for a weapon, Armor for armor.
                                Armor/Weapon/Utility/Tags are FULL-OBJECT-REPLACE: copy the parent's
                                whole block before adding a line
    MMOSkillQuestPack/           the boss's 2 SPAWNER BLOCKS (Sands_Brood_Queen_Spawner,
                                Sands_Brood_Spawner): a BlockType whose BlockEntity carries a
                                SpawnMarkerBlock component naming a marker + a MarkerOffset, the
                                vanilla Scarak egg-sack look, NO Gathering group and NO Support
                                rule (unharvestable, never pops off uneven ground). Names are
                                native server.items.<Id>.name in server.lang
  Prefabs/MMOSkillQuestPack/     Sands_Brood_Queen_Spawners.prefab.json, the arena: a bare blocks
                                array (version 8, blockIdVersion 11, anchors 0) of one queen
                                clutch at the origin and five brood clutches around it, within a
                                9x9 footprint so a paste stays inside one chunk. The owner
                                pastes it once: /prefab load MMOSkillQuestPack/Sands_Brood_Queen_Spawners
  EncounterManager/              Sands_Brood_Queen_Encounter.json, the boss's native encounter
                                SCRIPT (Type Generic on Zc_Encounter_Base's shape; the id is the
                                FIGHT's, never a role's, because scripts and roles share one id
                                namespace and a script named after a role replaces it at load)
  NPC/Roles/Passive/             the 3 giver ROLES, one per character: Guide_Wilds
                                (Feran_Windwalker), Quartermaster_Wilds (Klops_Merchant),
                                Guide_Sands (Outlander_Hunter). Each is a native Variant of
                                the jar's Template_Mmo_QuestGiver carrying only Appearance +
                                NameTranslationKey - see below
  NPC/Roles/MMOSkillQuestPack/   the boss's 3 PHASE ROLES (Sands_Brood_Queen, _Phase2, _Phase3):
                                native Variants of the vanilla Template_Scarak_Broodmother
                                carrying only MaxHealth (1200 / 600 / 300, each later one the
                                health she CARRIES in, so the bar reads full per phase and the
                                three total the first), the shared NameTranslationKey and the
                                vanilla _CombatConfig immunities. Modify may name only the
                                template's declared parameters plus _CombatConfig
  NPC/Spawn/Markers/MMOSkillQuestPack/  2 ManualTrigger SpawnMarkers: Sands_Brood_Queen_Marker
                                (the queen, SpawnAfterGameTime P1D on the world clock) and
                                Sands_Brood_Marker (a weighted Scarak_Fighter / Scarak_Louse
                                brood, real-time respawn). DeactivationDistance 512 and
                                DeactivationTime 150 keep the engine from packing a spawn away
                                while the fight's chunk is still awake after a wipe
  MMOSkillTree/                  the mod's OWN stores
    Control/MMOSkillQuestPack.json   add-mode for every MMOSkillTree type shipped here (NEVER
                                omit one). This pack ships no content into a store the mod
                                itself owns today, so the file names none.
  Languages/<bcp47>/          mmoskilltree.lang (quest/dialogue/achievement keys) +
                                npcs.lang (giver names + the shared Mmo_QuestGiver.hint
                                prompt the template role bakes) + server.lang (item display
                                names, Hytale's native items.<ItemId>.name convention, which
                                each item references as server.items.<ItemId>.name) x9
```

## How it fits together

- **Givers are NPC PLACEMENTS** (`Server/ZiggfreedCommon/NpcPlacements/*.json`), read by the
  placement engine in ziggfreed-common. Each uses `Anchor.Structure` so it stands beside a
  matching base-game worldgen SpawnMarker (`MarkerIds`, an exact case-insensitive allow-list
  that is FAIL-CLOSED: no entry, or a typo, anchors to nothing). `Where` authors
  `Match: ["default"]` (an exact world-name pattern), so givers appear in the ordinary world
  and in no instance.
  `/mmonpc list` shows what targets a world and what is standing, `/mmonpc list --arg1=structures`
  lists the marker ids the anchor engine has SEEN to author `MarkerIds` (`--arg1=markers` scans the
  live entity store instead, so the two disagreeing localizes a sighting problem),
  `/mmonpc reconcile [--arg1=<world>]` forces a sweep, and `/mmonpc disable --arg1=<id>` despawns
  one immediately.
- **Who a giver IS is its `Identity.Role`; where press F GOES is `Interact.Dialogue`.** None of the
  three placements authors an `Identity.NpcId`: with none, the character IS the role it names, so
  `Guide_Wilds` / `Guide_Sands` / `Quartermaster_Wilds` are the ids the pack's quests name as
  `Npc.ViewId`, turn in against, and a talk step credits. Author an `NpcId` only to make one
  standing a character of its own that nothing else answers to. Each giver's `Interact.Dialogue`
  names its conversation by file id, and the conversation is AUTOMATICALLY with the character
  standing there: the header name, `@self`, the talk credit and every quest-aware line all read
  the placement's own identity, so nothing is ever restated. `Interact.Open` is the same value for
  a press-F that opens something other than a conversation, naming any destination a mod on the
  server registered; author one or the other, never both (`INTERACT_BOTH_FORMS`).
- **Giver appearance + nameplate ship in a ROLE file this pack ships**, at
  `Server/NPC/Roles/Passive/<RoleId>.json`, named from the placement by `Identity.Role`. Each of the
  three is a three-line native `Variant` of `Template_Mmo_QuestGiver` (the jar's parameterized
  template, which holds the whole idle/watch/press-F body), with a `Modify` block carrying just
  `Appearance` (a vanilla Model id) and `NameTranslationKey`. **`Modify` may name ONLY the five
  parameters that template declares** - `Appearance`, `NameTranslationKey`, `Weapons`, `OffHand`,
  `DefaultOffHandSlot` - because the engine refuses the whole role over one undeclared key and the
  symptom is a giver who is never anywhere. The press-F prompt is NOT one of them (a role's `Hint`
  is read literally, so no variant can change it): every giver here shows the template's shared
  prompt, `npcs.Mmo_QuestGiver.hint`, which this pack translates in all nine locales. A giver
  needing its own prompt needs a full role body instead. The name convention is
  `npcs.<npcId>.name`. A new giver = one role file + one placement file + its two `.lang` lines.
- **Quests are Pattern A assets with native `Parent` inheritance.** One file per quest, the
  FILENAME is the id, every leaf inherits, and `Objectives` merges per objective id. A `_`-marked
  ancestor folder contributes its name (sans `_`, lower-cased) as an id prefix, which is what keeps
  `wilds_`/`sands_` ids while the filenames read as beats. **Basenames must be unique across the
  WHOLE store even in different folders** (the engine keys assets by filename before the prefix is
  folded in), and **renaming a `_`-marked folder renames every id under it**, which starts anyone
  mid-campaign over.
- **Two families are written by GENERATORS.** `QuestGenerators/Zone_Gather.json` and
  `Zone_Slay.json` each carry one row per contract and emit ordinary child quests carrying
  `Parent: zone_<family>_base`, so a generated contract behaves exactly like a hand-written one and
  can be replaced by a real file of the same id the day it needs to be special. Adding a contract is
  one row plus its two `.lang` lines. A generated id comes from `IdPattern` alone and takes no folder
  prefix, so the rows spell the full `wilds_`/`sands_` id. **Zone turn-in quests are hand-authored**
  (one member is not a family).
- **Gates are the shared `Requires` block.** `Quests` names prerequisites, `AnyOf` is "either route
  will do", and level floors are `Factors` over `hytale:stat`: `MMO_TotalLevel` (every skill summed),
  `MMO_Level_<SKILL>` (one skill), `MMO_CombatLevel` (best combat skill), `MMO_HighestSkillLevel`
  (best single skill). A feature switch is `{"Factor":"mmoskilltree:feature","Param":"<feature>"}`.
  Everything fails CLOSED, so content gated on something nothing can answer stays locked.
- **Rewards are `{Kind, Params}` entries.** `Mmo_Xp` (Skill/Amount), `Item` (Item/Count),
  `Mmo_Boost_Token` (Skill/Multiplier/DurationMinutes), plus `Command`, `Currency`,
  `Mmo_Ability_Mod`, `Lootable`, `Stamped_Item`, `Effect`, `Droplist`. Kind ids are native-asset
  style, PascalCase with underscores, mod-prefixed for a consumer's own; the old lowercase
  spellings still parse but nothing in this pack writes them. **Prefer `Item` over a `/give`
  command**: an item reward is room-checked, so a full bag holds the payout back instead of losing
  it. `Params` values are strings. An entry with a BLANK `Kind` pays nothing out, which is how a
  generator row skips a reward slot the rest of its family uses.
- **Achievements are the peer Pattern A asset.** `Criteria` is an id-keyed map
  (`{"<criterion-id>": {...}}`), and the KEY is what progress is filed under, so renaming one
  starts that criterion over while adding, removing or reordering entries never moves anyone's
  tally, a `Parent` child retunes one
  criterion by key and keeps the rest, `MetaChildren` makes a capstone over other achievements,
  and the `Rewards` group's `Auto` bucket lands on earning while `Claim` waits to be collected.
- **A description key with a `{0}` in it gets its number from `Text.TextArgs.Flavor: ["@amount"]`**,
  which the renderer fills from the criterion's own Amount. That is how the two hunter rungs share
  ONE translated line and a balance pass changes a number without touching any locale. `@skill`
  fills in a skill's readable name the same way; anything else is used exactly as typed.
- **A tiered pair or ladder declares itself on `Listing.Chains: [{"Id": "<ladder>", "Tier": n}]`**,
  so the page draws the whole climb as one entry rather than a row of near-identical ones. An
  achievement may be a rung of several ladders at once, each with its own tier.
- **`Meta.mmoskilltree` is where a knob only this mod reads goes** (`ServerFirst` for a race,
  `Announce` for the winner's broadcast, `Feat`, `Feature`, `Class`, `LegacySince`). It is decoded
  through a real codec, so a mistyped key is named in the boot log and costs only that knob.
- **Dialogues** are id-keyed structured trees at `Server/ZiggfreedCommon/Dialogues/MMOSkillTree/`
  (Pattern A: `Start`, `Nodes`, `Memories` and `Fragments` are top-level fields of the file, no
  `Payload` wrapper). `Start` is declared sections (`First`/`Quests`/`Then`/`Fallback`); Guide_Sands
  and Quartermaster_Wilds write their state-varied greeting as one `When`-gated `First` beat plus a
  `Fallback` node for everyone else; Guide_Wilds uses the other two sections instead - a `Quests`
  map routing the two Kweebec quests to their Offerable/Active/Ready nodes, then `Then` beats for
  the mid- and post-campaign greetings, over the same `Fallback`. `{"Type":"MarkTalked"}`
  is the ONE way a conversation credits a talk step or a met-the-giver achievement - nothing
  credits implicitly, so every giver needs at least one always-reachable option carrying it. Intro
  beats are **gated on `QuestState <introQuest> ACTIVE`, NOT on remembered state** (self-heal: a
  memory that outlives a quest reset soft-locks the intro - derive visibility from quest state,
  which resets with the quest). The bread handout is a one-time option (`"Once": true`), so it is
  offered until it is taken, and nothing else guards it: the `Reward`'s own once-guard
  (`RewardOnce`, on when unauthored) never gets a second chance to matter under an option-level
  `Once`, so authoring it beside a `Do` array only earns the audit's `SUGAR_SHADOWED_BY_DO` warning
  (a bare sugar key next to `Do`). Options use the sugar
  shorthand (`Open`/`Goto`/`TurnIn`/`Reward`/`Do`); crediting is written out in full as an action,
  because it is a deliberate statement about the story rather than a side effect of opening a menu.
  `Open` names a registered destination - `"Quests"` is the bare word for the character the player
  is standing at, which is what every "what work do you have" option in this pack writes; naming a
  different character is `{"Type": "Quests", "Npc": "<id>"}`. See CONTENT_PACKS.md "Dialogue
  authoring".
- **Hub dialogue**: the jar owns it (`Mmo_Hub_Intro`), and the hub placement points at it.
  To give the guide a different greeting, override the jar's hub placement by dropping your
  own `Server/ZiggfreedCommon/NpcPlacements/Mmo_Hub.json` (same id wins) whose
  `Interact.Dialogue` names your own conversation. It is automatically with whichever character
  the placement stands, so nothing here ever names an npc id.
- **Campaign flow**: wilds_meet_the_guide (offered at the jar hub, `Npc.ViewId: Mmo_Hub`, gated on
  the jar `gather_the_basics` tutorial; one blank-target `TURN_IN` step locked to `Guide_Wilds`,
  which renders as "Go to Ranger Wren" and fires the map marker; Wren's `QuestState ACTIVE`-gated
  intro option `TurnIn`s it) ->
  camp/meal/combat arc (Wren) + supply arc (Bramble) -> proving_day -> call_of_the_dunes (offered by
  Guide_Wilds, handed in at Guide_Sands => "Go to Dunewalker Ashkar" + marker; Ashkar spawns at the
  first Outlander camp loaded beside its Outlander_Hunter marker; his intro, gated on the AutoAccept
  sands_walker_of_the_wastes ACTIVE, credits sands_walker via `MarkTalked` AND `TurnIn`s
  call_of_the_dunes in the same option) -> the desert arc. "Go meet NPC X" handoffs are standardized
  on a blank-target `TURN_IN` step (nothing to carry, only somebody to find), never a TALK bridge.
  The desert arc is authored but held: every `_Sands` quest (the boss quest `sands_brood_below`
  included) plus the `howling_sands_campaign` and `orbis_campaigner` achievements ship
  `"Enabled": false`, so call_of_the_dunes is the last reachable beat today. The three generated
  sands quests carry no such leaf, they simply chain off disabled prerequisites, so flipping those
  eight leaves to true is the whole edit that opens the arc. The Brood Queen FIGHT is live
  regardless: the quest is the only piece of her that waits on the arc.
- **The Kweebec Nightmares arc** (`_Wilds/Whispers_After_Dark`, `Face_The_Nightmare`, and the three
  dailies `Amateur_Night`/`Nightmare_Shift`/`Hardcore_Vigil`, plus `Zones/Night_Owl_T1..T3`) is
  gated on ANOTHER MOD being present:
  `{"Factor": "hytale:mod_installed", "Param": "<Group>:<Name>", "Min": 1}` reads 1 while that plugin
  is loaded and 0 while it is not, so the `Min` bound is what keeps the whole arc invisible without
  it. **A bounds-less `hytale:mod_installed` condition does NOT gate**: absence is a definite 0, not
  an unanswerable null, and `FactorCondition.accepts` passes any finite value when neither bound is
  authored. Always write `"Min": 1` for present, `"Max": 0` for absent. The manifest carries a
  matching `OptionalDependencies` entry, never a hard `Dependencies` one. Rounds are counted by `INSTANCE_ROUND_WON`, whose Target is
  `<modId>:<modeId>`; the bare `kweebec:` prefix with `MatchMode: PREFIX` means "any mode that mod
  runs", and `Qualifier` pins one difficulty preset. The three dailies use `Repeat.Reset.Period
  Daily` (a calendar window, so they all roll over together) plus an `Auto`-bucket reward so a win pays out
  in the instance rather than back at Wren.
- **The Brood Queen** is the family's exemplar overworld boss and is fifteen authored files plus a
  quest, no Java: the script (`Server/EncounterManager/Sands_Brood_Queen_Encounter.json`), the three phase
  roles, the two markers with their two spawner blocks and the arena prefab, the binding row
  (`Server/ZiggfreedCommon/Encounters/`, named for the SCRIPT), the four moment files, the loot
  table, and `_Sands/Brood_Below.json`. The script follows zc's `Zc_Encounter_Base` shape (the
  Player sensor + `EncounterMembers` collector as a `Continue` sibling, `TriggerSpawners` from the
  pack's `ManualTrigger` markers, the release on the Target sensor alone, `Zc_Phase_At_Health` with
  in-place role changes at `[0, 0.5]`, `Zc_Adds_Wave`, `Zc_Defeat_Beat` in every phase with its
  `ClearEncounterBossBar`, `CleanupOnRemove`) and adds what an OVERWORLD site needs and a round
  never does: a `Complete` that RE-ARMS (`Any` sensor, `ActionsBlocking`, `[Timeout, zc:reset,
  State Intro]`; never `Once` over a blocking list), a no-show beat under the pack's own signal
  id (`zc:mmoquestpack:no_show`), and a `Standing` state a phase falls into when no player has
  been within eighty blocks for a minute (after the library has settled the wipe), from which the
  next arriving player's presence sends `zc:reset` and Intro re-engages the wounded queen at the
  health she was left with. Two independent gates decide when she comes back after a kill: the
  marker's `SpawnAfterGameTime` (the one to tune) and Complete's `Timeout`. The quest step names
  the SCRIPT id (`Target: Sands_Brood_Queen_Encounter`, `Kind: ENCOUNTER_DEFEATED`, `Amount`), never
  a role id, because the phase swap changes the creature id under the player. The install is the
  owner's, once per world: paste the prefab, `/zigencounter spawn Sands_Brood_Queen_Encounter` on
  it; the entity persists. `/zigencounter validate` audits every one of these files
  (`ENCOUNTER_*` findings), and a `$Comment` in any of them is a public-facing tip.
- **Zone scoping**: `"Zone": "Howling_Sands"` on an objective or criterion matches the
  engine's zoneName OR region folder names case-insensitively. The snakes step in
  sands_thorns_and_stings needs it (snakes spawn in zones 1, 2 and 3); the hunter
  achievement chains are fully zone-scoped kill counters.
- **Level floors on the campaign ramp are the SUMMED total of all skill levels**
  (`MMO_TotalLevel`), not the highest skill. Ramp: wilds 10/20 -> sands 40/60/80/100. The three
  desert trade quests gate on their own skill instead (`MMO_Level_MINING` /
  `MMO_Level_WOODCUTTING` / `MMO_Level_FISHING`, 15 each).
- **Verified-id notes** (ids re-verify against `hytale-shared-source/HytaleAssets/Server/**`, id = filename; `hytale-resources/{items,mobs}-index.json` for a fast id->name lookup): wood targets are CONTAINS substrings
  (`Wood_Gumboab`, `Wood_Palm` - the items are `Wood_<Species>_Trunk*`); fish are
  CONTAINS bare names (matches both the role id and `Fish_<Name>_Item`); Salmon
  does NOT spawn in zones 1-2 (use Catfish); bare Zombie has no vanilla overworld
  placement (graveyard watch hunts Skeletons); `Rock_Sandstone` is EXACT to avoid
  crafted variants; Trork/Scarak/Skeleton_Sand/Snake are CONTAINS families.

## Build & deploy

`.\build.ps1` inside the pack (auto-installs when `HYTALE_MODS_DIR` is set).
Verify: server log shows the Dialogue/Quest/Achievement layer-applied lines plus the
NpcPlacements merge, zero `BuilderManager` `FAIL:` lines (the encounter script and the three
Scarak roles resolve by index), `/mmonpc list` shows the three givers targeting the world,
`/mmoconfig validate` reports the quest, achievement, dialogue + placement domains clean, and
`/zigencounter validate` reports the boss's script, binding row and loot table clean.

## Conventions

PascalCase filenames AND PascalCase codec keys. **Everything this pack ships lives under
`Server/ZiggfreedCommon/` and is a Pattern A full structured asset: NO `Payload` wrapper, NESTED
PascalCase groups, decoded directly by the engine (editor-native), every leaf inheriting through a
top-level `"Parent": "<id>"`** (dialogue node ids/map keys + sugar values stay lowercase). For
placements those groups are `Identity`:{`Role`/`NpcId`/`Aliases`} (the look and nameplate live on the ROLE file), `Where`,
`Anchor`, `Limits`, `Lifecycle`, `Interact`:{`Dialogue`/`Open`}; for dialogues
`Start`:{`First`/`Quests`/`Then`/`Fallback`}, `Nodes`, `Memories`, `Fragments`; for quests
`Text`/`Listing`/`Flow`/`Repeat`/`Npc`/`Requires`/`Objectives`/`Rewards`; for
achievements `Text`/`Listing`/`Scoring`/`Requires`/`Criteria`/`MetaChildren`/`Rewards`
(`Hidden`/`RequirePrerequisites`/`Icon` live on `Listing` for both, and `Rewards` is the shared
`{Auto, Claim}` group).
`Interact.Dialogue` and `Interact.Open` are two spellings of one destination; author one or the
other, never both. Quest-driven option visibility derives from `QuestState`, never a parallel
remembered memory (self-heal convention). Lang values are data-free flavor (no digits, no reward
restating) - a count belongs in `Text.TextArgs`, where the renderer supplies it from the content.
`$Comment` is allowed anywhere (both the codec and the per-key merge skip `$`-prefixed keys) and is
written for the server owner reading the file: what it does, what each number means, how to tune it.
Commit + push HERE first, then bump the gitlink in the root repo.
