# Chronicle & Forge — TTRPG Suite Architecture
### North Eden Tabletop Roleplaying System

---

## Vision

A complete indie TTRPG creation and play suite built into Chronicle & Forge. Design your rulebook, build character sheets, populate your bestiary, run sessions, and playtest everything — all in-app, all in your aesthetic, all saved to localStorage.

This is not an attempt to replicate RPG Maker or a commercial VTT. It's a focused, creator-first toolkit for indie zine-scale TTRPGs (think: 2d6 systems, 12–32 page zines, North Eden world content).

---

## Module Map

The existing `rpg.html` becomes a **hub dashboard** for the suite. All sub-modules are separate HTML files, each under 700 lines.

```
/
├── rpg.html                  ← TTRPG Hub — overview, links to all sub-modules
│
├── rpg-rules.html            ← Rulebook / Zine Editor
├── rpg-characters.html       ← Character Sheet Designer + Player Sheets
├── rpg-bestiary.html         ← Creatures, NPCs, Stat Blocks
├── rpg-campaign.html         ← Campaign / Session Tracker
└── rpg-play.html             ← Playtest Table — dice, initiative, combat, live play
```

---

## Module Breakdown

### `rpg.html` — TTRPG Hub (~300 lines)
The entry point. Shows:
- Your active game system (name, tagline, quick stats)
- Cards linking to each sub-module
- Recent sessions summary
- Word/content counts (pages of rules, # of creatures, # of characters)
- Quick dice roller (for casual use without entering playtest)

**Status:** ☐ Not started

---

### `rpg-rules.html` — Rulebook / Zine Editor (~650 lines)
Create and edit your game's rulebook in structured sections.

**Features:**
- Sections: Introduction, Character Creation, Core Mechanic, Skills/Abilities, Combat, Equipment, Bestiary Reference, Scenario
- Rich text editing per section (bold, italic, headers, lists)
- Page count estimator (targets: 8-page, 16-page, 24-page, 32-page zine formats)
- Export to print-ready layout view (formatted, paginated preview)
- Dice notation support: inline `2d6+STR` tags that link to dice roller
- Rule snippets library (reusable boxes: e.g. "On a 10+, you succeed fully...")
- Version history per section (last 3 saves)

**Data key:** `chronicle_ttrpg.rules`

**Status:** ☐ Not started

---

### `rpg-characters.html` — Character Sheet Designer + Sheets (~700 lines)

Two modes:

**Designer Mode** (game creator):
- Define your character sheet fields (stat names, types: number / text / checkbox / select)
- Define stat ranges and defaults
- Set up derived stats (e.g. HP = CON + 5)
- Preview the sheet as it will appear to players
- Save as the "official" sheet template for the game

**Player Mode** (during play):
- Fill out a sheet using the designed template
- Multiple characters saved (players 1–6, or NPCs)
- Level up / stat change tracking
- Inventory list with weight/slots
- Notes field per character
- Condition markers (injured, exhausted, poisoned, etc.)
- XP tracker

**Data keys:** `chronicle_ttrpg.sheet_template`, `chronicle_ttrpg.characters`

**Status:** ☐ Not started

---

### `rpg-bestiary.html` — Creatures, NPCs, Stat Blocks (~500 lines)
Your monster manual and NPC roster.

**Features:**
- Create creature/NPC entries with: name, description, stat block, special abilities, loot table, notes
- Stat block template matches your game's stat system
- Tags: creature / NPC / boss / faction / friendly
- Search and filter
- "Send to encounter" button (pushes creature to playtest table)
- Illustration slot (upload or link image)
- Print view for individual stat blocks

**Data key:** `chronicle_ttrpg.bestiary`

**Status:** ☐ Not started

---

### `rpg-campaign.html` — Campaign / Session Tracker (~500 lines)
Track your game's story across sessions.

**Features:**
- Campaign overview: title, setting, premise
- Session log: date, title, summary, participants, outcome
- NPC relationship tracker (linked from bestiary)
- Faction status tracker
- Player notes vs GM notes (toggle visibility)
- World timeline (add events with dates in your world's calendar)
- Hooks and threads (unresolved plot threads with status: open / resolved / abandoned)
- Session prep checklist

**Data key:** `chronicle_ttrpg.campaign`

**Status:** ☐ Not started

---

### `rpg-play.html` — Playtest Table (~700 lines)
The live play interface. This is where you actually run the game.

**Features:**

*Dice System:*
- Full dice set: d4, d6, d8, d10, d12, d20, d100
- Custom pools: e.g. `2d6+2`, `3d8 keep highest 2`
- Roll history with labels ("Attack roll — Goblin", "Save vs poison")
- Advantage/disadvantage toggle (roll twice, take high/low)
- Quick-roll buttons configurable per game system

*Initiative & Turn Tracker:*
- Add combatants from character list or bestiary
- Set initiative values, auto-sort
- Track HP in real time, status conditions
- Round counter
- Visual "current turn" highlight

*Session Tools:*
- GM screen panel: quick reference for core rules
- Ambient mood: label text (e.g. "Dungeon — Tense", "Town — Relaxed") — cosmetic
- Session timer
- Quick NPC generator (pull from bestiary or roll random traits)
- Loot roller (define tables, roll results)

*Scene Notes:*
- Scratchpad for current encounter
- Reference linked bestiary entries inline

**Data key:** `chronicle_ttrpg.sessions` (live session state, auto-saved)

**Status:** ☐ Not started

---

## Shared Data Structure

All TTRPG data lives under `chronicle_ttrpg` in localStorage:

```js
chronicle_ttrpg = {
  meta: {
    gameTitle: "North Eden",
    tagline: "...",
    diceSystem: "2d6",       // primary resolution mechanic
    created: timestamp,
    lastModified: timestamp
  },
  rules: {
    sections: [
      { id, title, content, order, wordCount }
    ],
    version: 1
  },
  sheet_template: {
    stats: [ { id, label, type, defaultValue, min, max } ],
    derived: [ { id, label, formula } ],
    sections: ["core", "skills", "inventory", "notes"]
  },
  characters: [
    { id, name, player, stats: {}, inventory: [], notes, conditions: [], xp }
  ],
  bestiary: [
    { id, name, type, tags, description, stats: {}, abilities: [], loot: [], imageUrl }
  ],
  campaign: {
    title, premise,
    sessions: [ { id, date, title, summary, participants, outcome } ],
    factions: [ { id, name, status, notes } ],
    timeline: [ { id, worldDate, event } ],
    threads: [ { id, title, status, notes } ]
  }
}
```

---

## Dice System Flexibility

The suite is designed to support any dice-based system, not just 2d6. During initial setup (or in Settings), you define your game's primary resolution mechanic:

| System | Description |
|--------|-------------|
| 2d6 | Indie zine standard. Results 2–12, soft target numbers |
| d20 | D&D-style. Roll d20 + modifier vs DC |
| d6 pool | World of Darkness style. Roll X d6s, count successes |
| Custom | Define your own result table |

The playtest dice roller and rule reference adapt to whichever you choose.

---

## Build Phases

### Phase A — Foundation
- [ ] `rpg.html` hub rebuild (hub dashboard, links, quick dice)
- [ ] `rpg-play.html` dice roller only (prove the dice system works)

### Phase B — Creation Tools
- [ ] `rpg-rules.html` rulebook editor
- [ ] `rpg-characters.html` sheet designer + player sheets

### Phase C — World Content
- [ ] `rpg-bestiary.html` creature/NPC builder
- [ ] `rpg-campaign.html` session and campaign tracker

### Phase D — Full Playtest
- [ ] `rpg-play.html` full playtest table (initiative, HP, encounter tools)
- [ ] Cross-module linking (bestiary → playtest, characters → playtest)

### Phase E — Polish
- [ ] Export rulebook to print-preview layout
- [ ] Character sheet print view
- [ ] Data backup/restore integration with main Chronicle system

---

## Module Size Targets

| Module | Est. Lines | Actual | Status |
|--------|-----------|--------|--------|
| rpg.html (hub) | ~300 | 340 | ✅ Complete |
| rpg-rules.html | ~650 | 425 | ✅ Complete |
| rpg-characters.html | ~700 | 479 | ✅ Complete |
| rpg-bestiary.html | ~500 | 373 | ✅ Complete |
| rpg-campaign.html | ~500 | 483 | ✅ Complete |
| rpg-play.html | ~700 | 533 | ✅ Complete |
| **Total** | **~3,350** | **2,633** | ✅ All done |

No single file exceeds 700 lines. Each can be edited independently.

---

## Design Standards

Inherits Chronicle & Forge's RPG-tome aesthetic:
- Dark backgrounds: `#0d0d0f` → `#1a1a24`
- Gold accents: `#c9a84c` / `#e8c97a`
- Cinzel (headers) + Crimson Pro (body text)
- Gold-bordered cards, left accent bars
- Bottom or side navigation within sub-module where needed

The rulebook editor should feel like writing in a grimoire.
The playtest table should feel like a war room.

---

## Open Questions (Decide Before Building)

1. **Primary dice system for North Eden?** (2d6 recommended for indie zine scope)
2. **Stat names?** What are your core character stats? (STR/DEX/CON style, or custom?)
3. **Sheet structure?** How many stats, any derived values, skill list?
4. **Do you want the map tile editor** (from the earlier conversation) integrated into `rpg-play.html`, or as a separate `rpg-map.html` module?
5. **Export format?** PDF-style print view, or just on-screen reference?

---

*Document version: 1.0 — Pre-build planning*
*Last updated: February 2026*
