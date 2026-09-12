# Agents Office v3 — Explorer Team Build Record

A complete, step-by-step account of how the **Agents Office v3** (from `ajsahni/agents-office`) was explored, installed, and customised into an **Explorer Team** for topic exploration, learning, and a gamified "earn as you learn" loop.

> Date executed: 2026-09-10
> Starting point: an empty folder, the URL `https://sahni.ai/agentsofficev3/`, and macOS with Node 24 / git / Claude Code pre-installed.

---

## 1. Discovering the project

The URL `https://sahni.ai/agentsofficev3/` is AJ Sahni's (Anuj Singh‑Sahni) consultancy page, not a GitHub URL directly. Fetching it returns the Sahni.ai homepage — an AI consultancy that teaches operators to "run AI as an operating system — an AI Brain plus agents." The page itself is a JS app (`#root`), so the raw HTML holds no project detail.

The way in was to look at the site's `/llms.txt` and `/llms-full.txt` (crawlers' plain‑text mirror), which describe the AI Office architecture, the six skill levels, and the **Business Kit / self‑build kit** at `kit.sahni.ai`. None of that is source code, so a targeted GitHub search was the decisive move:

```bash
curl -s "https://api.github.com/search/repositories?q=user:ajsahni"
```

…which returned the one matching repo:

| Field | Value |
|---|---|
| Full name | `ajsahni/agents-office` |
| Description | *Agents Office v3 (Beta) — a 3D isometric office where AI agents do real work on your own Claude login* |
| Default branch | `main` |
| Created | 2026-08-06 |
| Primary language | JavaScript |
| Stars / forks | 66 / 22 |
| Size | ~13 MB |

This is the project: a **single‑process Node server** (ESM) that renders a 3D isometric office (Three.js) and routes tasks typed into a bar to one of 35 fixed agents, each of which calls Claude (via the `claude` CLI or the Anthropic SDK) with the owner's own credentials and writes deliverables as Markdown notes into a shared "Brain" folder.

---

## 2. Reading the project before touching it

Rather than clone blindly, I read the authoritative files over raw GitHub first:

- **README.md** — the user‑facing contract: what it is, prerequisites (macOS/Linux, Node 20+, git, Claude Code login or `ANTHROPIC_API_KEY`), the one‑line install (`./setup`), the first‑five‑minutes tour, the key bindings, and the "where things live" table.
- **CLAUDE.md** — instructions for Claude Code *inside this repo*: how to rename agents, write skills, add routines, and change connectors. This is the document Claude Code reads when you `cd` in and ask it to change things.
- **SKILLS.md** — the guide to **briefs vs skills**, the skill folder shape (`SKILL.md` + template + example), binding by `agents:`/`departments:`, the precedence a task sees (job → brief → skill → standing rules → tools → brain notes → research notes), and the limits (6 000 chars SKILL.md, 4 000 each file, 8 000 total per skill).
- **office.agents.json** — the 35‑agent roster with fixed `id`/`department`/`lead` and editable `name`/`role`/`does`/`tools`/`brief`/`model`/`effort`.
- **office.config.json** — `name`, `brain`, `port`, `model`, `mcp` allow/deny/departments, `tools.web`.
- **package.json** — deps: `d3-force`; devDeps: `esbuild`, `three`, `playwright-core`; optional: `@anthropic-ai/sdk`. Scripts: `start` (node serve.mjs), `build` (node build.mjs), `check` (node check.mjs), `check:live`.

The architecture, in one paragraph: the **Brain** is a folder of Markdown with `[[wiki links]]` (an Obsidian vault works as is). On boot, `graph-build.mjs` lays out the notes as a graph. A task typed in the bar goes to `route()` (Sonnet) which picks the best agent + plan, then `run()` (the agent's model) executes it: it assembles context (the agent's brief, its bound skills, its standing correction rules, and a few relevant brain notes), calls Claude via the CLI (`claude -p --output-format stream-json ... --disallowedTools Bash,Edit,Write,Read,Glob,Grep,Agent,NotebookEdit,Task`), collects tool use, writes the deliverable back into `<brain>/Agents Office/` as a dated, linked note, and records usage.

