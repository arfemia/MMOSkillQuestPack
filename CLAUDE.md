# Zone Quests Pack

Zone campaigns (Wilds, Sands) and the Brood Queen encounter. The family-wide rules apply here; this file adds only what is specific to this pack.

- The desert arc is held: the six `_Sands` quests and the `Howling_Sands_Campaign` and `Orbis_Campaigner` achievements ship `Enabled` false, and flipping those eight leaves opens it. The Brood Queen fight itself is live.
- A `_`-marked folder prefixes every id beneath it, so renaming `_Wilds` or `_Sands` renames every quest id under it and restarts players mid-campaign.
- Generated quests take no folder prefix: each generator row spells the full `wilds_`/`sands_` id.
- The Kweebec arc gates on `hytale:mod_installed` with `Min` 1, and `KweebecNightmare` sits under manifest `OptionalDependencies`, never `Dependencies`.
- Campaign level gates read `MMO_TotalLevel` (all skills summed), not the highest skill; the desert trade quests gate on their own skill.
- Pick `MatchMode` against the real id family: `CONTAINS` for families (`Wood_<Species>`, a bare fish name; wood targets are `Wood_<Species>_Trunk*` substrings), `EXACT` where crafted variants share the stem (`Rock_Sandstone`). Re-verify ids against `shared-source/release/HytaleAssets/Server/**` (id = filename).
- The Brood Queen is `CONTENT_PACKS.md`'s worked encounter example; an owner installs it once per world by pasting the prefab and running `/zigencounter spawn Sands_Brood_Queen_Encounter`.
