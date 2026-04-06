# Talent Registry Specification

**Version:** 0.1
**Author:** Mark Walden
**Date:** April 2026

---

## The Farm

A talent registry is not a library. It's a farm. Libraries store things. Farms grow things.

The shepherd doesn't walk into a library every morning to look up how to tend sheep. He walks onto a farm that's already alive — the fence is holding, the dog is moving, the herd is where he left it. And every day the farm produces something new. More wool. More milk. A lamb that wasn't there yesterday.

The Talent Registry works the same way. Agents don't just read talents. They grow them. Every session is a season. Every task is a field. Every failure is compost that feeds the next harvest.

---

## 1. The Three Roles

### 1.1 The Shepherd (Human Keeper)

The shepherd sees the whole farm. He doesn't shear the sheep or chase strays — he decides which fields to plant, when to move the herd, and when to call the dog.

**In the registry:**
- Sets the direction — which domains to probe, which stacks to test
- Reviews new talents before they're verified
- Prunes dead talents that no longer work
- Decides when a talent is ready for the public registry

### 1.2 The Sheep Dog (Orchestrator)

The sheep dog doesn't produce wool. It moves things. It reads the field, knows where the gaps are, and drives the herd toward them.

**In the registry:**
- Reads the registry on every task startup
- Matches existing talents to the current task
- Identifies gaps — "no talent exists for this situation"
- Routes tasks to the agents best suited to discover new talents
- After every task, checks: "did we learn something new?"
- Triggers talent documentation when a novel pattern succeeds

### 1.3 The Herd (Executor Agents)

The sheep don't know about the farm. They know about the grass in front of them. They eat, they produce, they move when the dog says move.

**In the registry:**
- Execute tasks using existing talents
- Encounter new situations during execution
- Report outcomes — success, failure, unexpected behavior
- Produce raw material (logs, outputs, patterns) that becomes talents

---

## 2. The Growth Cycle

Every talent goes through four seasons:

### 2.1 Discovery (Spring)

An agent encounters a new situation during real work. Not a test. Not a drill. Real work, real stakes.

```
Agent hits unknown situation
  → No matching talent in registry
  → Agent attempts solution from skills/knowledge
  → Solution works (or fails instructively)
  → Raw pattern is captured
```

**Output:** A draft talent — unverified, rough, but real.

### 2.2 Documentation (Summer)

The raw pattern is formalized into a talent file. This can happen automatically (agent documents its own discovery) or manually (shepherd or dog captures it).

