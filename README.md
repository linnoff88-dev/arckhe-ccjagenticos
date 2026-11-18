# Claude OS

![Claude OS â€” Unlimited Power: Agentic OS with data, context, agents and automation working together](doc/cover.png)

## O que Ã©

**Claude OS Ã© um painel pessoal, read-only, que conecta tudo que vocÃª usa de IA em uma tela sÃ³.**

VocÃª paga Claude, ChatGPT, OpenRouter, Pinecone, Obsidian, OpenClaw â€” mas nÃ£o sabe quanto usa, quanto gasta, quanto economiza, ou o que melhorar. O Claude OS lÃª os arquivos que essas ferramentas jÃ¡ deixam no seu computador e responde isso em um dashboard Ãºnico.

**Em uma frase:** seu agentic OS â€” dados, contexto, agentes, skills, memÃ³ria, seguranÃ§a, hooks e automaÃ§Ãµes trabalhando juntos.

### O que ele te mostra

- **Quanto vocÃª gastou** em IA nos Ãºltimos 7 dias (valor equivalente em API)
- **Quanto vocÃª economizou** com skills (invocaÃ§Ãµes Ã— tempo Ã— sua hora)
- **ROI lÃ­quido** = economia âˆ’ gasto, com Operator Score
- **Grafo 3D da sua memÃ³ria** (Obsidian + memÃ³rias do Claude Code + Pinecone)
- **Dream diÃ¡rio Ã s 7h** â€” uma IA olha suas Ãºltimas 24h e prescreve as 4 aÃ§Ãµes de maior impacto pra hoje
- **Skills, integraÃ§Ãµes, modelos** â€” Opus/Sonnet/Haiku, plugins, MCP servers, vector indexes

### Para quem

Operadores que rodam um stack multi-ferramenta (Claude Code + ChatGPT + Codex + Obsidian + Pinecone + â€¦) e querem ver, em uma tela, **quanto disso tudo estÃ¡ realmente valendo a pena**.

### Como funciona, em alto nÃ­vel

```
suas pastas locais     â†’    aggregator script    â†’    live-data.json    â†’    dashboard
(~/.claude/, Obsidian,      (Ãºnico leitor de            (gitignored,           (localhost:8081)
 Pinecone, OpenRouter)      disco, anonimiza)           sÃ³ fica na sua mÃ¡quina)
```

Nada sai do seu computador. Sem telemetria. As Ãºnicas chamadas externas sÃ£o pra APIs que **vocÃª** configurou.

---

> ## ðŸŽ macOS-only project
>
> Claude OS is **built and tested exclusively for macOS**. Core features depend on macOS-specific APIs:
>
> - **Keychain credential detection** (`security dump-keychain`) â€” subscriptions / auth-mode tile
> - **`dscl` name lookup** â€” auto-redaction of the operator's full name
> - **`launchd` plist** â€” daily Dream cron (`bun run install-dream`)
> - **`/Applications` + `~/Library/Mobile Documents/`** â€” Obsidian iCloud vaults and `.app` discovery
>
> On **Linux/Windows** the aggregator silently degrades: 0 keychain creds, no name auto-redaction, no auto-cron, no `.app` detection. Project sessions and Pinecone indexes still aggregate, but the dashboard will be incomplete and **anonymization is weaker than advertised**. See [`REVIEW.md`](./REVIEW.md) Â§2 for the full list of degraded signals before running on non-macOS.
>
> If you must run on Linux, follow the manual `crontab` snippet printed by `bun run install-dream` and accept the gaps.

---

> ## âš ï¸ Read `REVIEW.md` before running
>
> A technical critique covering known gaps in privacy redaction, cron reliability, the `Run this fix â†’` execution path, and architectural debt lives at [`REVIEW.md`](./REVIEW.md). Skim the **Top 3** section at minimum before you point this at your real `~/.claude/` data.

---

Claude OS is for the operator running a multi-tool stack â€” Claude Code, ChatGPT / Codex, OpenRouter, Pinecone, Obsidian, OpenClaw â€” who has no honest way of knowing how much they actually use any of it. Claude OS reads what's already on your disk, never writes to source tool data directories, and renders one scrollable dashboard with subscriptions, token spend, skills ROI, a 3D memory graph, and a daily **Dream** review that prescribes the four highest-impact things to fix today.

