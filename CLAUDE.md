# CLAUDE.md - MMOSkillQuestPack

Zone questlines for MMO Skill Tree **1.6.0+** (hard dependency in `manifest.json`).
The jar ships only the engine (quest givers, dialogues, zone scoping) plus the five-quest
onboarding chain (getting_started -> craft_starter_tools -> gather_the_basics ->
into_the_fray -> climbing_the_ranks); this pack ships the actual Orbis campaign content.
Without it the quest log is minimal by design (the bounty-pack philosophy).

## Layout

```
manifest.json                  Group Ziggfreed, hard-dep Ziggfreed:MMOSkillTree ^1.6.0
build.ps1                      zips MMOSkillQuestPack-<Version>.zip (fwd-slash + dir entries)
Server/
  MMOSkillTree/
    Control/MMOSkillQuestPack.json   add-mode for every MMOSkillTree type shipped here (NEVER
                                omit one). NpcPlacements are NOT listed: that store belongs to
                                ziggfreed-common and has no add/replace control gate.
    Dialogues/                  3 Payload-wrapped dialogue trees ({Name, Payload:{Start,
                                Nodes}}): guide_wilds + guide_sands + quartermaster_wilds
                                (all standalone, explicit + sugar). 1.5.0: quartermaster is now standalone
                                (the extends/params template DSL was removed); shared giver
                                skeletons use native "Parent":"<id>" inheritance instead.
    DialogueTemplates/          (empty) native Parent BASES go here (a base dialogue others
                                inherit by "Parent":"<id>"); the old QuestGiver_Standard
                                params template was removed with the DSL
    QuestTemplates/             Zone_{Slay,Gather,TurnIn}_Standard (extends/params DSL)
    Quests/                     11 Emerald Wilds + 8 Howling Sands (raw Payload quests)
    Achievements/               campaigns + orbis_campaigner meta + well_met + zone hunts
  ZiggfreedCommon/
    NpcPlacements/              3 givers, all anchored to a worldgen SpawnMarker
                                (Anchor.Structure, SpawnChance 1.0 + MaxPerWorld 1 = one
                                unique each): guide_wilds @ Kweebec village
                                (Feran_Windwalker), quartermaster_wilds @ Kweebec_Merchant
                                (Klops_Merchant), guide_sands @ Outlander_Hunter camp
                                (Outlander_Hunter). Each file authors Identity.BaseRole
                                mmo_questgiver + Appearance + NameKey + HintKey (all
                                explicit - the placement schema has no per-field defaults);
                                the jar GENERATES the native role at runtime (NO
                                hand-authored role JSON ships here - see below).
  Languages/<bcp47>/            mmoskilltree.lang (quest/dialogue/achievement keys) +
                                npcs.lang (giver names + shared questgiver.hint) x9

  (No NPC/Roles/ here: an Identity with an Appearance and no explicit Role opts into a
   GENERATED role - the engine clones the jar's mmo_questgiver base per placement with that
   Appearance + NameKey nameplate + HintKey and registers them as a runtime asset pack on
   plugin start(), so all three givers exist with ZERO role JSON.)
```

## How it fits together

- **Givers are NPC PLACEMENTS** (`Server/ZiggfreedCommon/NpcPlacements/*.json`), read by the
  placement engine in ziggfreed-common. Each uses `Anchor.Structure` so it stands beside a
  matching base-game worldgen SpawnMarker (`MarkerIds`, an exact case-insensitive allow-list
  that is FAIL-CLOSED: no entry, or a typo, anchors to nothing). `Where` names the `primary`
  world selector, so givers appear in the ordinary world and in no instance.
  `/mmonpc list` shows what targets a world and what is standing, `/mmonpc structures` lists
  seen markerIds to author `MarkerIds`, `/mmonpc reconcile [world]` forces a sweep, and
  `/mmonpc disable <id>` despawns one immediately.
- **Press F is routed by `Interact.Bindings`, not by the role.** Each giver binds
  `mmoskilltree:ui_target` to `dialogue:<dialogueId>:<npcId>` and `mmoskilltree:npc_id` to its
  own id. **Use the `ui_target` form, NOT `Interact.Dialogue`**: only `ui_target` carries the npc
  id into the dialogue, which is what these trees' own `npcquests:@self` options resolve against
  and what puts the character's name in the header. Authoring both leaves the `ui_target` unused
  and is a validator finding. `mmoskilltree:npc_id` is the same id the pack's quests bind as
  `npcViewId` / turn in against, so the two must agree.
- **Giver appearance + nameplate ship in that one placement file, no role JSON.** `Identity`
  authors `BaseRole: "mmo_questgiver"` + `Appearance` (a vanilla appearance id) + `NameKey` +
  `HintKey`. The placement schema carries NO per-field defaults, so author all four explicitly;
  the shared hint is `npcs.questgiver.hint` and the name convention is `npcs.<npcId>.name`, both
  shipped in npcs.lang. Omitting `Identity.Role` is what opts into role generation: the jar's
  base role is cloned per placement with those substituted and registered as a RUNTIME asset pack
  on `start()` (before worldgen streams, so no spawn race). Set an explicit `Identity.Role` only
  to ship a custom hand-authored role instead. A new giver = one placement file (+ the
  `npcs.<id>.name` it reuses for objective text).
