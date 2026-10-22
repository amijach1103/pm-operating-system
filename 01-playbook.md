# Claude Code as a PM operating system: playbook and lessons learned

**Work sample, Agueda Schwartz.** A playbook I put together on March 31, 2026, for colleagues at the lab who wanted to use Claude Code to run a project, not just to write code. At the time, three scoped instances were running alongside the main session; as the project pivoted, some later went idle or were retired. Names, internal project names and workspace details are removed.

---

## What this is

A playbook for using Claude Code as the operating system for project management: status tracking, stakeholder communication, sprint planning, decisions, and coordinating parallel tracks.

On our discovery project, Claude Code was the single interface for the PM work. I ran it with fractional support and no direct reports. Claude Code handled the information architecture, the daily rhythm, stakeholder briefs, meeting prep, and coordination between scoped instances, while I made the decisions, ran the meetings, and owned the relationships.

It covers:
1. The PM orchestrator: what the main session is and isn't
2. Scoped instances: how to define and launch them
3. The `_status/` system: the single source of truth
4. The instruction hierarchy: how `CLAUDE.md` files cascade
5. Document ownership: the single-writer rule
6. Handoffs: how instances coordinate without talking to each other
7. Integrations: Slack, Asana, Granola, Google Drive
8. Skills: automating recurring PM rituals
9. An advisory council: red-teaming your own blind spots
10. Decision discipline: pre-committed criteria and hard deadlines
11. Lessons learned

---

## Part 1: The PM orchestrator

### The core idea

Claude Code runs from the project root. When you open a terminal in the project and launch `claude`, it loads your `CLAUDE.md` and its `@imports`, so it has the project's context in seconds. No re-explaining.

The PM session is the **control plane**. It doesn't do the execution work (ad data, landing page edits, cohort session prep). It coordinates the instances that do.

```
YOU (the PM)
 │
 │  Decisions, relationships, meetings, judgment calls
 │
 ▼
PM SESSION (Claude Code at the project root)
 │
 │  Coordination, synthesis, stakeholder comms, status tracking
 │
 ├──→ Market validation instance (scoped session)
 ├──→ Engineering instance (scoped session)
 └──→ Cohort operations instance (scoped session)
```

### What the PM session owns
- **`_status/` files:** the single source of truth. The PM writes; everyone else reads.
- **Coordination between tracks:** instances don't talk to each other. The PM routes handoffs.
- **Stakeholder communication:** leadership briefs, Asana updates, daily check-ins.
- **Sprint planning and decisions:** go, pivot or kill; sprint goals; capacity.
- **The advisory council:** structured red-teaming before major decisions.
- **The decision log:** an append-only record of what was decided and why.

### What it delegates

| Task | Delegated to |
|------|-------------|
| Ad performance data and analysis | Market validation instance |
| Landing page copy changes | Market validation instance |
| Outreach and interviews with strangers | Market validation instance |
| Engineering scaffolding and infrastructure | Engineering instance |
| Cohort session prep, participant messages | Cohort instance |
| The cohort's Google Drive folders | Cohort instance |

### The daily rhythm
1. **Morning:** read every instance's `status.md`, build the consolidated view, draft the daily check-in.
2. **During the day:** route tasks, answer the instances' questions, do PM work (briefs, Asana, synthesis).
3. **End of day:** `/session-end` updates `_status/current.md` and the relevant status files.

You never start a session cold. The `_status/` files load automatically, you read the instance status files for two minutes, and you know where everything stands.

---

## Part 2: Scoped instances

### Why scope them

Without scoping, Claude Code tries to do everything in one session. That works for small projects. Once you have parallel workstreams (paid ads running while a cohort operates while code is being scaffolded), one session can't hold it all. You lose context, the session drifts, and you spend more time re-orienting than working.

A scoped instance gives each workstream its own Claude Code session, with its own `CLAUDE.md`, its own status file, and explicit limits on what it can do.

### How to define one

Each lives in `agents/[name]/` with three files:

```
agents/market-validation/
  CLAUDE.md           ← identity, scope, rules, domain knowledge
  status.md           ← current state, updated every session
  references/
    INDEX.md          ← curated sources it trusts
```

