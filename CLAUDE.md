# CLAUDE.md - MMOSkillQuestPack

Zone questlines for MMO Skill Tree **1.6.0+** (hard dependency in `manifest.json`).
The jar ships only the engine (quest givers, dialogues, zone scoping) plus the five-quest
onboarding chain (getting_started -> craft_starter_tools -> gather_the_basics ->
into_the_fray -> climbing_the_ranks); this pack ships the actual Orbis campaign content.
Without it the quest log is minimal by design (the bounty-pack philosophy).

**This pack is the reference model for the new-shape content layout**: quests, quest
generators and achievements are ziggfreed-common Pattern A assets under
`Server/ZiggfreedCommon/`, and the schema authority is
`additional-mods/ziggfreed-common/zc-progression` (`quest/asset/`, `achievement/asset/`,
`progress/asset/`, `progress/gate/`) - read those codecs, never guess a field name.

## Layout

```
manifest.json                  Group Ziggfreed, hard-dep Ziggfreed:MMOSkillTree ^1.6.0
build.ps1                      zips MMOSkillQuestPack-<Version>.zip (fwd-slash + dir entries)
Server/
  ZiggfreedCommon/             the shared framework's own stores: they merge BY ID and have no
                               add/replace control gate, so they are NOT listed in Control/
    Quests/MMOSkillTree/Zones/
        Zone_Gather_Base.json      Abstract skeletons the two generators write children against
        Zone_Slay_Base.json        (unmarked folder => plain filename ids)
        _Wilds/  (8 files)         a _-marked folder prefixes every id beneath it, so
        _Sands/  (5 files)         _Wilds/Trork_Trouble.json is wilds_trork_trouble
    QuestGenerators/
        Zone_Gather.json           writes the 3 harvest contracts, one row each
        Zone_Slay.json             writes the 3 hunting contracts, one row each
    Achievements/MMOSkillTree/Zones/   8 files: 2 campaigns + orbis capstone + well_met + 2 hunt chains
    NpcPlacements/                 3 givers, all anchored to a worldgen SpawnMarker
                                (Anchor.Structure, SpawnChance 1.0 + MaxPerWorld 1 = one
                                unique each): Guide_Wilds @ Kweebec village,
                                Quartermaster_Wilds @ Kweebec_Merchant, Guide_Sands @
                                Outlander_Hunter camp. Each names only its role by
                                Identity.Role, which IS its character id
  NPC/Roles/Passive/             the 3 giver ROLES, one per character: Guide_Wilds
                                (Feran_Windwalker), Quartermaster_Wilds (Klops_Merchant),
                                Guide_Sands (Outlander_Hunter). Each is a native Variant of
                                the jar's Template_Mmo_QuestGiver carrying only Appearance +
                                NameTranslationKey - see below
  MMOSkillTree/                  the mod's OWN stores
    Control/MMOSkillQuestPack.json   add-mode for every MMOSkillTree type shipped here (NEVER
                                omit one). Only Dialogues today.
    Dialogues/                  3 Payload-wrapped dialogue trees ({Name, Payload:{Start,
                                Nodes}}): Guide_Wilds + Guide_Sands + Quartermaster_Wilds
                                (all standalone, explicit + sugar). A set of givers sharing a
                                skeleton uses native "Parent":"<id>" inheritance.
  Languages/<bcp47>/          mmoskilltree.lang (quest/dialogue/achievement keys) +
                                npcs.lang (giver names + the shared MMO_QuestGiver.hint
                                prompt the template role bakes) x9
```

## How it fits together

- **Givers are NPC PLACEMENTS** (`Server/ZiggfreedCommon/NpcPlacements/*.json`), read by the
  placement engine in ziggfreed-common. Each uses `Anchor.Structure` so it stands beside a
  matching base-game worldgen SpawnMarker (`MarkerIds`, an exact case-insensitive allow-list
  that is FAIL-CLOSED: no entry, or a typo, anchors to nothing). `Where` authors
  `Match: ["default"]` (an exact world-name pattern), so givers appear in the ordinary world
  and in no instance.
  `/mmonpc list` shows what targets a world and what is standing, `/mmonpc structures` lists
  seen markerIds to author `MarkerIds`, `/mmonpc reconcile [world]` forces a sweep, and
  `/mmonpc disable <id>` despawns one immediately.