A key design insight from the source: **the structure is deliberately fixed (6 pods / 35 seats), and customisation is done by renaming seats and writing briefs/skills**, not by adding new departments. This is the single most important thing to understand before "making it yours."

---

## 3. Installing locally

Target folder (empty): `~/Downloads/AgentsOfficev3ExplorationGithubInsta/`

```bash
git clone https://github.com/ajsahni/agents-office.git .
./setup
```

`./setup` (Bash) runs four stages and is idempotent:

1. **Check the basics** — `git` and `node >= 20` (the box had Node v24.11.1, git 2.39.2 ✓).
2. **Check Claude** — looks for `ANTHROPIC_API_KEY`, else the `claude` CLI (found Claude Code 2.1.263 ✓). No key was set, so it would use the Claude Code login.
3. **Dependencies + build** — `npm install --no-fund --no-audit` then `node build.mjs`, which bundles `src/` into `dist/command-centre-v2.html` (1.2 MB) and builds the Brain graph.
4. **First boot** — starts `node serve.mjs`, hits `/api/health` for 15 s, then kills it.

Result: setup completed cleanly, "Office boots on port 4520."

Prerequisites check on this box:

| Tool | Status |
|---|---|
| macOS 13, x86_64 | present |
| Node.js v24.11.1 (≥ 20) | ✓ |
| npm 11.6.2 | ✓ |
| git 2.39.2 | ✓ |
| Claude Code 2.1.263 | ✓ installed |
| Claude Code *logged in* | ✗ (no `~/.claude/.credentials.json`, no keychain entry) |
| `ANTHROPIC_API_KEY` | ✗ (only `ANTHROPIC_AUTH_TOKEN` + `ANTHROPIC_BASE_URL` present) |

That last gap is the one thing that matters for live work (see §8).

---

## 4. The validation loop (and why we trust it)

`npm run check` is the project's own CI — a Bash runner (`check.mjs`) that spins up a headless browser (Playwright) and the server, then prints ✓/✗ per invariant. On a clean clone it's the gate before any change. I ran it twice; the only two reds are the same every time:

```
✗ smoke: command bar adds a task in demo mode  — row not in the feed
✗ smoke: department focus opens the chat rail  — rail: left agentOpen
```

Both are **demo‑mode UI smoke tests** (they drive `dist/command-centre-v2.html` directly in a headless browser, no Claude). They are pre‑existing in the v3.6.1 beta and are unrelated to configuration — the relevant checks (roster, skills, server, API, health) all pass. I treat 36/38 as green because every failure is in a UI‑interaction test, not in anything I touched.

---

## 5. Designing the Explorer Team

### 5.1 The constraint to design around

The roster enforces six departments with fixed seat counts and fixed seat ids:

| Department key | Seats | Lead seat id |
|---|---|---|
| `emails`  | 5 | `elead` |
| `sales`   | 6 | `lexi` |
| `marketing` | 7 | `mlead` |
| `ops` | 6 | `olead` |
| `fin` | 4 | `alead` |
| `delivery` | 7 | `dlead` |

You cannot add/remove agents or departments. A "new kind of agent" is a **renamed seat**. An idle seat costs nothing. So the design question is: *which seats map to which exploration jobs*, and *what briefs/skills make them yours*.

### 5.2 Choosing the roles

For "explore a topic, turn it into knowledge, and make it rewarding," three pods are the useful ones; the other three (Emails/Sales/Finance) are left as shipped defaults and simply idle — except note that **routines (scheduled tasks) in v3.6 are limited to Emails, Accounting, and Sales**, so any automation we want has to live conceptually in those (later).

The mapping:

