# MMO Skill Quest Pack

A free content pack for [MMO Skill Tree](https://www.curseforge.com/hytale/mods/mmo-skill-tree). It adds two hand-written **questlines** with their own quest-giver NPCs: a starter campaign in the **Emerald Wilds** and a tougher follow-up in the **Howling Sands**, tied together with branching dialogue, campaign achievements, and zone-scoped hunter chains.

Requires the MMO Skill Tree mod (1.6.0+). The pack supplies the content; the mod supplies the quest-giver, dialogue, and zone-scoping engine, so install both.

[![Discord](https://img.shields.io/badge/Discord-Join%20Server-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/5NFdZsUxHZ) [![Ko-fi](https://img.shields.io/badge/Ko--fi-Support-FF5E5B?style=for-the-badge&logo=ko-fi&logoColor=white)](https://ko-fi.com/ziggfreed) [![Documentation](https://img.shields.io/badge/Docs-Read%20More-0ea5e9?style=for-the-badge)](https://mmo-skill-tree-docs.ziggfreed.com)

---

[![Host your own Hytale server with Kinetic Hosting](https://i.imgur.com/UHn3FzW.png)](https://billing.kinetichosting.com/aff.php?aff=1262)

---

## What it adds

- **The Emerald Wilds campaign** - eleven quests that start the moment you spawn. Ranger Wren waves you over to her camp by the fires, teaches you to build, hunt, fish, and gather, then sends you against trork warbands and the restless dead before pointing you south. Quartermaster Bramble runs a supply side-arc alongside her.
- **The Howling Sands campaign** - eight tougher quests in the desert. Dunewalker Ashkar appears the first time anyone discovers the Howling Sands and sets you quarrying sandstone, felling desert hardwood, fishing the hidden oases, culling Scarak hives, and finally hunting the apex predators of the dunes.
- **Branching NPC dialogue** - every quest giver has a real conversation, not a single press of a button: progress-aware greetings, lore branches you can ask about, and a one-time bite of camp bread from Wren if you ask nicely.
- **Quest-giver NPCs that come to you** - Wren and Bramble stand at your world spawn automatically; Ashkar appears at the desert's edge the first time it's found. No placing anything, no setup.
- **A night arc for Kweebec Nightmare owners** - if you also run the Kweebec Nightmare minigame, Ranger Wren starts hearing screams out of the kweebec grove after dark. Two story quests and three daily contracts send you into the grove and back, and a three-rung Night Owl ladder hands over the Nightwarden armour set a piece at a time. Without that minigame installed, none of it appears anywhere.
- **Gear you can only earn** - Wren's Prospecting Pick for finding her camp, and the Nightwarden's Longsword, Hood, Cuirass and Greaves from the night arc. No recipes, no shop: you get them by doing the work.
- **Campaign achievements** - a capstone for each campaign, a server-first "Campaigner of Orbis" for finishing both, a "Well Met" for introducing yourself to every guide, and zone-scoped hunter ladders that count the creatures you fell in each region.
- **Fully translated** - every quest name and blurb, dialogue line, NPC name, and achievement ships in 9 languages (English, German, Spanish, French, Hungarian, Italian, Brazilian Portuguese, Russian, Turkish); anything a translation misses falls back to English.

## How it works

- **It just starts.** Join a world with the pack installed and Ranger Wren is already at spawn with your first quest. Follow the chain at your own pace - each quest tells you what it needs, tracks your progress live, and hands off to the next.
- **Talk, don't just bump.** Press the interact key on a quest giver and a dialogue opens: pick what to say, accept or turn in quests, ask about the world. The conversation changes as you progress through their questline.
- **The desert opens itself.** You don't have to find Ashkar on a map - the first time any player steps into the Howling Sands, he appears at the threshold, once per world, and never duplicates on restart.
- **Quests scale with you.** The Wilds ease you in; the Sands quests ask for more skill levels before you can take them, so the desert stays aspirational until you've trained up. A few hunts only count in their own region.
- **Shared world, personal progress.** The quest givers are placed once per world for everyone, but each player runs the campaigns on their own quest log.

## Don't want the spawn NPCs?

The quest givers place through the same system as the mod's Adventurer's Guide, so the same controls apply:

- **Before anyone joins:** set `"enabled": false` for `guide_wilds`, `quartermaster_wilds`, and/or `guide_sands` in `mods/ziggfreedcommon/npc-placements.json` and the ones you list will never spawn.
- **Already in your world?** Run `/mmonpc list` to see their ids, then `/mmonpc disable --arg1=<id>` - it despawns right away and stays gone. Changed your mind? `/mmonpc enable --arg1=<id>` brings it back.

## Install

1. Install the MMO Skill Tree mod (1.6.0 or newer).
2. Drop `MMOSkillQuestPack-1.0.0.zip` into your server's `Mods/` folder, alongside the mod.
3. Start the server. The questlines and their givers are on by default.

## Make your own quests and dialogue

The questlines are plain pack files. A new quest is one small file that names a target, an amount and who hands it out; a whole family of similar quests is a single file with one row per member; a new quest giver is one more file; and dialogue trees are readable JSON. The pack's repository has the authoring notes, including how to wire a new giver, branch a conversation, and scope an objective to a zone.

## Versions

| Pack  | Plugin | Notes                                                                                                                                                                                                                                                                                                                                                                                                       |
| ----- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.0.0 | 1.6.0+ | First release. The Emerald Wilds (eleven quests) and Howling Sands (eight quests) campaigns with three quest-giver NPCs, branching dialogue for each, zone-scoped objectives and hunts, campaign and server-first achievements plus per-zone hunter ladders. The Emerald Wilds campaign is handed out at the Adventurer's Guide hub and points you east to Ranger Wren beside the kweebec village; it gates on finishing the plugin's own intro tutorial first. Quest givers spawn automatically through generated NPC roles (Wren and Bramble at the kweebec village, Ashkar on first Howling Sands discovery). The Kweebec Nightmares arc, for servers that also run the Kweebec Nightmare minigame: two story quests from Ranger Wren, three daily grove contracts and a three-rung Night Owl achievement ladder, all invisible without that minigame. Five earned-only items (Wren's Prospecting Pick and the four-piece Nightwarden set). Full 9-language translations. |

---

## Links & Support

[![MMO Skill Tree](https://img.shields.io/badge/CurseForge-MMO%20Skill%20Tree-F16436?style=for-the-badge&logo=curseforge&logoColor=white)](https://www.curseforge.com/hytale/mods/mmo-skill-tree) [![Get Pro Edition](https://img.shields.io/badge/Get%20Pro%20Edition-F59E0B?style=for-the-badge)](https://mmo-skill-tree-docs.ziggfreed.com/commercial)

Questions or suggestions? Join the [Discord](https://discord.gg/5NFdZsUxHZ) or leave a comment!

**Support Development:** [Ko-fi](https://ko-fi.com/ziggfreed) | [Buy Me a Coffee](https://buymeacoffee.com/wintergreensolutions)

_MMO Skill Tree is not affiliated with Hypixel Studios or Hytale._
