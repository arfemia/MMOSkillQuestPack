# MMO Skill Quest Pack

A free content pack for [MMO Skill Tree](https://www.curseforge.com/hytale/mods/mmo-skill-tree). It adds two hand-written **questlines** with their own quest-giver NPCs: a starter campaign in the **Emerald Wilds** and a tougher follow-up in the **Howling Sands**, tied together with branching dialogue, campaign achievements, and zone-scoped hunter chains.

Requires the MMO Skill Tree mod (1.4.0+). The pack supplies the content; the mod supplies the quest-giver, dialogue, and zone-scoping engine, so install both.

[![Discord](https://img.shields.io/badge/Discord-Join%20Server-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/5NFdZsUxHZ) [![Ko-fi](https://img.shields.io/badge/Ko--fi-Support-FF5E5B?style=for-the-badge&logo=ko-fi&logoColor=white)](https://ko-fi.com/ziggfreed) [![Buy Me a Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-Support-FFDD00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black)](https://buymeacoffee.com/wintergreensolutions) [![Documentation](https://img.shields.io/badge/Docs-Read%20More-0ea5e9?style=for-the-badge)](https://mmo-skill-tree-docs.ziggfreed.com)

---

[![Host your own Hytale server with Kinetic Hosting](https://i.imgur.com/UHn3FzW.png)](https://billing.kinetichosting.com/aff.php?aff=1262)

---

## What it adds

- **The Emerald Wilds campaign** - eleven quests that start the moment you spawn. Ranger Wren waves you over to her camp by the fires, teaches you to build, hunt, fish, and gather, then sends you against trork warbands and the restless dead before pointing you south. Quartermaster Bramble runs a supply side-arc alongside her.
- **The Howling Sands campaign** - eight tougher quests in the desert. Dunewalker Ashkar appears the first time anyone discovers the Howling Sands and sets you quarrying sandstone, felling desert hardwood, fishing the hidden oases, culling Scarak hives, and finally hunting the apex predators of the dunes.
- **Branching NPC dialogue** - every quest giver has a real conversation, not a single press of a button: progress-aware greetings, lore branches you can ask about, and a one-time bite of camp bread from Wren if you ask nicely.
- **Quest-giver NPCs that come to you** - Wren and Bramble stand at your world spawn automatically; Ashkar appears at the desert's edge the first time it's found. No placing anything, no setup.
- **Campaign achievements** - a capstone for each campaign, a server-first "Campaigner of Orbis" for finishing both, a "Well Met" for introducing yourself to every guide, and zone-scoped hunter ladders that count the creatures you fell in each region.
- **Fully translated** - every quest name and blurb, dialogue line, NPC name, and achievement ships in 9 languages (English, German, Spanish, French, Hungarian, Italian, Brazilian Portuguese, Russian, Turkish); anything a translation misses falls back to English.

## How it works

- **It just starts.** Join a world with the pack installed and Ranger Wren is already at spawn with your first quest. Follow the chain at your own pace - each quest tells you what it needs, tracks your progress live, and hands off to the next.
- **Talk, don't just bump.** Press the interact key on a quest giver and a dialogue opens: pick what to say, accept or turn in quests, ask about the world. The conversation changes as you progress through their questline.
- **The desert opens itself.** You don't have to find Ashkar on a map - the first time any player steps into the Howling Sands, he appears at the threshold, once per world, and never duplicates on restart.
- **Quests scale with you.** The Wilds ease you in; the Sands quests ask for more skill levels before you can take them, so the desert stays aspirational until you've trained up. A few hunts only count in their own region.
- **Shared world, personal progress.** The quest givers are placed once per world for everyone, but each player runs the campaigns on their own quest log.

## Don't want the spawn NPCs?

The quest givers spawn through the same system as the mod's Adventurer's Guide, so the same controls apply:

- **Before anyone joins:** turn quest givers off in the mod's config and they will never spawn.
- **Already in your world?** Run `/mmonpc givers` to see them, then remove the ones you don't want; `/mmonpc reset` lets them spawn again later if you change your mind.
- **Or use the in-game editor:** with creative/builder access, the native entity tool deletes any of them in the world like any other entity.

## Install

1. Install the MMO Skill Tree mod (1.4.0 or newer).
2. Drop `MMOSkillQuestPack-1.0.0.zip` into your server's `Mods/` folder, alongside the mod.
3. Start the server. The questlines and their givers are on by default.

## Make your own quests and dialogue

The questlines are plain pack files built on the mod's template system: a new quest is a few lines (extends a skeleton, names a target and amount), a new quest giver is one small file, and dialogue trees are readable JSON. The pack's repository has the authoring notes, including how to wire a new giver, branch a conversation, and scope an objective to a zone.

## Versions

| Pack  | Plugin | Notes                                                                                                                                                                                                                                                                                                                                                                                                       |
| ----- | ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.0.0 | 1.4.0+ | First release. The Emerald Wilds (eleven quests) and Howling Sands (eight quests) campaigns with three quest-giver NPCs, branching dialogue for each, zone-scoped objectives and hunts, campaign and server-first achievements plus per-zone hunter ladders, and full 9-language translations. Quest givers spawn automatically (Wren and Bramble at world spawn, Ashkar on first Howling Sands discovery). |

---

## Links & Support

[![MMO Skill Tree](https://img.shields.io/badge/CurseForge-MMO%20Skill%20Tree-F16436?style=for-the-badge&logo=curseforge&logoColor=white)](https://www.curseforge.com/hytale/mods/mmo-skill-tree) [![Get Pro Edition](https://img.shields.io/badge/Get%20Pro%20Edition-F59E0B?style=for-the-badge)](https://mmo-skill-tree-docs.ziggfreed.com/commercial)

Questions or suggestions? Join the [Discord](https://discord.gg/5NFdZsUxHZ) or leave a comment!

**Support Development:** [Ko-fi](https://ko-fi.com/ziggfreed) | [Buy Me a Coffee](https://buymeacoffee.com/wintergreensolutions)

_MMO Skill Tree is not affiliated with Hypixel Studios or Hytale._