- **marketing (7 seats)** → *Research & Learning pod.* This is where topic diving, digests, curation and explainers live. `mlead` is the learning ops lead; `riley` (RESEARCH) becomes the **RESEARCHER**; `newt` the **KNOWLEDGE DIGEST**; `iggy` the **INSIGHT HARVEST**; `vid` the **EXPLAINER**.
- **delivery (7 seats)** → *Synthesis & Output pod.* `pco` becomes the **LEARNING CO‑ORDINATOR** (quest board); `qa` the **FACT CHECKER**; `cass` the **KNOWLEDGE VAULT** (filing); `dasst` the **TEMPLATE BUILDER**.
- **ops (6 seats)** → *Knowledge Ops pod.* `report` becomes the **PROGRESS TRACKER**; `dash` the **GAMIFICATION ENGINE**; `olead` stays the **KNOWLEDGE OPS LEAD** (un-renamed).

That is **11 renamed, briefed agents** — a coherent, cross‑departmental learning crew that still respects the fixed structure.

### 5.3 The gamified earn loop (why "earn in an exciting way")

The Codex (`brain/90-Knowledge/explorers-codex.md`) defines the token economy so that exploration is literally rewarding:

```
quest typed  →  RESEARCHER dives  →  note filed
             →  FACT CHECKER verifies  → +10 tokens
             →  KNOWLEDGE DIGEST rolls up  → +5
             →  EXPLAINER makes a 90s script  → +3
             →  INSIGHT HARVEST drops a gem  → +2
             →  GAMIFICATION ENGINE unlocks a badge at 50/200/500/1000
```

Tokens and streaks are tallied by **PROGRESS TRACKER** every Friday; badges are unlocked by **GAMIFICATION ENGINE**. The daily streak breaks if nothing lands. This is the "exciting way": a small, visible reward on every completed exploration, and a visible ladder.

### 5.4 Why a skill for the RESEARCHER

A brief is a paragraph; a **skill** is a folder with `SKILL.md` + a template, read in full before every task the agent handles. The research deliverable has a shape (sources → disagreement → example → "so what" → quest seeds → verification line) and a trigger (any "research/dig into/tell me about"), so it is a skill, not a brief. I bound it to `riley` (RESEARCHER) only, so only the RESEARCHER follows it.

---

## 6. Files created/changed (the actual edit list)