Its `CLAUDE.md` has these sections, and every one matters:
1. **Role and scope:** one sentence each. What is it, and where's the boundary?
2. **Reports to:** always the PM session.
3. **What it owns:** an explicit list. "Paid advertising" means platform management, copy, budget tracking and optimization, not "marketing".
4. **Files it reads but doesn't write:** PM-owned documents it needs for context.
5. **Files it writes:** its workspace, with no overlap with other instances.
6. **Decision authority:** what's autonomous and what needs sign-off. The most important section. Get it wrong and it either asks permission for everything (slow) or makes expensive decisions without you (dangerous).
7. **Knowledge trust rules:** reference library first, search before you claim, cite your sources. Prevents invented benchmarks.
8. **Domain knowledge:** benchmarks, personas and decision criteria for its work.
9. **Status reporting:** a format template that keeps it reporting only on its own domain.

### Launching one

```bash
cd your-project/agents/market-validation
claude
```

Its `CLAUDE.md` loads automatically, and its imports bring in its status and reference index. It works in its lane from the first message.

### How many you need

Start with one. Add another when a single session can't hold two parallel workstreams. We started with none (the PM session did everything) and added three when paid ads, a code scaffold and a cohort were all running at once.

The overhead is low: a `CLAUDE.md`, a status file, a reference index. The cost of not having one is context bleed: the session that's supposed to pull ad data starts updating your Asana board.

---

## Part 3: The `_status/` system

### The problem it solves

Before `_status/`, every session started with "let me catch you up". Meeting notes lived in Granola. Decisions lived in session conversations, which get compressed and lost. Sprint status lived in my head. There was information everywhere and a source of truth nowhere.

### The design

Six markdown files. That's the whole system.

```
_status/
  current.md       ← snapshot: what's happening now, risks, wins, instance summary
  sprint.md        ← current sprint: goals, tasks, tracks, capacity, criteria
  dates.md         ← key dates and milestones, upcoming and completed
  decisions.md     ← append-only decision log
  workstreams.md   ← status per workstream
  people.md        ← contacts, roles, who's doing what
```

### How it loads

In the project's `CLAUDE.md`:

```markdown
## Auto-loaded context

@_status/current.md
@_status/sprint.md
@_status/dates.md
```

Every launch loads these three, so Claude knows where the project stands without a word from you. The others load on demand, because they change less often.

### Update discipline
- **The PM writes `_status/` files.** Instances read them. Non-negotiable.
- **`/session-end` is how they update.** At the end of every session it updates `current.md`, checks off tasks in `sprint.md`, and appends decisions to `decisions.md`.
- **`current.md` changes most.** Keep it to one screen plus a session log. Past two pages, archive older sessions.
- **`decisions.md` is append-only.** Never edit past entries. Each has the date, the decision, the rationale, who decided, and what it replaces.
- **`dates.md` has two sections, upcoming and completed.** Move dates across as they pass, and you have a project timeline for free.

### Why markdown, not a tool

We tried Asana as the source of truth. It's good for team visibility and poor for Claude Code: the integration is slow, authentication breaks, and tasks and custom fields don't map to "here's the full project state". Markdown files load instantly, are version-controlled, and can be structured however you need.

Asana, Slack and Google Drive are **output channels**, places you push formatted status to. `_status/` is where the truth lives.

---

## Part 4: The instruction hierarchy

### How instructions cascade

```
~/Projects/CLAUDE.md              ← portfolio level: rules across projects
  └── your-project/CLAUDE.md      ← project level: identity, rules, routing, @imports
       ├── .claude/rules/*.md      ← shared rules: orchestrator scope, ownership, council, routing
       └── agents/*/CLAUDE.md      ← instance level: scope, domain knowledge, decision authority
```

| Level | What belongs here |
|-------|------------------|
| **Portfolio `CLAUDE.md`** | Rules across projects, the integration table, a directory index |
| **Project `CLAUDE.md`** | Identity, key rules (no automated deletes; read freely, write with review), `@imports`, the skill table, a routing summary |
| **`.claude/rules/`** | Shared rules: orchestrator scope, document ownership, the council process, routing details |
| **Instance `CLAUDE.md`** | Identity, scope, what it owns, read and write limits, decision authority, trust rules, domain knowledge, status format |

### Key choices
- **Import only what changes often and is needed every session.** Everything else loads on demand.
- **Rules go in `.claude/rules/`, not `CLAUDE.md`.** The main file stays about identity and structure; rules can be long without bloating what loads every time.
- **An instance's `CLAUDE.md` is self-contained.** It can work with its own `CLAUDE.md`, status file and reference library, reading `_status/` for context, without depending on the PM's instructions.