- **Who a giver IS is its `Identity.Role`; where press F GOES is `Interact.Bindings`.** None of the
  three placements authors an `Identity.NpcId`: with none, the character IS the role it names, so
  `Guide_Wilds` / `Guide_Sands` / `Quartermaster_Wilds` are the ids the pack's quests name as
  `Npc.ViewId`, turn in against, and a talk step credits. Author an `NpcId` only to make one
  standing a character of its own that nothing else answers to. Each giver binds
  `mmoskilltree:ui_target` to `dialogue:<DialogueId>:<npcId>`. **Use the `ui_target`
  form, NOT `Interact.Dialogue`**: only `ui_target` carries the npc id into the dialogue, which is
  what these trees' own `npcquests:@self` options resolve against and what puts the character's
  name in the header. Authoring both leaves the `ui_target` unused and is a validator finding.
- **Giver appearance + nameplate ship in a ROLE file this pack ships**, at
  `Server/NPC/Roles/Passive/<RoleId>.json`, named from the placement by `Identity.Role`. Each of the
  three is a three-line native `Variant` of `Template_Mmo_QuestGiver` (the jar's parameterized
  template, which holds the whole idle/watch/press-F body), with a `Modify` block carrying just
  `Appearance` (a vanilla Model id) and `NameTranslationKey`. **`Modify` may name ONLY the five
  parameters that template declares** - `Appearance`, `NameTranslationKey`, `Weapons`, `OffHand`,
  `DefaultOffHandSlot` - because the engine refuses the whole role over one undeclared key and the
  symptom is a giver who is never anywhere. The press-F prompt is NOT one of them (a role's `Hint`
  is read literally, so no variant can change it): every giver here shows the template's shared
  prompt, `npcs.MMO_QuestGiver.hint`, which this pack translates in all nine locales. A giver
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
- **Rewards are `{Kind, Params}` entries.** `xp` (Skill/Amount), `item` (Item/Count),
  `boost_token` (Skill/Multiplier/DurationMinutes), plus `command`, `currency`, `ability_mod`,
  `lootable`, `stamped_item`, `effect`. **Prefer `item` over a `/give` command**: an item reward is
  room-checked, so a full bag holds the payout back instead of losing it. `Params` values are
  strings. An entry with a BLANK `Kind` pays nothing out, which is how a generator row skips a
  reward slot the rest of its family uses.
- **Achievements are the peer Pattern A asset.** `Criteria` is an ORDERED array and the order is
  PERMANENT (progress is filed per criterion by POSITION), a child that authors `Criteria` replaces
  the parent's list whole, `MetaChildren` makes a capstone over other achievements, and `Rewards`
  land on earning while `ClaimRewards` wait to be collected.
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
- **Dialogues** are id-keyed standalone Payload-wrapped trees. `{"Type":"MarkTalked"}` is the ONE
  way a conversation credits a talk step or a met-the-giver achievement - nothing credits implicitly,
  so every giver needs at least one always-reachable option carrying it. Intro beats are
  **gated on `QuestState <introQuest> ACTIVE`, NOT on remembered state** (self-heal: a memory that
  outlives a quest reset soft-locks the intro - derive visibility from quest state, which resets with
  the quest). The bread handout is a one-time option (`"Once": true`), so it is offered until it is
  taken; the `Reward` keeps its own inner once-guard. Options use the sugar shorthand
  (`Open`/`Goto`/`TurnIn`/`Reward`/`Do`); crediting is written out in full as an action, because it is
  a deliberate statement about the story rather than a side effect of opening a menu. See
  CONTENT_PACKS.md "Dialogue authoring".
- **Hub dialogue**: the jar owns it (`mmo_hub_intro`), and the hub placement points at it.
  To give the guide a different greeting, override the jar's hub placement by dropping your
  own `Server/ZiggfreedCommon/NpcPlacements/Mmo_Hub.json` (same id wins) whose
  `mmoskilltree:ui_target` reads `dialogue:<your dialogue id>:mmo_hub`. Keep the third
  segment: it is what carries the NPC's name into the dialogue header and makes `@self`
  resolve, which the giver trees here rely on.
