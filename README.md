# MMOSkillQuestPack

Zone questlines for [MMO Skill Tree](https://mmo-skill-tree-docs.ziggfreed.com/) (1.6.1+) and
its ZiggfreedCommon library (2.1.0+).

Two campaign arcs handed out by quest-giver NPCs, and a world boss:

- **Emerald Wilds** (Ranger Wren + Quartermaster Bramble at world spawn): survival,
  gathering, and combat quests easing new players from their first camp to the
  edge of the desert.
- **Howling Sands** (Dunewalker Ashkar, who appears where the desert is first
  discovered): a tougher desert arc with zone-scoped hunts, turn-ins, and a
  branching finale, capped by a boss fight. The arc is written and ships held; it
  opens in a later update.
- **The Scarak Brood Queen**: a three-phase world boss run by the game's own encounter
  scripts, with shared credit for everyone in the fight, loot scaled by each player's
  share, a boss bar, a world-map marker, a leaderboard and her own notices. She comes
  back on her own after a kill.

Plus branching NPC dialogue, campaign achievements, and zone-scoped hunter chains.

## Install

Drop `MMOSkillQuestPack-<version>.zip` into your server `Mods/` folder next to
`MMOSkillTree-1.6.1+.jar` and `ZiggfreedCommon-2.1.0+.jar`. Build from source with
`.\build.ps1`.

## Placing the Brood Queen

The pack ships the fight and the arena; you pick the spot, once per world, ever.

1. In creative with builder tools, run
   `/prefab load MMOSkillQuestPack/Sands_Brood_Queen_Spawners` to put the arena (her
   clutch and five brood clutches, each a block that keeps a spawn marker alive) in your
   clipboard, and place it where the fight belongs. Flat sand under the old kingdom's
   ruins reads best; keep the whole paste inside one chunk. A spawn-marker figure stands over
   each clutch as soon as the paste lands (creative players see them, adventure players do
   not); if none appears, `/zigencounter validate` says why.
2. Stand on the clutch and run `/zigencounter spawn Sands_Brood_Queen_Encounter`.

The encounter entity is saved with the world, wakes when a player comes near, raises the
queen from her clutch, and re-arms itself after every fight. Prefer to shape the arena by
hand? The two clutch blocks are ordinary items (`Sands_Brood_Queen_Spawner`,
`Sands_Brood_Spawner`): give yourself some and set them down instead of pasting.

To switch her off, set `"Enabled": false` under `sands_brood_queen_encounter` in
`mods/ziggfreedcommon/encounters.json` and run `/zigencounter reload`. To tune how long she
rests after a kill, set `"Timing": {"Rest": "PT8H"}` under the same key (one game day, `P1D`,
as shipped; the library keeps the rest on the fight itself, so it survives a restart);
`/zigencounter validate` audits the fight's files. Fight her in adventure mode: a creative
player is not counted as part of the fight.