**Requirements for a valid talent:**
- Exact commands — copy-paste, no placeholders that require guessing
- Parameters with real examples
- At least one gotcha (if there are none, you haven't tested enough)
- Provenance — who, when, during what
- Proof — the output that proves it worked

**The sheep dog's role:** After every session, the dog reviews agent logs for undocumented patterns. If an agent did something novel that worked, the dog prompts: "Document this as a talent."

### 2.3 Verification (Fall)

A different agent (or the same agent in a new context) reads the talent and follows it. If the result matches, the talent is verified.

```
Talent marked unverified
  → Different agent reads it
  → Follows steps exactly
  → Gets documented result
  → Talent marked verified
  → Verification recorded with date, agent, context
```

**The shepherd's role:** Reviews verified talents. Approves for the public registry or flags for refinement.

### 2.4 Pruning (Winter)

Talents decay. APIs change. Environments shift. A talent that worked in March might break in June.

```
Scheduled talent health check
  → Dog runs each talent's verification command
  → Pass: talent stays
  → Fail: talent flagged for update or deprecation
  → Shepherd decides: fix, archive, or delete
```

**Cadence:** Monthly for active talents. Quarterly for the full registry.

---

## 3. The Relentless Loop

The registry doesn't wait for work to come. It drives against the stack.

### 3.1 Stack Probing

The sheep dog maintains a **stack map** — every tool, API, platform, and integration the farm uses. On a schedule (or when idle), the dog assigns agents to probe the stack:

```
For each tool in the stack:
  → What talents exist for this tool?
  → What common use cases have no talent?
  → Assign an agent to test the gap
  → Document result as talent or gotcha
```

This is the agent intelligently and relentlessly building against the stack. Not randomly. Not when asked. Systematically. Every gap is a field waiting to be planted.

### 3.2 Edge Case Mining

When an agent hits an error during normal work, the dog doesn't just log it. It asks:

- Is this a known gotcha in an existing talent?
- If yes: is the gotcha documented? If not, update the talent.
- If no: is this a new failure mode worth documenting?
- Could this failure have been avoided with a talent that doesn't exist yet?

Every error is a seed. The dog decides which ones to plant.

### 3.3 Cross-Pollination

When Agent A on Machine 1 discovers a talent, the dog pushes it to all machines. But it doesn't stop there. It asks:

- Does this talent have equivalents on other platforms?
- Agent A discovered telegram-send-photo. Does a discord-send-photo talent exist? A slack-send-photo? If not, assign an agent to test it.

One discovery spawns a family of talents. The dog drives the expansion. The shepherd reviews the harvest.

### 3.4 The Metrics

The farm tracks:

| Metric | What it measures |
|--------|-----------------|
| **Talents discovered / week** | Is the farm growing? |
| **Talents verified / week** | Is the growth healthy? |
| **Talents pruned / month** | Is the farm maintained? |
| **Gap coverage %** | What % of the stack has talents? |
| **Talent hit rate** | How often do agents use talents vs. going raw? |
| **Time saved per talent** | How much faster is the talent vs. no talent? |
| **Discovery source** | Which agents discover the most? Which stacks produce the most? |

A healthy farm: high discovery, high verification, low pruning, increasing coverage.

---

## 4. The Orchestration Layer

### 4.1 Task Intake

```
Task arrives
  ↓
Sheep dog decomposes into fundamentals
  ↓
For each fundamental:
  → Search registry for matching talents
  → If talent exists: attach to task context
  → If no talent: flag as discovery opportunity
  ↓
Route to executor agent with:
  - Task description
  - Matched talents (weighted by relevance)
  - Discovery flags ("if you find a pattern for X, document it")
```

### 4.2 Execution

```
Agent receives task + talents + discovery flags
  ↓
Execute using talents where available
  ↓
For novel situations:
  → Attempt from skills/knowledge
  → Log the approach and outcome
  ↓
Return results + execution log
```

### 4.3 Post-Execution

```
Sheep dog reviews execution log
  ↓
For each novel pattern that succeeded:
  → Generate draft talent
  → Queue for verification
  ↓
For each failure:
  → Check if existing talent needs gotcha update
  → Check if new gotcha talent needed
  ↓
Update registry
  ↓
Notify shepherd of new discoveries
```

### 4.4 Scheduled Runs

The dog doesn't only work when tasks arrive. It runs on a schedule:

| Schedule | Action |
|----------|--------|
| **Every session** | Match talents to task, flag gaps |
| **Daily** | Review logs for undocumented patterns |
| **Weekly** | Probe stack for uncovered gaps, cross-pollinate |
| **Monthly** | Run verification health checks on all talents |
| **Quarterly** | Full registry audit, prune dead talents |

---

## 5. The Registry Protocol

### 5.1 File Format

Talents are markdown. Always. No JSON configs, no YAML manifests, no databases required. A human can read a talent file and follow it with no tooling.

The optional `warehouse.db` (SQLite) indexes talents for fast search but is never the source of truth. The markdown files are the source of truth. The database is regenerated from them.

### 5.2 Talent States

```
DRAFT → DOCUMENTED → VERIFIED → ACTIVE → DEPRECATED → ARCHIVED
```

| State | Meaning |
|-------|---------|
| **Draft** | Raw pattern captured, not yet formatted |
| **Documented** | Formatted with all required sections |
| **Verified** | Confirmed by a second agent/session |
| **Active** | In production use, healthy |
| **Deprecated** | Failing verification, needs update |
| **Archived** | No longer applicable, kept for history |

### 5.3 Versioning

Talents are git-versioned. When a talent is updated (new gotcha, updated commands, environment change), it's a new commit with a clear message. The git log IS the talent's history.

No version numbers in the file. Git handles that.

### 5.4 Namespacing

For shared registries with multiple contributors:

```
talents/
├── comms/
│   ├── telegram-send-photo.md
│   ├── telegram-send-voice.md
│   └── discord-send-image.md
├── tools/
│   ├── cli-anything-harness.md
│   └── roblox-studio-mcp.md
├── ops/
│   ├── telegram-bridge-memory.md
│   └── launchagent-deploy.md
└── media/
    ├── edge-tts-generation.md
    └── ffmpeg-ogg-conversion.md
```

---

## 6. Integration

### 6.1 Any Agent, Any Model

The registry is model-agnostic. The talent file is markdown. If the agent can read text, it can use talents. Claude, GPT, Gemini, Llama, Gemma, Phi — doesn't matter. The talent doesn't care who reads it.

### 6.2 The Startup Prompt

The minimum integration:

```
You have access to a Talent Registry at [path].
Before attempting any novel technique, check the registry.
If a talent exists for your task, follow it exactly.
If no talent exists and you discover a working pattern, document it.
Use the template at templates/talent-template.md.
```

That's it. Any agent. Any model. One paragraph.

### 6.3 The Full Integration (SDK)

For agents with deeper integration:

```python
from talent_registry import Registry, SheepDog

# Load the registry
registry = Registry("/path/to/talents")

# Create the orchestrator
dog = SheepDog(registry)

# Before a task
talents = dog.match(task_description)
gaps = dog.identify_gaps(task_description)

# After a task
dog.review(execution_log)
new_talents = dog.extract_discoveries(execution_log)

# Scheduled
dog.probe_stack(stack_map)
dog.verify_all()
dog.prune()
```

### 6.4 The Shepherd's Dashboard

The shepherd needs visibility:

- Total talents (by state, by category)
- Recent discoveries
- Verification queue
- Stack coverage map
- Agent leaderboard (who's discovering the most)
- Health status (how many failing verification)

---

## 7. The Gift

This spec is open. The format is open. The protocol is open. The SDK will be open.

Because a talent locked in one keeper's farm helps one keeper. A talent shared openly helps every keeper on every farm in every trade. The plumber's agent in New Hampshire discovers a talent. The electrician's agent in Tokyo uses it the next day. The registry gets smarter. Every farm benefits.

The talent is the gift. The registry is how you give it away.

---

*Built in The Shop. Grown on The Farm. Given freely.*

*Mark Walden — April 2026*