The Dream prescription engine is the headline. Everything else is context.

> _Screenshot once you've run the aggregator â€” drop a 1200Ã—630 image at `docs/screenshot.png`._

---

## Install in 60 seconds â€” let Claude do it (macOS)

Open **Claude Code** in any terminal. Paste this prompt exactly as-is:

```
Install Claude OS for me. Ask once for permission, then run all four
commands in order without further prompts:

1. git clone https://github.com/ItsssssJack/claude-operating-system.git ~/code/claude-os
2. cd ~/code/claude-os && bun install
3. cd ~/code/claude-os && bun run setup
4. cd ~/code/claude-os && bun run dev

Step 3 (`bun run setup`) does the heavy lifting on my behalf:
- Scans my machine for what it natively supports (~/.claude/,
  Obsidian vaults under ~/Documents and ~/Desktop, ~/.codex/,
  Pinecone if PINECONE_API_KEY is set, OpenRouter if its key is
  set, MCP servers, scheduled-task registries)
- Copies the /dream skill into ~/.claude/skills/dream
- Installs the daily Dream cron (macOS launchd)
- Drops a marker so the wizard auto-pops on first open
- Tells me which env keys would unlock more

If step 3 reports missing API keys I might want, ASK ME for each one
before continuing. For each key I give you:
   echo "<KEY_NAME>=<value>" >> ~/code/claude-os/.env.local
then re-run `bun run scripts/aggregate.ts` once.

If macOS prompts for a Keychain password during step 3, tell me to
enter my login password â€” that's macOS, not you.

Step 4 (`bun run dev`) stays running and pops the 7-step wizard in
my browser.

Once the wizard is open, send this line:

  âœ¨ Claude OS is ready. The wizard is open in your browser â€” walk
  the 7 steps and click Save at the end.

Then become my onboarding guide. DO NOT assume what's in my stack â€”
ASK me. Cover at least these four areas:

- Note / memory tools: Obsidian, Notion, Roam, Logseq, Apple Notes,
  Mem.ai, Bear, plain markdown folders, anything else
- Vector databases / knowledge stores: Pinecone, Weaviate, Qdrant,
  Chroma, pgvector, Mem0, Letta, Cognee, custom Postgres, etc.
- LLM / agent platforms beyond Claude Code: Cursor, Continue,
  Aider, Cody, Copilot, OpenAI direct, OpenRouter, Mistral, Groq,
  Replicate, Together
- Automation / cron systems: macOS launchd, cron, systemd, Zapier,
  n8n, Make, Cowork tasks, Codex automations

For each thing I name, be honest about three buckets:

A) AUTO-DETECTED â€” Claude OS already saw it (Obsidian vaults under
   default paths, ~/.claude memory, Pinecone via API key,
   OpenRouter via API key, ~/.codex/, MCP servers, Cowork tasks,
   Codex automations, launchd plists). Confirm it's in the
   dashboard and walk me through extra wiring (e.g.
   CLAUDE_OS_OBSIDIAN_PATH for vaults outside the default).

B) NEEDS WIRING â€” supported but the auto-scan needs a key, env
   var, or path I haven't given yet. Walk me through adding it
   to ~/code/claude-os/.env.local then re-running
   `bun run scripts/aggregate.ts`.

C) NOT YET SUPPORTED â€” say so plainly (e.g. Notion, Roam,
   Weaviate, Qdrant, Mem0, Cursor, Continue aren't in the native
   scanner today). Offer concrete workarounds: export to markdown
   the auto-scan picks up, drop a CLAUDE.md the tool can read,
   stash credentials in .env.local for a future version, or just
   log it as a known gap.

Also explain the daily Dream review: /dream reads my last 24h of
activity and writes 4 prescriptions to ~/.claude-os/dreams/ every
morning at 7am. Ask whether I want to run it now, change the
schedule, or skip it.

Lead with: "I can guide you through this and connect the data
specific to your computer â€” just tell me what's in your stack and
I'll show you what Claude OS already sees, what needs a quick
wire-up, and what isn't supported yet so we can plan around it."

Then wait for me to pick. Don't mention URLs, ports, or technical
details unless I ask.
```

