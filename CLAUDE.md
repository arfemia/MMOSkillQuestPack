# CLAUDE.md - MMOSkillQuestPack

Zone questlines for MMO Skill Tree **1.4.0+** (hard dependency in `manifest.json`).
The jar ships only the engine (quest givers, dialogues, zone scoping) plus the two
tutorial quests; this pack ships the actual Orbis campaign content. Without it the
quest log is minimal by design (the bounty-pack philosophy).

## Layout

```
manifest.json                  Group Ziggfreed, hard-dep Ziggfreed:MMOSkillTree ^1.4.0
build.ps1                      zips MMOSkillQuestPack-<Version>.zip (fwd-slash + dir entries)
Server/
  MMOSkillTree/
    Control/MMOSkillQuestPack.json   add-mode for every shipped type (NEVER omit a type)
    QuestGivers/                3 givers, all placement=structure (1.4.0 A3: anchored
                                beside a worldgen SpawnMarker, spawnChance 1.0 + maxPerWorld
                                1 = one unique each): guide_wilds @ Kweebec village,
                                quartermaster_wilds @ Kweebec_Merchant, guide_sands @
                                Outlander_Hunter camp
    Dialogues/                  4 Payload-wrapped dialogue trees ({Name, Payload:{Start,
                                Nodes}}): guide_wilds + guide_sands (explicit + sugar),
                                quartermaster_wilds (extends questgiver_standard),
                                guide_hub (hub welcome + directs to Wren)
    DialogueTemplates/          QuestGiver_Standard (extends/params/nodeOverrides/extraNodes)
    QuestTemplates/             Zone_{Slay,Gather,TurnIn,Talk}_Standard (extends/params DSL)
    Quests/                     11 Emerald Wilds + 8 Howling Sands (raw Payload quests)
    Achievements/               campaigns + orbis_campaigner meta + well_met + zone hunts
  NPC/Roles/Passive/            MMO_Quest_Wilds (Ranger Wren, Feran_Windwalker),
                                MMO_Quest_Wilds_Supply (Bramble, Klops_Merchant),
                                MMO_Quest_Sands (Ashkar, Outlander_Hunter); all
                                OpenMmoUi Target=auto -> giver:<id> routing
  Languages/<bcp47>/            mmoskilltree.lang (quest/dialogue/achievement keys) +
                                npcs.lang (names/hints + npc-id objective names) x9
```

## How it fits together

- **Givers** use `placement: structure` (1.4.0 A3): the jar's StructureGiverSpawnSystem
  spawns each beside a matching base-game worldgen SpawnMarker (`match.markerIds`), once
  per structure instance (persistent seen-set), via QuestGiverSpawnService; they record
  routing target `giver:<id>`. Pressing F resolves LIVE: dialogue when configured, else
  the npc quest list. `/mmonpc givers` shows match + spawn state, `/mmonpc structures`
  lists seen markerIds to author `match`, `/mmonpc reset` clears the structure seen-set.
- **Dialogues** are id-keyed standalone Payload-wrapped trees. Intro options fire
  `Talk` (the ONLY native TALK_TO_NPC source besides the MmoQuestTalk action)
  **gated on `QuestState <introQuest> ACTIVE`, NOT a story flag** (1.4.0 self-heal:
  a `SetFlag`/`NotFlag` survives a quest reset and soft-locks the intro - derive
  visibility from quest state, which resets with the quest). The bread handout is
  `Reward Once:true`; its option hides via the implicit, reset-clearable flag
  `reward:guide_wilds_dialogue:camp_talk:0`. Options use the [sugar shorthand]
  (`Open`/`Goto`/`Talk`/`TurnIn`/`Reward`/`Do`); quartermaster_wilds `extends`
  `questgiver_standard` (turn-in giver) as the live template example. See
  CONTENT_PACKS.md "Dialogue authoring".
- **Hub dialogue**: `guide_hub_dialogue` welcomes the player and points them to
  Ranger Wren, then opens the hub menu (`Open: hub`). It is wired by setting
  `mods/mmoskilltree/spawn-hub.json` `"dialogue": "guide_hub_dialogue"` (the jar's
  generic SpawnHubConfig hook; default null = hub menu, so it is an opt-in). The
  meet-the-guide quest still `autoAccept`s, so the campaign starts with or without
  this opt-in.
- **Campaign flow**: wilds_meet_the_guide (autoAccept; the hub Guide directs you to
  Wren, whose `QuestState ACTIVE`-gated intro option completes it) -> camp/meal/
  combat arc (Wren) + supply arc (Bramble) -> proving_day -> call_of_the_dunes
  (TALK_TO_NPC guide_sands - Ashkar spawns at the first Outlander camp loaded, beside
  its Outlander_Hunter marker; his intro, gated on the autoAccept
  sands_walker_of_the_wastes ACTIVE, completes the bridge AND sands_walker in the same
  Talk) -> the desert arc.
- **Zone scoping**: `"zone": "Howling_Sands"` on objectives/criteria matches the
  engine's zoneName OR region folder names case-insensitively. The Snake contract
  needs it (snakes spawn in zones 1, 2 and 3); the hunter achievement chains are
  fully zone-scoped kill counters.
- **minLevel is the SUMMED total of all skill levels** (not highest skill). Ramp:
  wilds 10/20 -> sands 40/60/80/100.
- **Verified-id notes** (hytale-resources): wood targets are CONTAINS substrings
  (`Wood_Gumboab`, `Wood_Palm` - the items are `Wood_<Species>_Trunk*`); fish are
  CONTAINS bare names (matches both the role id and `Fish_<Name>_Item`); Salmon
  does NOT spawn in zones 1-2 (use Catfish); bare Zombie has no vanilla overworld
  placement (graveyard watch hunts Skeletons); `Rock_Sandstone` is EXACT to avoid
  crafted variants; Trork/Scarak/Skeleton_Sand/Snake are CONTAINS families.

## Build & deploy

`.\build.ps1` inside the pack (auto-installs when `HYTALE_MODS_DIR` is set).
Verify: server log shows the Dialogue/Quest-giver/Quest layer-applied lines and
`/mmoconfig validate` reports the dialogue + questgiver domains clean.

## Conventions

PascalCase filenames; raw types use `{"Name": ..., "Payload": {...}}`, dialogues
too (PascalCase codec keys inside `Payload.Start`/`Payload.Nodes`; node ids/map
keys + sugar values stay lowercase). Quest-driven option visibility derives from
`QuestState`, never a parallel `SetFlag`/`NotFlag` (self-heal convention). Lang
values are data-free flavor (no digits, no reward restating). Commit + push HERE
first, then bump the gitlink in the root repo.
