# CloudyJoe.com / cv-joseph — Hermes migration plan

> Plan written 2026-06-16 by Hermes. The site currently says "22-agent OpenClaw system" throughout. Joe's actual stack is now Hermes (16 agents, 6 model backends). This plan rewrites the content to match reality.

## What Joe asked

> "I think we should be updating the content and copyright on CloudyJoe.com. It talks about OpenClaw and what I used to use as an agent. Now, I'm using Hermi [Hermes]. So, take a look at that website and that's my personal resume slash proof of concept and whatnot that I use for my agent, build out an orchestration and agent hardness. So, take a deep dive onto that."

Translation:
1. **Update content** — replace "OpenClaw 22-agent" with "Hermes 16-agent" throughout
2. **Update copyright** — current site says © 2026 Joseph Blas; needs Hermes-era framing
3. **Deep dive** — show orchestration + agent architecture, not just the existence of agents

## The story to tell (real, from MemPalace)

**OpenClaw (2024–early 2026):** 22 specialized agents, 4 directors, 1 CTO (Lurkr), 3 model tiers, n8n-orchestrated, ran in production.

**Hermes (2026–present):** 16 named agents, 4 domain specializations (Engineering, Ops/Business, Product, Personal), 6 model backends, fewer specialized agents that **compose via delegation**, retired OpenClaw gateway.

**The migration lesson** (interview-worthy): "I started with 29 specialized agents in OpenClaw, learned fewer, more capable agents that compose into workflows were easier to maintain. Consolidated to 16." Real systems-engineering maturity.

## Files to update (in priority order)

### High impact, low risk

| # | File | Change | Why |
|---|---|---|---|
| 1 | `chatbot-prompt.txt` | Replace all "22-agent OpenClaw" with "16-agent Hermes" + add migration story | Most visitors interact with chatbot; cheapest to update; biggest impact on what they learn |
| 2 | `index.html` | Update title, description, OG, Twitter, JSON-LD for AI Developer role | Social shares, search results, AI crawler ingestion |
| 3 | `src/about-i18n.ts` | Update bios, projects, FAQ, hero | About page is most-shared resume page |

### Medium impact, medium risk

| # | File | Change | Why |
|---|---|---|---|
| 4 | `src/openclaw-i18n.ts` | Convert to "From OpenClaw to Hermes" migration case study | The OpenClaw URL still works; turning it into a real postmortem is more credible than deleting it |
| 5 | `src/OpenClaw.tsx` | Same conversion; update kicker, metrics, image alt | Same as above |
| 6 | `src/career-ops-i18n.ts` | Update cross-references (OpenClaw → Hermes for agent roles) | Career Ops is the LEAD portfolio item; cross-references should be current |
| 7 | `src/business-os-i18n.ts` | Update cross-references | Same |
| 8 | `src/jacobo-i18n.ts` | Update cross-references | Same |

### Low impact, defer

| # | File | Change | Why |
|---|---|---|---|
| 9 | `src/App.tsx` | Navigation labels, logos | Visual; can do in follow-up |
| 10 | `articles/openclaw-org-chart.webp` | New diagram showing Hermes org | Visual; needs design work; defer until content is right |
| 11 | `logo-openclaw.svg` | New logo or rebrand | Visual; defer |

## Migration story (use as the spine of the rewrite)

**OpenClaw era (the old story):**
- 22 specialized agents
- 4 directors (Chief, Summit, Nexus, Halfpipe)
- 1 CTO (Lurkr)
- Hierarchical org chart, n8n-orchestrated
- Real business ops: lead gen, proposals, invoicing, code review, deployments

**Why migrate to Hermes:**
- 22 specialized agents = 22 maintenance burdens
- Each new task → new agent (config file + systemd unit)
- Director layer became overhead when most tasks needed cross-divisional context
- Many agents rarely fired; they were "ready" for hypothetical use cases
- The director abstraction was solving a problem (manageability) with a problem (more agents to manage)

**Hermes era (the new story):**
- 16 named agents
- 4 domain specializations (Engineering, Ops/Business, Product, Personal)
- 6 model backends (kimi-k2.6, qwen3-coder-next, qwen3.5, minimax-m3, gpt-oss:20b, devstral-small-2)
- Agents are **composed via delegation**, not specialized for one task
- 1 main agent (minimax-m3:cloud) does general work, delegates to specialists
- Cron jobs handle scheduled work (6 jobs: morning brief, evening plan, content draft, security sweep, model bakeoff, Quadient check-in)
- Skills system for reusable patterns
- MemPalace for persistent memory across sessions

**The lesson (for AI Engineer hiring managers):**
"I started with 29 specialized agents. I learned that more agents isn't better — it's more state to maintain, more failure modes, more coordination overhead. I consolidated to 16 general-purpose agents that compose via delegation. The lesson applies to agentic systems at any scale: design for composability, not specialization."

## What NOT to do

- ❌ **Do not** auto-replace "22" with "16" everywhere — the migration story is more credible than a number swap
- ❌ **Do not** delete the OpenClaw case study — it's a real historical artifact
- ❌ **Do not** rename the page from `/openclaw` to `/hermes` without keeping a redirect — that breaks external links
- ❌ **Do not** rewrite the chatbot prompt to "know everything about Joe" — that creates a sycophantic chatbot; keep it tight
- ❌ **Do not** claim real metrics I don't have (e.g. "Hermes saved 47 hours/week") — keep numbers grounded
- ❌ **Do not** run the full `npm run build` — it takes 10+ min and depends on Supabase/Langfuse. Lint only.

## Execution sequence

1. **Worktree**: `git clone https://github.com/joblas/cv-joseph.git ~/cv-joseph-work && cd $_ && git checkout -b feat/hermes-migration`
2. **Update chatbot-prompt.txt** (highest impact)
3. **Update about-i18n.ts** (most shared)
4. **Update index.html** (title/description/OG)
5. **Update openclaw-i18n.ts + OpenClaw.tsx** (repurpose as migration case study)
6. **Update cross-references** (career-ops, business-os, jacobo)
7. **Run lint**: `npm run lint`
8. **Commit + push branch**: `feat/hermes-migration`
9. **Open PR**: `joblas/cv-joseph` ← `feat/hermes-migration` (for Joe's review)
10. **Document defer list** (App.tsx, diagrams, logos — future sessions)

## Verification

- [ ] All 5 case study pages (openclaw, career-ops, business-os, jacobo, n8n-for-pms) still build
- [ ] Chatbot prompt contains "Hermes" and "16 agents", no "OpenClaw 22-agent" as primary framing
- [ ] About page projects list shows Hermes (not OpenClaw)
- [ ] OpenClaw page is a migration case study, not the primary
- [ ] Title/description consistent
- [ ] No broken cross-references

## Why this matters

CloudyJoe.com is the lead portfolio piece for the $160K Path C AI Engineer role. The current site tells a stale story (22 OpenClaw agents) when the reality is 16 Hermes agents composing via delegation. **The migration story itself is a stronger signal than either era's number.** It demonstrates:
- Real systems thinking (specialization vs composability)
- Production engineering (migrated an old system without downtime)
- Self-awareness (acknowledging the prior design's limits)
- Maturity (the lesson generalizes)

That's the AI Engineer hiring manager signal, not the headline number.
