# MMO Skill Quest Pack

A free content pack for [MMO Skill Tree](https://www.curseforge.com/hytale/mods/mmo-skill-tree). It adds a hand-written **questline** with its own quest-giver NPCs: a starter campaign in the **Emerald Wilds**, tied together with branching dialogue, campaign achievements, and zone-scoped hunter chains. A tougher follow-up in the **Howling Sands** is written and coming in a later update.

Requires the MMO Skill Tree mod (1.6.1 or newer) and Ziggfreed's CommonLib, the library it already runs on (2.1.0 or newer). The pack supplies the content; the mod and its library supply the quest, dialogue, NPC and boss-fight engines.

[![Discord](https://img.shields.io/badge/Discord-Join%20Server-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/5NFdZsUxHZ) [![Ko-fi](https://img.shields.io/badge/Ko--fi-Support-FF5E5B?style=for-the-badge&logo=ko-fi&logoColor=white)](https://ko-fi.com/ziggfreed) [![Documentation](https://img.shields.io/badge/Docs-Read%20More-0ea5e9?style=for-the-badge)](https://mmo-skill-tree-docs.ziggfreed.com)

---

[![Host your own Hytale server with Kinetic Hosting](https://i.imgur.com/UHn3FzW.png)](https://billing.kinetichosting.com/aff.php?aff=1262)

---

## What it adds

- **The Emerald Wilds campaign** - eleven quests that start the moment you spawn. Ranger Wren waves you over to her camp by the fires, teaches you to build, hunt, fish, and gather, then sends you against trork warbands and the restless dead before pointing you south to the desert's edge.
- Every quest giver holds a real conversation: progress-aware greetings, lore branches you can ask about, and a one-time bite of camp bread from Wren if you ask nicely.
- The givers come to you. Wren and Bramble stand at your world spawn automatically; nothing to place, nothing to set up.
- **A night arc for Kweebec Nightmare owners.** If you also run that minigame, Ranger Wren starts hearing screams out of the kweebec grove after dark. Two story quests and three daily contracts send you into the grove and back, and a three-rung Night Owl ladder hands over the Nightwarden armour set a piece at a time. Without the minigame installed, none of it appears anywhere.
- Gear you can only earn: Wren's Prospecting Pick for finding her camp, and the Nightwarden's Longsword, Hood, Cuirass and Greaves from the night arc, reskinned onto a darker, void-touched look with damage, mana and a little stamina baked in. No recipes, no shop.
- **A world boss you place yourself.** The Scarak Brood Queen fights in three phases, with her brood coming up out of the sand as she weakens, and she scales her health to however many players are on her. Everyone in the fight gets credit, and the loot she pays is rolled per player and scaled by their share of it. A boss bar tracks her, a map marker follows her while she stands, the whole world hears when she rises, and the fight keeps its own leaderboard. She comes back a day after she dies. Installing her is two commands, once per world: load the shipped arena prefab and place it, then `/zigencounter spawn Sands_Brood_Queen_Encounter` on top of it.
- Campaign achievements: a capstone for the Emerald Wilds, a "Well Met" for introducing yourself to every guide, and zone-scoped hunter ladders that count the creatures you fell in each region, including the Howling Sands.
- Every quest name and blurb, dialogue line, NPC name, and achievement ships in 9 languages (English, German, Spanish, French, Hungarian, Italian, Brazilian Portuguese, Russian, Turkish); anything a translation misses falls back to English.

## How it works

- Join a world with the pack installed and Ranger Wren is already at spawn with your first quest. Follow the chain at your own pace - each quest tells you what it needs, tracks your progress live, and hands off to the next.
- Press the interact key on a quest giver and a dialogue opens: pick what to say, accept or turn in quests, ask about the world. The conversation changes as you progress through their questline.
- A finished quest waits for you. Once you turn it in, the reward sits in your quest log until you collect it, so you never lose one to a full backpack at the wrong moment.
- The quest givers are placed once per world for everyone, but each player runs the campaign on their own quest log.

## Don't want the spawn NPCs?

The quest givers place through the same system as the mod's Adventurer's Guide, so the same controls apply:

- **Before anyone joins:** set `"enabled": false` for `guide_wilds`, `quartermaster_wilds`, and/or `guide_sands` in `mods/ziggfreedcommon/npc-placements.json` and the ones you list will never spawn.
- **Already in your world?** Run `/mmonpc list` to see their ids, then `/mmonpc disable --arg1=<id>` - it despawns right away and stays gone. Changed your mind? `/mmonpc enable --arg1=<id>` brings it back.

## Install

1. Install the MMO Skill Tree mod (1.6.1 or newer) and Ziggfreed's CommonLib (2.1.0 or newer).
2. Drop `MMOSkillQuestPack-1.1.0.zip` into your server's `Mods/` folder, alongside them.
3. Start the server. The questlines and their givers are on by default.
4. Want the Brood Queen? In creative with builder tools, `/prefab load MMOSkillQuestPack/Sands_Brood_Queen_Spawners` puts her arena in your clipboard; place it where the fight belongs, stand on it and run `/zigencounter spawn Sands_Brood_Queen_Encounter`. Once per world, ever. Switch her off later under `sands_brood_queen_encounter` in `mods/ziggfreedcommon/encounters.json`.

## Make your own quests and dialogue

The questlines are plain pack files. A new quest is one small file that names a target, an amount and who hands it out; a whole family of similar quests is a single file with one row per member; a new quest giver is one more file; and dialogue trees are readable JSON. The pack's repository has the authoring notes, including how to wire a new giver, branch a conversation, and scope an objective to a zone.

## Versions

| Pack  | Plugin | Notes                                                                                                                                                                                                                                                                                                                                                                                                       |
| ----- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.1.0 | 1.6.1+ | The Scarak Brood Queen, a world boss a server owner places once under the desert: three phases with her brood rising as she weakens, health scaled to the party, credit for everyone in the fight and loot rolled per player by their share, a boss bar, a following map marker, a server-wide notice when she rises, her own leaderboard, and a return one in-game day after each kill. The Brood Below, the quest capping the Howling Sands campaign (held with that campaign), plus two new lines from Dunewalker Ashkar. A content-audit warning on Wren's bread offer is gone. Requires Ziggfreed's CommonLib 2.1.0 or newer. All 9 languages. |
| 1.0.0 | 1.6.0+ | First release. The Emerald Wilds campaign (eleven quests) with two quest-giver NPCs, branching dialogue, zone-scoped objectives and hunts, a campaign achievement, and hunter ladders across both regions. The Emerald Wilds campaign is handed out at the Adventurer's Guide hub and points you east to Ranger Wren beside the kweebec village; it gates on finishing the plugin's own intro tutorial first. Quest givers spawn automatically through generated NPC roles (Wren and Bramble at the kweebec village). The Kweebec Nightmares arc, for servers that also run the Kweebec Nightmare minigame: two story quests from Ranger Wren, three daily grove contracts and a three-rung Night Owl achievement ladder, all invisible without that minigame. Five earned-only items (Wren's Prospecting Pick and the four-piece Nightwarden set, reskinned onto a darker look with Void damage, mana and a touch of stamina). Full 9-language translations. The Howling Sands campaign (eight quests) is written and comes in a later update; Dunewalker Ashkar already stands at the desert's edge but has nothing to offer yet. |

---

## Links & Support

[![MMO Skill Tree](https://img.shields.io/badge/CurseForge-MMO%20Skill%20Tree-F16436?style=for-the-badge&logo=curseforge&logoColor=white)](https://www.curseforge.com/hytale/mods/mmo-skill-tree) [![Get Pro Edition](https://img.shields.io/badge/Get%20Pro%20Edition-F59E0B?style=for-the-badge)](https://mmo-skill-tree-docs.ziggfreed.com/commercial)

Questions or suggestions? Join the [Discord](https://discord.gg/5NFdZsUxHZ) or leave a comment!

**Support Development:** [Ko-fi](https://ko-fi.com/ziggfreed) | [Buy Me a Coffee](https://buymeacoffee.com/wintergreensolutions)

_MMO Skill Tree is not affiliated with Hypixel Studios or Hytale._