---

## Part 5: Document ownership

### The single-writer rule

Every file has exactly one writer. Others can read; only the owner writes.

It sounds obvious until your market validation instance overwrites your evidence package draft, or your cohort instance updates `_status/current.md` with cohort-only context and wipes the full project view.

| Directory or file | Owner | Others |
|---|---|---|
| `_status/*` | PM | All instances read |
| Evidence package | PM, assembled from instance outputs | Market validation supplies data sections |
| Interview notes | PM, and market validation for its own interviews | Cross-read |
| Landing page | Market validation (copy) and engineering (structure) | |
| Pilot folder | Cohort | PM reads observations |
| `agents/[name]/status.md` | That instance | PM reads |

### Rules
1. **No file has two writers.** If ownership is unclear, the PM decides and updates the map.
2. **Instances update their own `status.md`.** The PM reads all of them.
3. **Shared directories are read access.** No duplication.
4. **The PM creates shared artifacts.** If two instances need the same data in different formats, the PM makes one shared artifact.

The map lives in `.claude/rules/document-ownership.md`, so every session knows the rules.

---

## Part 6: Handoffs

### How instances coordinate

They don't talk to each other. No shared channel, no instance-to-instance messages. All coordination goes through the PM, via status files.

```
Market validation                 PM session                         Cohort
─────────────────                 ──────────                         ──────
Updates status.md:
"Handoff: day 7 data              Reads its status.md ──→
 ready for evidence               "Day 7 data is in.
 package assembly"                  Time to update the
                                    evidence package."

                                   Reads cohort status.md ──→         Updates status.md:
                                   "Session 2 notes are in."          "Handoff: session 2
                                                                       observations ready
                                                                       for PM synthesis"
```

### The handoff format

Each `status.md` ends with a **Handoffs** section that says what's ready, who needs it, and what to do with it:

```markdown
## Handoffs

- **Final campaign data → PM**: full dataset ready, metrics updated for Tuesday's presentation.
- **Campaign critique → PM**: `campaign-setup-critique.md`. Key argument: the test was underpowered, not failed.
```

Handoffs happen after every data pull, after a deliverable, when an instance is blocked on a PM decision, and at session end. The PM reads them every morning. It takes five minutes and replaces a standup.

---

## Part 7: Integrations

### What we connected

| Service | What it does | Why it matters |
|---------|-------------|----------------|
| **Slack** | Read channel history, post messages | Daily check-ins and status updates without leaving Claude Code |
| **Asana** | Read, create and update tasks and status | Sprint tracking, stakeholder-facing updates |
| **Granola** | Search meetings, get transcripts | Meeting context for check-ins, decision history, meeting prep |
| **Google Drive** | Read and write Docs, Sheets and Drive files | Leadership briefs, cohort templates, signup data |

All of them are configured in `~/.claude.json` under `mcpServers`, not in `.claude/mcp_config.json`, which Claude Code ignores. We learned that the hard way.

### Routing rules

If you have more than one workspace for the same service, write down which connection maps to which workspace. We had three Slack connections:

| Connection | Workspace | Use for |
|-----------|-----------|---------|
| Slack, organization | The lab's workspace | Daily check-ins, project updates |
| Slack, cohort (participant) | The cohort's workspace, as a participant | Peer reviews, engagement |
| Slack, cohort (admin) | The cohort's workspace, as admin | Reminders, session summaries |

The rules live in `.claude/rules/mcp-routing.md`, with channel IDs, permission notes, and which instances can use which connections.

### What we learned
1. **Authentication breaks.** Tokens expire, network security tools interfere, servers need restarts. Budget time for maintenance. A personal access token was more reliable than OAuth for Asana.
2. **Read is free; write needs review.** Auto-allow reads and prompt for writes. You don't want a Slack message posted without seeing it first.
3. **Not every instance needs every connection.** Market validation got Google Drive and web search, not Asana or Granola. Scope integration access the way you scope file access.
4. **The Google Drive integration isn't Google Drive for desktop.** Desktop syncs files locally for reading; the integration reads and writes Drive content programmatically. Both are useful.

---

## Part 8: Skills

Skills are reusable prompts in `.claude/skills/` that automate recurring rituals. You call them with a slash command, and they read from `_status/`, Granola and the conversation to produce formatted output.