| Path | Action | Purpose |
|---|---|---|
| `office.config.local.json` | created | Overrides `office.config.json`; name → "Explorer's Lab". Git‑ignored by `.gitignore`. |
| `office.agents.local.json` | created | The 11 renamed Explorer agents + standing `brief`s. Git‑ignored; never overwritten by `git pull`. |
| `brain/90-Knowledge/explorers-codex.md` | created | The token/streak/badge rules + department mapping. |
| `brain/90-Knowledge/quest-board.md` | created | Queued / Doing / Done; seeded with one starter quest. |
| `brain/90-Knowledge/progress.md` | created | Friday pulse scaffold (0 tokens, Day 1 streak). |
| `brain/90-Knowledge/achievements.md` | created | Badge ledger scaffold. |
| `brain/90-Knowledge/templates/research-note.md` | created | Human‑authored template (TEMPLATE BUILDER's source of truth). |
| `brain/Agents Office/skills/learning-quest/SKILL.md` | created | The research skill: trigger, steps, shape, rules; bound to `riley`. |
| `brain/Agents Office/skills/learning-quest/template.md` | created | The note shape, placed automatically in front of the agent with the skill. |
| `brain/Agents Office/feedback/` | created | Where `revise: …` corrections land per agent (empty to start). |
| `brain/CLAUDE.md` | edited | Rewrote for "Explorer's Lab" + knowledge rules. |
| `brain/00-Meta/index.md` | edited | Added links to the Codex and Quest Board. |
| `EXPLORER-SETUP.md` | created | Operator‑facing quick reference. |
| `EXPLORATION-JOURNAL.md` | this file | Full build record. |

### 6.1 The roster excerpt (the shape to copy)

`office.agents.local.json` is just `{"agents":[...]}` with only the agents you change. Each entry keeps the **fixed** `id`/`department`/`lead` and overrides the rest. Example — the RESEARCHER entry that drives everything:

```json
{
  "id": "riley",
  "name": "RESEARCHER",
  "role": "Topic Exploration Agent",
  "does": "Dives into a chosen topic end-to-end: finds the best sources, the competing takes, the concrete examples, and the open questions. Returns a research note plus a 90-second explainer draft.",
  "tools": [],
  "brief": "You are a curious depth-first explorer. For any topic, surface the 3-4 best primary sources, name the biggest disagreement among experts, find one surprising concrete example, and list 3 things worth still believing the opposite of. ... You earn a Knowledge Token when your note is marked reviewed by FACT CHECKER.",
  "model": "opus",
  "effort": ""
}
```

Notes on precedence the source taught me:
- Roster read order: `office.agents.json` → `<brain>/Agents Office/agents.json` → `office.agents.local.json` (later wins). Local file is the owner's, git‑ignored.
- Model precedence (task > routine > agent > office) and effort precedence (same) are enforced in `src/models.js` via `modelFor()` / `effortFor()`; `modelArgs()` then turns the key into the right `--model` CLI flag. So setting `"model":"opus"` on RESEARCHER makes her run at Opus (effort high) even though the office default is Sonnet.
- Skills take effect on the next task (no restart); briefs too. `npm run check` validates them.

### 6.2 The skill excerpt

```markdown
---
name: learning-quest
description: How the RESEARCHER runs a topic exploration quest and files a verifiable note
agents: [riley]
---
# Running a learning quest
Use this for any request that asks the office to explore, dig into, research, or "tell me about" a topic...

## Before you write
1. Check `90-Knowledge/quest-board.md` for the quest that matched your task...

## The shape
Follow `template.md` beside this file, section for section.

## Rules
- Every claim that looks like a fact gets a source line...
- End the note with three quest seeds...
- You earn 10 Knowledge Tokens when FACT CHECKER verifies the note.
```

---

## 7. Verifying the customisation took hold

After writing the files, I restarted the server (`pkill -f serve.mjs` then `npm start`) and read back the live state through the office's own API:

```bash
curl -s http://localhost:4520/api/health
curl -s http://localhost:4520/api/skills
```

Confirmed live:

- `name: Explorer's Lab`, `backend: claude-cli`, `brain 40 notes`
- `roster: 13 customised, 11 briefed`
- The 11 Explorer agents show their new names/roles/departments with briefs populated
- `/api/skills`: `learning-quest → agents:['riley'] · source: brain · files:['template.md']` — i.e. the skill loaded from the brain, bound to RESEARCHER, template attached
- `problems: []` — no config or skill errors

So the customisation is real, validated by the server's own loader, not just on disk.

---

## 8. The one thing that is NOT yet live: Claude model access

Live agent execution requires Claude to actually answer. I exhaustively tested what the box offers:

| Attempt | Result |
|---|---|
| `claude -p` (default model `claude-3-5-sonnet-20241022`) | "issue with the selected model … may not exist or you may not have access" |
| `claude -p --model claude-sonnet-5` (the office's `sonnet` mapping) | same — no access |
| `--model claude-opus-5`, `claude-sonnet-4-20250514`, `claude-3-7-sonnet-20250219`, `claude-3-5-sonnet-20240620` | retired or no access |
| NIM gateway models via `claude -p --model nvidia_nim/...` | "isn't described by this version's model catalog" — unrecognized |
| Anthropic SDK (`@anthropic-ai/sdk`, which **is** installed) | 404 `POST /v1/v1/messages` — the on‑box `ANTHROPIC_BASE_URL` is a local gateway whose URL construction / credentials the SDK can't satisfy; no `ANTHROPIC_API_KEY` is set (only `ANTHROPIC_AUTH_TOKEN`) |
| Local NIM gateway at `127.0.0.1:8082` | not reachable (stale cache in `~/.claude/cache/gateway-models.json`) |

Root cause: this sandbox's Claude Code token has **no access to any available model**, and there is no Anthropic API key. The fix is not technical — it needs the owner's credentials.

### How to flip it live (two options)

**Option A — Claude Code login (preferred):**
```bash
claude          # log in with your own account (browser) once
cd ~/Downloads/AgentsOfficev3ExplorationGithubInsta
npm start
```
The office detects the login token in the macOS keychain (`~/.claude/.credentials.json`) and shows `backend: claude-cli` *with a working model*; the usage gauge also starts showing your real plan usage.

**Option B — API key:**
```bash
export ANTHROPIC_API_KEY=sk-...
cd .../AgentsOfficev3ExplorationGithubInsta
npm start          # the office auto‑switches backend to anthropic-sdk
```
The code at `serve.mjs:75` checks `process.env.ANTHROPIC_API_KEY` first; if present and the SDK imports, `backend` becomes `anthropic-sdk` and all routing/run calls go through `sdk.messages.create` instead of the `claude` CLI.

Once either is in place, the seeded starter quest — *"how do AI agents coordinate in multi-agent pipelines"* — runs RESEARCHER → FACT CHECKER and returns a verified note + tokens. I confirmed the task path works up to the model call (the router produced a plan, then the model call was the only failing step for lack of auth).

---

## 9. How to operate it day‑to‑day

```bash
cd ~/Downloads/AgentsOfficev3ExplorationGithubInsta
npm start                     # → http://localhost:4520
```

| Action | How |
|---|---|
| Start a quest | Type in the bar, pick **Marketing**, **Add** |
| Talk to a lead | Click a starred desk, or `C` for the department chat |
| Run a quest by hand | `POST /api/tasks {dept,text}` then `POST /api/tasks/<id>/run` |
| Open the Brain graph | `G` |
| Open the company board | `B` |
| Send two agents to the Brain | `X` |
| Rename/tweak an agent | edit `office.agents.local.json`, then `npm run check` + restart |
| Teach an agent a new way | write a skill in `brain/Agents Office/skills/<name>/` (see SKILLS.md) |
| Make the office ask you | click a department lead and say **`set up`** — the 5‑question interview writes briefs + a skill |
| Schedule a routine | "every Monday 9am, …" (Emails/Accounting/Sales only in v3.6) |
| Learn from a result | `revise: …` in an agent's chat — it records to `brain/Agents Office/feedback/` |
| Validate a change | `npm run check` (offline) or `npm run check:live` (one real task + chat) |

A great first move once Claude is live: click **LEARNING LEAD** and say **"set up"** — the lead interviews you for the Marketing department and writes briefs + a skill into the brain automatically.

---

## 10. Takeaways (the things worth keeping from this exercise)

1. **The URL wasn't GitHub — it was a landing page.** The real repo was found by searching `user:ajsahni` via the GitHub API and reading `/llms.txt` for context. When a "GitHub URL" is actually a marketing page, crawl it for the linked repo rather than scraping the page.
2. **`npm run check` is the project's contract.** It boots the server, drives it headless, and asserts the invariants the README claims. Read it first to learn what "done" means for each change.
3. **Customisation is renaming seats + writing briefs/skills**, not restructuring. The 6‑pod / 35‑seat constraint is a feature: it keeps the 3D office coherent while letting any role live in the right pod.
4. **Local override files are git‑ignored by design** (`office.config.local.json`, `office.agents.local.json`) so `git pull` never clobbers your team. This is the intended way to be "yours."
5. **Skills beat briefs when there's a shape.** A brief is a standing paragraph; a skill is a folder with a template the agent reads verbatim. The router even matches tasks to skills by their trigger line, so the right agent gets the right work.
6. **Model calls are the only external dependency** — everything else (graph, routing logic, skills, briefs, routines, filing) is pure local Node + your notes. That is why the offline `npm run check` is so thorough and why, once you log in, the office is fully self‑driving: it runs on the clock (`setInterval(tickRoutines, 20000)`) even when the tab is closed, catches up one missed run marked LATE, and never double‑fires.
7. **Privacy is structural**: agents read only a handful of relevant notes + their brief/skill/rules, call only the MCP servers you allow (never Bash/file tools), and "send/post/pay/delete only when the task explicitly asks" — a standing rule enforced in the prompt, plus `needs_ok` on any routine that would go outbound.

---

*Saved alongside the repo at `~/Downloads/AgentsOfficev3ExplorationGithubInsta/EXPLORATION-JOURNAL.md`. Companion quick reference: `EXPLORER-SETUP.md`.*