Claude Code asks once for permission, you say yes, and ~60 seconds later your browser is on the wizard with the dashboard already populated by your real machine data. The wizard is a pure browser experience â€” no backend processes, no race conditions, no "Activate" timing out.

If you don't have Claude Code, [scroll to the manual install](#manual-install) below.

---

## What it shows

The dashboard is one long scroll. Top to bottom:

- **KPI strip** â€” _Spent on AI_ (last 7d API-equivalent value), _Skills saved_ (invocations Ã— time Ã— hourly rate), _Net ROI_ (saved minus spent). Plus an **Operator Score pill** that grades the health of your stack at a glance. Source: `~/.claude/projects/**/*.jsonl`.
- **Currently in** tile â€” the project you most recently worked in, with last-active timestamp. Source: JSONL `cwd` + timestamps.
- **Subscription strip** â€” four tiles for Claude / ChatGPT / OpenRouter / OpenClaw with the ROI multiplier on each (`$X extracted from $Y sub`). Source: macOS Keychain heuristic + `~/.codex/auth.json` + `~/.openclaw/openclaw.json` + shell env.
- **Usage panel** â€” the 5h Claude window bar, ChatGPT window, live OpenRouter balance, and the model split (Opus / Sonnet / Haiku / others). Source: JSONL `usage` blocks + `https://openrouter.ai/api/v1/auth/key`.
- **Skills + Skill recommender** â€” most-used skills, last activation, recommendations for new skills based on repeated manual sequences. Source: `~/.claude/skills/*/SKILL.md` + JSONL grep.
- **Memory graph** â€” 3D force-graph across Obsidian, `~/.claude/projects/*/memory/`, and Pinecone. Multi-source filter chips. Source: walked from disk + Pinecone API.
- **Integrations + Vector indexes** â€” installed plugins, MCP servers, and Pinecone indexes (the latter feed the memory graph as another memory source). Source: `~/.claude/plugins/installed_plugins.json` + Pinecone.
- **Automations** â€” scheduled Claude Code tasks. Source: `~/.claude/tasks/`. (Cron + launchd plist parsing is on the roadmap.)
- **Dream panel** â€” four prescription cards (memory / cost / skills / workflow) with a `[Run this fix â†’]` button that sends the prescription ID to the local sidecar, which resolves it from the latest Dream JSON and runs the stored `claude -p` command. If the sidecar is offline, the UI falls back to copying the command. Source: `~/.claude-os/dreams/dream-{date}.json`.
- **/share route** â€” a 1200Ã—630 OG card for Twitter / LinkedIn. Open `http://localhost:8081/share`, take a screenshot.

---

## Honest "what's real today"

### Works out of the box

The moment you `bun run dev`:

| Section                                                        | Notes                                                                   |
| -------------------------------------------------------------- | ----------------------------------------------------------------------- |
| Aggregator (Claude / Codex / OpenRouter / Pinecone / Obsidian) | macOS only â€” auto-runs from the setup wizard                            |
| Subscription detection                                         | OAuth Keychain heuristic                                                |
| Memory graph                                                   | Live data flows in once the aggregator runs                             |
| Skills (active)                                                | Real `skills.active` from JSONL parse                                   |
| Sample Dream cards                                             | Four sample prescriptions render straight from `live-data.example.json` |
| One-click setup wizard activation                              | The local sidecar boots alongside Vite via `bun run dev`                |
| One-click `[Run this fix â†’]`                                   | Same â€” backed by the bundled sidecar                                    |
| `/share` OG card                                               | Take a screenshot of the 1200Ã—630 frame                                 |

### Needs one extra step

