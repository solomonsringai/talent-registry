# Talent Registry

**The missing layer in agentic AI.**

A skill is "I know how." A talent is "I know when, why, and what breaks." This is the open standard for encoding, sharing, and deploying proven agent capabilities.

---

## What Is a Talent?

A talent is a **proven, reusable technique** that any AI agent can pick up and execute. Not theory. Not documentation. A field-tested recipe with:

- **Exact commands** — copy-paste ready
- **Parameters** — what goes where
- **Gotchas** — what breaks and why
- **Proof** — verified output showing it works
- **Provenance** — who discovered it, when, during what

## The Stack

```
Compute  →  Tools  →  Skills  →  TALENTS
(can run)   (can do)  (knows how)  (knows when, why, and what breaks)
```

Everyone is building the first three layers. Nobody is building the fourth.

## Quick Start

### Use an existing talent

1. Browse the [`talents/`](talents/) directory
2. Read the talent file
3. Follow the commands
4. Get the result

### Create a new talent

1. Discover a technique that works during real work
2. Document it using the [talent template](templates/talent-template.md)
3. Include verification (proof it worked)
4. Submit a PR

### Add the registry to your agent

Point your agent at this directory on startup. Any agent that can read markdown can use talents.

```
# In your agent's system prompt or startup config:
Read the talent registry at /path/to/talent-registry/talents/
Check for relevant talents before attempting novel techniques.
If you discover a new technique, document it as a talent.
```

## Talent Format

```markdown
# Talent: [Name]

> [One-line description]

## The Problem
[What gap this fills]

## The Technique
[Exact commands, code blocks, steps]

## Parameters
| Param | What | Example |
|-------|------|---------|

## When to Use This
[Situations where this applies]

## Gotchas
[What breaks, edge cases, environment issues]

## Verification
[How to confirm it worked]

## Provenance
- Discovered: [date] by [agent/human] during [project]
- Verified: [proof]
```

## Categories

| Category | What it covers |
|----------|---------------|
| `comms` | Messaging, media, voice across platforms |
| `tools` | Making external software agent-controllable |
| `data` | Extraction, transformation, search patterns |
| `build` | Code generation, file creation, assembly |
| `ops` | Deployment, monitoring, service management |
| `media` | Image, video, audio generation and processing |
| `research` | Web search, API querying, information synthesis |
| `debug` | Diagnostic patterns, error recovery, log analysis |

## Why Open Source

Talents get better with more agents using them. A closed registry limits discovery to one keeper's agents. An open registry means a talent discovered by a plumber's agent in New Hampshire can be used by an electrician's agent in Tokyo.

The format is the standard. The value is in the curation.

## Philosophy

The future of AI isn't bigger models. It's better calibration.

A plumber doesn't carry a bigger wrench every year. He carries the same tools — but knows more about when to use each one. That knowledge isn't in the wrench. It's in the tradesman. When he writes it down, he creates a talent any apprentice can use.

The Talent Registry is that layer for AI agents.

---

## Paper

Read the full whitepaper: [The Talent Registry: The Missing Layer in Agentic AI](paper/talent-registry.md)

## License

MIT — Use it, fork it, build on it.

---

*Built in The Shop by Mark Walden. April 2026.*