| Skill | What it does | When |
|-------|-------------|------|
| `/session-end` | Updates the `_status/` files with what happened | End of every session |
| `/daily-checkin` | Drafts the daily standup for Slack and Teams | Morning |
| `/status-update [audience]` | Formats status for Slack, leadership, Asana or the team | Before stakeholder touchpoints |
| `/sprint-closeout` | Summarizes the sprint from sessions, decisions and meetings | Sprint boundary |
| `/meeting-prep [topic]` | Assembles a context brief for a meeting | Before any meeting |

### Principles
1. **Skills read from `_status/`; they don't store state.** Their output comes from the source of truth, so it's always current.
2. **Skills draft; humans approve.** Every skill shows a draft before posting or writing. You edit, then confirm.
3. **Skills force status hygiene.** If `_status/` is stale, `/daily-checkin` produces a stale check-in, which is pressure to keep it current.
4. **Don't over-build.** Five is enough. If you'd use it once a month, do it by hand.

### Build `/session-end` first

Without it, the status files drift. With it, every session ends with an update that keeps the source of truth current: it reads the conversation, checks Granola for meetings, and updates `current.md`, `sprint.md` and `decisions.md`. The next session loads them and knows where you left off.

---

## Part 9: Advisory council

### The problem

Working as the only PM means your blind spots go unchallenged between reviews. Nobody asks "are you sure?" before you present to leadership.

### Four lenses

A structured red-team step in Claude Code. Four simulated perspectives challenge your reasoning before big decisions:

| Lens | Core question | Pushes on |
|------|--------------|-----------|
| **User researcher** | "What's your evidence for that, and who told you?" | Assumed needs, untested value propositions, proxy data treated as direct evidence |
| **Product strategist** | "If this works, what happens next? If it fails, will you actually kill it?" | Scope creep, unclear criteria, deferred decisions |
| **Critical friend** | "What are you avoiding, and why?" | Comfort work disguised as progress, optimistic framing |
| **Stakeholder simulator** | "When do we have something we can sell?" | Research timelines, academic framing, small-scale thinking |

Calibrate the last one to the stakeholder who pushes you hardest on speed and commercial viability. Make it uncomfortable.

| Trigger | Format | Time |
|---------|--------|------|
| Before major decisions | Full red team: present the artifact, attack from every lens | 30 min |
| Before stakeholder artifacts | Targeted review with the lenses that matter | 15 min |
| Weekly | Quick check: what changed our beliefs this week? | 15 min |
| Sprint close | Retro: did we learn, or just do? | 20 min |

### How to run it

Ask Claude Code to run an advisory council red team on an artifact or decision. It runs each lens separately, collects the critiques, and flags convergent concerns (raised by two or more lenses). You respond to each: accept, reject with reasoning, or flag for follow-up. Logs go in a dated file.

### Principles
1. **The council has no authority.** You decide. The value is hearing the challenge.
2. **Convergent concerns are the strongest signal.**
3. **Write down what changed.** If the critiques don't change anything, the council is theater.
4. **Don't overuse it.** It's for decisions and stakeholder artifacts, not Asana cleanup.

### What it caught
- Six concerns in our outcomes framework, before we committed to thresholds.
- That reframing the product as a system of layers, not just AI, was a positioning decision, not messaging, which became a standing project principle.
- That we were about to present evidence sources that didn't all hold up. We cut to the honest ones.
- That our target population was undefined: we were testing adjacent populations and calling it validation.

---

## Part 10: Decision discipline

### The problem

Discovery projects die of indecision. There's always one more question, committing to criteria means you might fail, and "we need more data" extends every sprint.

### Three mechanisms

**1. Criteria committed before data exists.** Before a test runs, write down what go, pivot and kill look like, with measurable thresholds. Have someone red-team them, then commit. We wrote ours before the ads launched:
- **Go:** more than 100 signups, more than 3% conversion, and at least two interviews with strangers showing genuine intent
- **Pivot:** interest but the wrong format or audience, or under 10% of signups agreeing to be contacted
- **Kill:** fewer than 30 signups after two or more weeks across several channels

When the data came in, we judged it against criteria we'd already committed to, not criteria invented to fit the results.

**2. A hard decision date.** No extensions, no "one more sprint". The deadline compresses everyone's thinking. We hit ours. The volume came in well under the go thresholds, but from a single channel, so the kill condition (several channels) wasn't met. Against the criteria we'd committed to, the call was pivot.