| Feature                                    | What's needed                                 | Command                                                                                               |
| ------------------------------------------ | --------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| Real Dream prescriptions (vs. sample)      | Install the `/dream` skill + a daily 7am cron | `mkdir -p ~/.claude/skills && cp -r skills/dream ~/.claude/skills/dream` then `bun run install-dream` |
| Live OpenRouter balance / Pinecone indexes | Add API keys to `.env.local`                  | see [Connecting to everything](#connecting-to-everything)                                             |
| Skill recommendations                      | Pattern-detection v0 â€” manual review for now  | n/a                                                                                                   |

### Aspirational

| Feature                | Status                                             |
| ---------------------- | -------------------------------------------------- |
| `bun create claude-os` | ðŸ”´ Package not yet on npm â€” clone the repo for now |

---

## Architecture

```
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”    bun run aggregate     â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚ ~/.claude/           â”‚ â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â–¶ â”‚ live-data.json     â”‚
â”‚ ~/.codex/            â”‚                          â”‚ (gitignored, real) â”‚
â”‚ ~/.openclaw/         â”‚                          â”‚ live-data.example  â”‚
â”‚ Obsidian vaults      â”‚                          â”‚ (committed, demo)  â”‚
â”‚ Pinecone API         â”‚                          â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
â”‚ OpenRouter API       â”‚                                    â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜                                    â–¼
                                                 â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
                                                 â”‚ TanStack Start app â”‚
                                                 â”‚ (port 8081)        â”‚
                                                 â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```

**One rule:** the frontend never reads `~/.claude/` directly. The aggregator script is the only reader on disk. The frontend reads `live-data.json` plus the most recent `~/.claude-os/dreams/dream-{date}.json`. Everything else is derived.

---

## Manual install

If you're not letting Claude run the commands, the path that works for any stranger on macOS.

**Prerequisites**

- **macOS** (officially supported platform â€” Linux/Windows are best-effort and run in degraded mode, see [Linux / Windows](#linux--windows) and [`REVIEW.md`](./REVIEW.md) Â§2)
- [Bun](https://bun.sh) â‰¥ 1.0
- Claude Code installed and used at least once (so `~/.claude/projects/` exists)

**Clone and run**

```bash
git clone https://github.com/ItsssssJack/claude-operating-system.git
cd claude-os
bun install
bun run dev
```

That's it. `bun run dev` starts the Vite dev server on **http://localhost:8081** _and_ the local sidecar on **127.0.0.1:17873** in the same terminal â€” no second window required. Open the browser, walk the setup wizard, click **Activate now**, and the dashboard populates with your real machine's data.

If you'd rather scan from the command line, you can still run `bun run scripts/aggregate.ts` directly. The wizard does this for you on Step 2 and again on the final "Activate now" click.

---

## The setup wizard

The setup wizard lives at `http://localhost:8081/setup`. A short Welcome pre-roll opens it; the 7 numbered steps that follow are what the Stepper at the top tracks:

| #   | Step          | What it does                                                                                                                                                                                                                                                                                       |
| --- | ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| â€”   | Welcome       | Animated pre-roll + value prop. Not numbered.                                                                                                                                                                                                                                                      |
| 1   | Make it yours | Optional avatar (file picker â†’ 256Ã—256 base64 in localStorage)                                                                                                                                                                                                                                     |
| 2   | Detect tools  | Probes for Claude Code / Desktop / VS Code / AntiGravity / Codex / Gemini / Anthropic API / OpenAI API / OpenRouter / OpenClaw. On a fresh clone, auto-runs the aggregator via the sidecar (`POST /api/aggregate`) the first time you land on this step, then reloads with real detection results. |
| 3   | Memory        | Shows Pinecone / Obsidian / Notion / Logseq detection, lists detected Obsidian vaults, and lets you paste a vault path if detection misses it.                                                                                                                                                     |
| 4   | API keys      | Captures optional API keys for OpenRouter, Anthropic, OpenAI, KIE, Pinecone, and Notion.                                                                                                                                                                                                           |
| 5   | Time value    | Set your hourly rate so skills ROI is denominated correctly ($150/hr default)                                                                                                                                                                                                                      |
| 6   | Dream cadence | Daily or weekly schedule, morning or evening                                                                                                                                                                                                                                                       |
| 7   | You're set    | One-click "Activate now" via the local sidecar â€” writes the config, then re-runs the aggregator so the dashboard has fresh data when you land.                                                                                                                                                     |

The wizard's final action is **one click** â€” "Activate now" hands the collected state to the local sidecar, which writes `~/.claude-os/config.json` and the repo-root `.env.local`, then triggers the aggregator so the dashboard is hydrated by the time you land on it. The three-command copy-paste fallback only appears if the sidecar can't be reached.

---

## Connecting to everything

The aggregator and dashboard auto-detect what they can. Anything that needs an API key goes in `claude-os/.env.local`:

```bash
# claude-os/.env.local
PINECONE_API_KEY=...
OPENROUTER_API_KEY=...
ANTHROPIC_API_KEY=...        # optional, only if you use raw API mode
```

| Service                                               | What it unlocks                              | How to get the key                                                                                                  | Where it goes                                        |
| ----------------------------------------------------- | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| **Anthropic (Claude)**                                | Subscription tier detection, plan estimation | OAuth via Claude Code CLI (default), or set `ANTHROPIC_API_KEY` for raw API mode                                    | Auto if OAuth; else `.env.local`                     |
| **OpenAI Codex / ChatGPT**                            | ChatGPT panel + Codex tab                    | Sign in via the Codex CLI â€” writes `~/.codex/auth.json`                                                             | Auto                                                 |
| **OpenRouter**                                        | Live PAYG balance + burn rate                | https://openrouter.ai/keys                                                                                          | `OPENROUTER_API_KEY` in `.env.local`                 |
| **Pinecone**                                          | Vector indexes in the memory graph           | https://app.pinecone.io                                                                                             | `PINECONE_API_KEY` in `.env.local`                   |
| **Obsidian**                                          | Local notes in the memory graph              | Nothing â€” auto-detected from common vault locations, one-level desktop/document scans, or `CLAUDE_OS_OBSIDIAN_PATH` | n/a                                                  |
| **OpenClaw**                                          | Sidebar agent tab                            | Install OpenClaw locally                                                                                            | Auto via `~/.openclaw/openclaw.json`                 |
| **AntiGravity / Claude.app / Cursor / Terminal apps** | Setup-wizard tool detection                  | Install the apps                                                                                                    | Auto via `/Applications/<App>.app`                   |
| **KIE.ai**                                            | (Developer scripts only â€” image generation)  | https://kie.ai                                                                                                      | `KIE_API_KEY` in `.env.local`; not needed at runtime |

The setup wizard surfaces missing keys and helps you generate the `.env.local` file at the end. Keys are only ever read locally â€” the aggregator never relays them anywhere except the API the key belongs to.

---

## Permissions (macOS Full Disk Access)

The first time the aggregator reads `~/.claude/projects/`, macOS may deny access. If `bun run scripts/aggregate.ts` reports `permission denied`, grant **Full Disk Access**:

1. Open **System Settings â†’ Privacy & Security â†’ Full Disk Access**.
2. Toggle **on** for the host you run commands from â€” Terminal, iTerm2, Ghostty, or your editor's integrated shell.
3. Re-run the aggregator. It logs project counts and memory file counts as it walks each directory.

`security dump-keychain` (used to estimate Claude plan tier) will additionally prompt for your **login password** the first time it runs. That's a macOS prompt â€” Claude OS never sees the password.

---

## Linux / Windows

> **Not the supported platform.** Claude OS is built for macOS. The sections below describe best-effort, degraded behavior on Linux/Windows â€” not a promise that the dashboard will be coherent.

**Works:**

- JSONL parsing (`~/.claude/projects/`)
- OpenRouter + Pinecone polling
- Obsidian vault detection (only paths that exist outside macOS conventions; iCloud Obsidian is unreachable)
- Codex auth detection (if you have `~/.codex/auth.json`)

**Doesn't work / silently degraded:**

- macOS Keychain credential count â†’ returns 0 (shows up in the UI as if you had no creds)
- `dscl`-based auto-redaction of your full name â†’ **anonymization is weaker** (only the username and `CLAUDE_OS_REDACT_NAMES` env entries are scrubbed)
- Plan-tier estimation â†’ falls back to "unknown"
- Dream cron â†’ no `launchd`; the installer prints a `crontab` snippet you have to add manually
- `.app` discovery in `/Applications` â†’ not attempted
- macOS Full Disk Access hints are still printed (cosmetic noise)

The aggregator prints a platform warning at startup, but the **UI does not currently show a "degraded mode" banner** â€” be aware that some tiles will show zeros that look like real values. See [`REVIEW.md`](./REVIEW.md) Â§2 for the full critique. Open an issue if you want a target supported properly.

---

## Privacy + anonymization

- **Source data stays read-only.** Claude OS never writes to source tool data directories like `~/.claude/`, `~/.codex/`, `~/.openclaw/`, or your Obsidian vaults. The sidecar writes Claude OS config to `~/.claude-os/config.json`, writes repo-local `.env.local`, and the aggregator writes `src/data/live-data.json`.
- **Local processing.** No telemetry. No phone-home. No analytics. The only outbound calls are the APIs whose keys you explicitly provide (OpenRouter, Pinecone) and Dream runs you invoke via `claude -p`, which make Anthropic API calls through Claude Code.
- **`live-data.json` is gitignored.** Only the sanitized `live-data.example.json` is committed.
- **Anonymization is on by default.** `CLAUDE_OS_ANONYMIZE=1` rewrites your real macOS username to `operator`, redacts emails to `<email-redacted>`, and accepts an extra `CLAUDE_OS_REDACT_NAMES="First Last,handle"` env var to scrub additional terms before screen-sharing or recording.
- **Disable for fully local use:** `CLAUDE_OS_ANONYMIZE=0 bun run scripts/aggregate.ts`.

---

## The Dream feature

Dream is what makes Claude OS more than a metrics dashboard.

A daily 7am cron runs `claude -p "/dream"`, which audits your last 24 hours of activity across **eight orthogonal signal buckets**:

1. **Conversation mining** â€” recurring topics not yet skilled
2. **Cost intelligence** â€” model misuse, low cache hit rate, token waste
3. **Skill performance** â€” dead, dormant, or compose-worthy skills
4. **Memory health** â€” stale, conflicting, missing, drifted
5. **Session hygiene** â€” context rot, edit-without-read, length issues
6. **Workflow patterns** â€” manual sequences ripe for automation
7. **External opportunity** â€” new tools, skills, models worth adopting (only outbound network bucket; opt-in)
8. **Business outcomes** â€” actual ROI per skill / workspace / day

It then ranks every candidate by `severity Ã— dollar_impact` and writes the **Top 4** to `~/.claude-os/dreams/dream-{date}.json`. The dashboard reads that JSON and renders four cards. Each card has a `[Run this fix â†’]` button that POSTs the prescription ID to the local sidecar, which resolves the stored command from the latest Dream JSON before running it.

**Cost:** ~$1.50â€“$3.00 per run, amortized into your existing Claude subscription via OAuth. No new keys.

**Setting it up (two commands):**

```bash
# 1. Install the /dream skill into your Claude Code skills directory
mkdir -p ~/.claude/skills && cp -r skills/dream ~/.claude/skills/dream

# 2. Install the launchd cron so /dream runs daily at 7am (macOS)
bun run install-dream         # --time HH:MM to change, --uninstall to remove
```

The cron installer writes `~/Library/LaunchAgents/com.claude-os.dream.plist`, loads it via `launchctl`, and pipes output to `~/.claude-os/dream-cron.log`. The skill itself lives at `skills/dream/SKILL.md` in this repo and is what `claude -p "/dream"` runs. On Linux/Windows the installer prints a `crontab` snippet you can install by hand.

The dashboard's `[Run again â†»]` button hits `POST /api/dream-now` on the local sidecar (auto-started by `bun run dev`) to trigger a run on demand without waiting for 7am.

In short: Dream walks 8 signal buckets (CONVERSATION, COST, SKILLS, MEMORY, SESSION, WORKFLOW, EXTERNAL, BUSINESS), ranks every candidate by `severity Ã— dollar_impact`, and emits the top 4 highest-impact prescriptions as JSON to `~/.claude-os/dreams/dream-{date}.json`. Each prescription comes with a `claude -p "<command>"` that the dashboard can run via its prescription ID or that you can run manually in your terminal.

---

## Repo layout

```
claude-os/
â”œâ”€â”€ scripts/
â”‚   â””â”€â”€ aggregate.ts                # Bun script â€” reads ~/.claude, Pinecone, OpenRouter
â”œâ”€â”€ src/
â”‚   â”œâ”€â”€ data/
â”‚   â”‚   â”œâ”€â”€ live-data.json          # gitignored â€” generated from your machine
â”‚   â”‚   â””â”€â”€ live-data.example.json  # sanitized template (committed, demo mode)
â”‚   â”œâ”€â”€ routes/
â”‚   â”‚   â”œâ”€â”€ index.tsx               # Home dashboard
â”‚   â”‚   â”œâ”€â”€ setup.tsx               # Setup wizard (Welcome pre-roll + 7 numbered steps)
â”‚   â”‚   â”œâ”€â”€ memory.tsx              # 3D memory graph
â”‚   â”‚   â”œâ”€â”€ share.tsx               # 1200Ã—630 OG card
â”‚   â”‚   â”œâ”€â”€ skills.tsx              # Skills fleet detail
â”‚   â”‚   â”œâ”€â”€ activity.tsx            # Recent project activity
â”‚   â”‚   â”œâ”€â”€ settings.tsx            # Local config viewer
â”‚   â”‚   â”œâ”€â”€ workspaces.*.tsx        # Per-workspace drilldowns
â”‚   â”‚   â””â”€â”€ agents.*.tsx            # Hermes / OpenClaw sidebar tabs
â”‚   â”œâ”€â”€ components/                 # KPI tiles, usage panel, dream cards, sidebar
â”‚   â””â”€â”€ assets/                     # Logos + Ghibli skill banners
â””â”€â”€ package.json
```

The aggregator is the one and only integration point with disk. The frontend never reads `~/.claude/` directly.

---

## Roadmap

Clearly labeled aspirations. All contributions welcome.

- â³ **`bun create claude-os`** â€” distribution package on npm so you can `bun create claude-os my-os`
- âœ… **Backend sidecar** â€” auto-started alongside Vite by `bun run dev`. Writes `~/.claude-os/config.json` from the wizard, runs the aggregator on demand, and powers Dream's one-click prescriptions. See [Backend sidecar](#backend-sidecar-auto-started-by-bun-run-dev) below
- âœ… **Dream cron auto-install** â€” `bun run install-dream` writes the launchd plist and loads it; `skills/dream/SKILL.md` is the bundled skill
- â³ **File-watcher** â€” live memory graph updates when you edit a note in Obsidian
- â³ **Pinecone search bar** â€” query your vector indexes from inside the memory graph
- â³ **Heartbeat + sparklines + ambient sound polish** â€” small details that make the dashboard feel alive

---

## Contributing

Issues and pull requests are welcome. The codebase is small enough to read in a sitting:

- Aggregator changes â†’ `scripts/aggregate.ts`
- New panels â†’ `src/components/` + wire into `src/routes/index.tsx`
- New routes â†’ `src/routes/`
- Data contract changes â†’ update `src/data/live-data.example.json` so the demo mode still boots

Don't push secrets. Don't commit `live-data.json`. The aggregator's anonymization layer is on by default for a reason.

---

## Backend sidecar (auto-started by `bun run dev`)

A small Bun sidecar that turns the dashboard from copy-paste theater into a real product. It powers the **"Activate now"** button at the end of the setup wizard, the **"Scanning your machineâ€¦"** step inside the wizard, and the **"Run this fix"** button on each Dream prescription.

`bun run dev` starts it for you â€” Vite and the sidecar run in the same terminal under one process supervisor. If you want to run it standalone (for debugging, or to keep Vite in another window), `bun run server` still works.

It binds to `127.0.0.1:17873` (localhost only â€” never `0.0.0.0`) and exposes:

- `POST /api/install` â€” writes `~/.claude-os/config.json` and `.env.local` from the wizard's collected state.
- `POST /api/aggregate` â€” runs `bun run scripts/aggregate.ts` and streams stdout back as Server-Sent Events. On exit, emits `event: meta` with `{ wrote: "src/data/live-data.json" }` then `event: done` with the exit code.
- `POST /api/run-fix` â€” accepts `{ "prescriptionId": "..." }`, resolves it from the latest `~/.claude-os/dreams/dream-{date}.json`, then streams the stored `claude -p` command back as Server-Sent Events.
- `POST /api/dream-now` â€” same, but runs the `/dream` skill (optionally `--quick`).
- `GET /api/health` â€” `{ ok: true, version: "1" }`.

If the sidecar isn't reachable, the wizard surfaces a clear "restart `bun run dev`" hint and the Dream cards fall back to copy-paste flows so the UI keeps working.

---

## License

MIT. See [`LICENSE`](LICENSE).

---

Projeto adaptado da comunidade INEMA TDS - Marca Arckhe