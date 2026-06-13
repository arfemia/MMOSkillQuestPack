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
    QuestGivers/                3 givers: guide_wilds + quartermaster_wilds (placement
                                spawn, beside the hub Guide), guide_sands (zone_discovery
                                Howling_Sands - spawns once per world on first discovery)
    Dialogues/                  3 STRUCTURED dialogue trees (Start/Nodes directly, NOT
                                Payload-wrapped - the only structured MMO asset type)
    QuestTemplates/             Zone_{Slay,Gather,TurnIn}_Standard (extends/params DSL)
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

- **Givers** spawn via the jar's QuestGiverSpawnService and record routing target
  `giver:<id>`; pressing F resolves LIVE: dialogue when configured, else the npc
  quest list. `/mmonpc givers` shows spawn state; `/mmonpc reset` respawns.
- **Dialogues** are id-keyed standalone trees. Intro options fire `Talk` (the ONLY
  native TALK_TO_NPC source besides the MmoQuestTalk action) gated on one-shot
  flags (`NotFlag` + `SetFlag`), so auto-accept intro quests cannot deadlock on a
  quest-state condition. The bread handout is `Reward Once:true` and its option
  hides via the implicit flag `reward:guide_wilds_dialogue:camp_talk:0`.
- **Campaign flow**: wilds_meet_the_guide (autoAccept, completes on Wren's intro
  option) -> camp/meal/combat arc (Wren) + supply arc (Bramble) -> proving_day ->
  call_of_the_dunes (TALK_TO_NPC guide_sands - Ashkar spawns at first Howling
  Sands discovery; his intro option completes the bridge AND auto-accepts
  sands_walker_of_the_wastes in the same fire) -> the desert arc.
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
are structured (PascalCase codec keys; node ids/map keys stay lowercase). Lang
values are data-free flavor (no digits, no reward restating). Commit + push HERE
first, then bump the gitlink in the root repo.