**3. Three outcomes, no fourth.** Go, pivot or kill. No "let's keep exploring". Pivot is broad enough to mean "reshape and try again". Without that, the quality signals could easily have justified another sprint of research. The framework said: make the call now with what you have.

### It works at every scale

- **Project:** a decision sprint with committed criteria, a hard deadline and a go, pivot or kill call.
- **Sprint:** a weekly observe, learn, build, test cycle, with each week ending in keep, revise or drop for what shipped.
- **Week:** Wednesday observe, Thursday learn, Friday to Tuesday build and ship, Wednesday test.

---

## Part 11: Lessons learned

### What worked

1. **`_status/` as the source of truth is the foundation.** Everything else builds on it. Build the `/session-end` habit early.
2. **Scoped instances cut context bleed.** Each session stays in its lane, and the PM session only synthesizes.
3. **The hierarchy loads context progressively.** An instance doesn't need to know the Asana workflow or the council process; it needs its scope, its sources and its decision authority.
4. **Document ownership prevents collisions.** Lose 30 minutes of work to a write collision once and you never skip the ownership map again.
5. **Skills turn rituals into one command.** `/daily-checkin` takes 90 seconds instead of 15 minutes; `/session-end` two minutes instead of updating three files by hand.
6. **The council catches what you won't.** Left to my own judgment, I would have presented all the evidence sources because together they made a stronger case. The council forced honesty.
7. **Integrations cut the tool-switching tax.** The saving isn't just the task; it's not opening five tabs and losing your place.

### What didn't work, and what we'd change

1. **Set up scoped instances from the start, not mid-sprint.** Setup takes 30 minutes; weeks of context bleed cost more.
2. **The architecture document went stale and stayed stale.** The `_status/` files stayed current because `/session-end` updates them. A document with no skill or habit behind it goes stale. Accept it and point people to `_status/`.
3. **Integrations break at the worst times.** Slack went down the week we launched paid ads; Granola's authorization expired during a sprint review; Asana's OAuth never recovered and we switched to a token. Budget for maintenance and keep a fallback. Copy and paste always works.
4. **The council needs to run more often.** We used it for big decisions and skipped the weekly checks, which is where it catches small assumptions piling up.
5. **Google Drive and local files are two sources of half-truth.** Leadership reads Google Docs; Claude Code reads local markdown; they drift. Be deliberate about which is canonical for which audience.
6. **Status files grow too long.** One hit 70 lines of metric tables. A status file should answer five questions: what are you working on, what did you finish, what's next, what's blocked, what's ready to hand off. Everything else goes in separate files.
7. **Don't skip the decision criteria.** Committing to criteria before testing was the most important structural decision of the project. Without it, we'd have rationalized every result into "we need more data".

### The meta-lesson

Claude Code as a PM works because it **separates information management from judgment**. Claude Code manages the files, the status, the meeting context, the formatted outputs and the coordination. The PM manages the relationships, the decisions, the politics and the calls.

It fails when either side does the other's job: when the PM tracks status by hand across five tools instead of using `_status/`, or when Claude Code makes the stakeholder communication decision instead of drafting options.

`_status/` is the information layer, skills the automation layer, scoped instances the execution layer, and the PM the decision layer. Keep them clean and the system runs.

---

## Quick start

**Day 1**
- [ ] Create `_status/` with `current.md`, `sprint.md`, `dates.md` and `decisions.md`
- [ ] Write the project `CLAUDE.md` with `@imports` for the three main status files
- [ ] Build `/session-end` so the status files stay current from day one
- [ ] Write the first sprint's decision criteria

**When you have parallel workstreams**
- [ ] Create `agents/[name]/` with `CLAUDE.md`, `status.md` and `references/INDEX.md`
- [ ] Write the ownership map in `.claude/rules/document-ownership.md`
- [ ] Write the orchestrator scope in `.claude/rules/pm-orchestrator.md`
- [ ] Test each instance: launch it from its folder and ask for status. It should report only on its domain.

**Integrations, as needed**
- [ ] Configure them in `~/.claude.json`
- [ ] Write the routing rules: connection, workspace, channels
- [ ] Scope access per instance

**Decision discipline**
- [ ] Write go, pivot and kill criteria before testing
- [ ] Set up the advisory council in `.claude/rules/advisory-council.md`
- [ ] Commit to a hard decision date before the sprint starts