- **Campaign flow**: wilds_meet_the_guide (AutoAccept, gated on the jar `gather_the_basics`
  tutorial; one blank-target `TURN_IN` step locked to `Guide_Wilds`, which renders as "Go to Ranger
  Wren" and fires the map marker; Wren's `QuestState ACTIVE`-gated intro option `TurnIn`s it) ->
  camp/meal/combat arc (Wren) + supply arc (Bramble) -> proving_day -> call_of_the_dunes (offered by
  Guide_Wilds, handed in at Guide_Sands => "Go to Dunewalker Ashkar" + marker; Ashkar spawns at the
  first Outlander camp loaded beside its Outlander_Hunter marker; his intro, gated on the AutoAccept
  sands_walker_of_the_wastes ACTIVE, credits sands_walker via `MarkTalked` AND `TurnIn`s
  call_of_the_dunes in the same option) -> the desert arc. "Go meet NPC X" handoffs are standardized
  on a blank-target `TURN_IN` step (nothing to carry, only somebody to find), never a TALK bridge.
- **Zone scoping**: `"Zone": "Howling_Sands"` on an objective or criterion matches the
  engine's zoneName OR region folder names case-insensitively. The Snake contract
  needs it (snakes spawn in zones 1, 2 and 3); the hunter achievement chains are
  fully zone-scoped kill counters.
- **Level floors on the campaign ramp are the SUMMED total of all skill levels**
  (`MMO_TotalLevel`), not the highest skill. Ramp: wilds 10/20 -> sands 40/60/80/100. The two
  desert trade quests gate on their own skill instead (`MMO_Level_MINING` /
  `MMO_Level_WOODCUTTING`, 15 each).
- **Verified-id notes** (ids re-verify against `hytale-shared-source/HytaleAssets/Server/**`, id = filename; `hytale-resources/{items,mobs}-index.json` for a fast id->name lookup): wood targets are CONTAINS substrings
  (`Wood_Gumboab`, `Wood_Palm` - the items are `Wood_<Species>_Trunk*`); fish are
  CONTAINS bare names (matches both the role id and `Fish_<Name>_Item`); Salmon
  does NOT spawn in zones 1-2 (use Catfish); bare Zombie has no vanilla overworld
  placement (graveyard watch hunts Skeletons); `Rock_Sandstone` is EXACT to avoid
  crafted variants; Trork/Scarak/Skeleton_Sand/Snake are CONTAINS families.

## Build & deploy

`.\build.ps1` inside the pack (auto-installs when `HYTALE_MODS_DIR` is set).
Verify: server log shows the Dialogue/Quest/Achievement layer-applied lines plus the
NpcPlacements merge, `/mmonpc list` shows the three givers targeting the world, and
`/mmoconfig validate` reports the quest, achievement, dialogue + placement domains clean.

## Conventions

PascalCase filenames AND PascalCase codec keys. `Server/MMOSkillTree/Dialogues` files are raw
types wrapped as `{"Name": ..., "Payload": {...}}` (node ids/map keys + sugar values stay
lowercase). **Everything under `Server/ZiggfreedCommon/` is a Pattern A full structured asset:
NO `Payload` wrapper, NESTED PascalCase groups, decoded directly by the engine (editor-native),
every leaf inheriting through a top-level `"Parent": "<id>"`.** For placements those groups are
`Identity`:{`Role`/`BaseRole`/`Appearance`/`NameKey`/`HintKey`/`NpcId`/`Aliases`}, `Where`,
`Anchor`, `Limits`, `Lifecycle`, `Interact`:{`Dialogue`/`Bindings`}; for quests
`Text`/`Listing`/`Flow`/`Repeat`/`Visibility`/`Npc`/`Requires`/`Objectives`/`Rewards`; for
achievements `Text`/`Listing`/`Scoring`/`Requires`/`Criteria`/`MetaChildren`/`Rewards`/`ClaimRewards`.
`Interact.Bindings` is a MAP keyed by channel that merges per key, so a child replaces one channel
and keeps the rest. Quest-driven option visibility derives from `QuestState`, never a parallel
remembered memory (self-heal convention). Lang values are data-free flavor (no digits, no reward
restating) - a count belongs in `Text.TextArgs`, where the renderer supplies it from the content.
`$Comment` is allowed anywhere (both the codec and the per-key merge skip `$`-prefixed keys) and is
written for the server owner reading the file: what it does, what each number means, how to tune it.
Commit + push HERE first, then bump the gitlink in the root repo.