- **Dialogues** are id-keyed standalone Payload-wrapped trees. Intro options fire
  `Talk` (the ONLY native TALK_TO_NPC source besides the MmoQuestTalk action)
  **gated on `QuestState <introQuest> ACTIVE`, NOT a story flag** (1.4.0 self-heal:
  a `SetFlag`/`NotFlag` survives a quest reset and soft-locks the intro - derive
  visibility from quest state, which resets with the quest). The bread handout is
  `Reward Once:true`; its option hides via the implicit, reset-clearable flag
  `reward:guide_wilds_dialogue:camp_talk:0`. Options use the [sugar shorthand]
  (`Open`/`Goto`/`Talk`/`TurnIn`/`Reward`/`Do`). 1.5.0 removed the dialogue template
  DSL (`extends`/`params`/`nodeOverrides`/prune): quartermaster_wilds is now a
  standalone turn-in-giver tree; a set of givers that share a skeleton should use
  native `"Parent":"<id>"` inheritance (per-node keyed merge, convention loc-keys)
  instead. See CONTENT_PACKS.md "Dialogue authoring".
- **Hub dialogue**: the jar owns it (`mmo_hub_intro`), and the hub placement points at it.
  To give the guide a different greeting, override the jar's hub placement by dropping your
  own `Server/ZiggfreedCommon/NpcPlacements/Mmo_Hub.json` (same id wins) whose
  `mmoskilltree:ui_target` reads `dialogue:<your dialogue id>:mmo_hub`. Keep the third
  segment: it is what carries the NPC's name into the dialogue header and makes `@self`
  resolve, which the giver trees here rely on.
- **Campaign flow**: wilds_meet_the_guide (autoAccept, gated on the jar `gather_the_basics`
  tutorial; a quest-level `turnInNpcId: guide_wilds` and NO npcViewId, so the engine
  auto-appends a "Go to Ranger Wren" report-back turn-in + fires the map marker; Wren's
  `QuestState ACTIVE`-gated intro option `TurnIn`s it) -> camp/meal/combat arc (Wren) +
  supply arc (Bramble) -> proving_day -> call_of_the_dunes (`turnInNpcId: guide_sands`,
  npcViewId guide_wilds => "Go to Dunewalker Ashkar" + marker; Ashkar spawns at the first
  Outlander camp loaded beside its Outlander_Hunter marker; his intro, gated on the
  autoAccept sands_walker_of_the_wastes ACTIVE, completes sands_walker via `Talk` AND
  `TurnIn`s call_of_the_dunes in the same option) -> the desert arc. "Go meet NPC X"
  handoffs are standardized on `turnInNpcId` (the report-back turn-in), never a TALK bridge.
- **Zone scoping**: `"zone": "Howling_Sands"` on objectives/criteria matches the
  engine's zoneName OR region folder names case-insensitively. The Snake contract
  needs it (snakes spawn in zones 1, 2 and 3); the hunter achievement chains are
  fully zone-scoped kill counters.
- **minLevel is the SUMMED total of all skill levels** (not highest skill). Ramp:
  wilds 10/20 -> sands 40/60/80/100.
- **Verified-id notes** (ids re-verify against `hytale-shared-source/HytaleAssets/Server/**`, id = filename; `hytale-resources/{items,mobs}-index.json` for a fast id->name lookup): wood targets are CONTAINS substrings
  (`Wood_Gumboab`, `Wood_Palm` - the items are `Wood_<Species>_Trunk*`); fish are
  CONTAINS bare names (matches both the role id and `Fish_<Name>_Item`); Salmon
  does NOT spawn in zones 1-2 (use Catfish); bare Zombie has no vanilla overworld
  placement (graveyard watch hunts Skeletons); `Rock_Sandstone` is EXACT to avoid
  crafted variants; Trork/Scarak/Skeleton_Sand/Snake are CONTAINS families.

## Build & deploy

`.\build.ps1` inside the pack (auto-installs when `HYTALE_MODS_DIR` is set).
Verify: server log shows the Dialogue/Quest layer-applied lines plus the NpcPlacements
merge, `/mmonpc list` shows the three givers targeting the world, and
`/mmoconfig validate` reports the dialogue + placement domains clean.

## Conventions

PascalCase filenames; raw types use `{"Name": ..., "Payload": {...}}`, dialogues
too (PascalCase codec keys inside `Payload.Start`/`Payload.Nodes`; node ids/map
keys + sugar values stay lowercase). **NpcPlacements are the exception - a Pattern A
full structured asset: NO `Payload` wrapper, NESTED PascalCase groups
(`Identity`:{`Role`/`BaseRole`/`Appearance`/`NameKey`/`HintKey`}, `Where` (a world
selector, `Names`/`Match`/`GameplayConfig`/`ExcludeNames`),
`Anchor`:{`WorldSpawn`/`Coords`/`Structure`/`Zone`/`Custom`},
`Limits`:{`SpawnChance`/`MaxPerWorld`/`OncePerWorld`}, `Lifecycle`,
`Interact`:{`Dialogue`/`Bindings`}), decoded directly by the engine (editor-native).
`Appearance` set + `Identity.Role` omitted => the engine generates a role from
`Identity.BaseRole` (NameKey nameplate + HintKey hint); set `Identity.Role` only to ship a
custom hand-authored role. Every leaf inherits, so a variant of an existing placement is a
file carrying `"Parent": "<id>"` plus only the leaves that differ, and `Interact.Bindings`
is a MAP keyed by channel that merges per key, so a child replaces one channel and keeps the
rest.** Quest-driven option visibility derives from `QuestState`, never a parallel
`SetFlag`/`NotFlag` (self-heal convention); "go meet NPC X" steps use a quest-level
`turnInNpcId` (report-back turn-in) completed by the giver's `TurnIn` dialogue option
(or the NpcQuestPage Complete button), never a TALK objective. Lang values are data-free
flavor (no digits, no reward restating). Commit + push HERE first, then bump the gitlink
in the root repo.
