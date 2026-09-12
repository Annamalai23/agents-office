# Explorer's Lab — Agents Office v3 setup

This is a **customised Agents Office v3** configured for **exploration, learning and knowledge-building**, with a gamified "earn as you learn" loop. It was cloned from [`ajsahni/agents-office`](https://github.com/ajsahni/agents-office) and the custom roster lives in `office.agents.local.json` (git-ignored, so `git pull` never overwrites it).

## What's here

- **`office.config.local.json`** — sets the business name to **Explorer's Lab**. (Created by hand after `./setup`.)
- **`office.agents.local.json`** — the **Explorer Team**: 11 renamed seats with standing `brief`s, all under the fixed 6-department / 35-seat structure.
- **`brain/90-Knowledge/`** — the learning system: Codex (rules + token loop), Quest Board, Progress, Achievements, and Templates.
- **`brain/Agents Office/skills/learning-quest/`** — a skill bound to the **RESEARCHER** (`riley`) that turns any topic request into a verifiable research note.
- **`brain/CLAUDE.md`** + **`brain/00-Meta/index.md`** — updated so every agent reads the Explorer context first.

## The Explorer Team

| Agent (id) | Role | Department | Does |
|---|---|---|---|
| LEARNING LEAD (mlead) | Learning Ops Lead | marketing | Runs the learning team, quest board, weekly report |
| RESEARCHER (riley) | Topic Explorer | marketing | Deep-dives on a topic, writes a verified note |
| KNOWLEDGE DIGEST (newt) | Weekly Digest | marketing | Turns the week's notes into a skimmable digest |
| INSIGHT HARVEST (iggy) | Social Curation | marketing | Pulls 1 gem/day from feeds worth keeping |
| EXPLAINER (vid) | Explainer | marketing | 90-second explainer script + 3-frame summary |
| LEARNING CO-ORDINATOR (pco) | Quest Board | delivery | Tracks Queued / Doing / Done quests |
| FACT CHECKER (qa) | Verification | delivery | Verifies sources; marks notes ✓ |
| KNOWLEDGE VAULT (cass) | Filing | delivery | Names, tags and links every note correctly |
| TEMPLATE BUILDER (dasst) | Templates | delivery | Maintains the note templates |
| PROGRESS TRACKER (report) | Gamification | ops | Tallies tokens + streak, Friday pulse |
| GAMIFICATION ENGINE (dash) | Achievements | ops | Unlocks badges at 50/200/500/1000 tokens |

The other 24 seats stay as the shipped defaults and are idle (an idle agent costs nothing). Rename any of them later by editing `office.agents.local.json` and restarting.

## How to run

```bash
cd /path/this/repo
./setup          # first time only: checks Node/Claude, installs, builds, boots once
npm start        # → http://localhost:4520
```

You need **Claude Code logged in** (or an `ANTHROPIC_API_KEY`) for the agents to actually do work. Without it the office still boots — you can browse the 3D office, the Brain graph (G), the company board (B), and the API — but tasks that call Claude will error on the model call.

## The earn loop (in action)

1. Type a quest in the bar, e.g. *"research how AI agents coordinate in multi-agent pipelines"*, pick **Marketing**, press **Add**.
2. The router (Sonnet) names the agent → **RESEARCHER**. The task appears in the feed.
3. RESEARCHER runs the `learning-quest` skill → writes `brain/90-Knowledge/Research/YYYY-MM-DD-...`.
4. **FACT CHECKER** verifies it → **+10 tokens**.
5. **KNOWLEDGE DIGEST** rolls it into the weekly digest → **+5 tokens**.
6. **GAMIFICATION ENGINE** unlocks badges → earns you the next level.
7. `revise: …` in any chat records a correction in `brain/Agents Office/feedback/` — standing rules re-apply; one-offs stay one-off.

## Validate any change

```bash
npm run check           # 36/38 offline checks pass (2 are pre-existing demo-mode UI tests)
npm run check:live      # runs one real task + one chat through Claude (needs auth)
```

## Gotchas

- Departments, ids and leads are **fixed** — to add a "kind" of agent, rename a seat in the right department, not add one.
- Routines (scheduled tasks) in v3.6 are limited to **Emails, Accounting, Sales** only.
- Skills go in `brain/Agents Office/skills/<name>/`, **not** the repo's `skills/` folder.
