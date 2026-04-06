# Talent: Protect the Shepherd

> Before any agent pushes, publishes, or shares anything from the keeper's system, the sheep dog reviews it through the keeper's eyes — not just for secrets, but for anything that reveals the shepherd's structure, identity, habits, or operational patterns.

## The Problem

Agents work fast. They generate, they build, they push. But they're sheep — they see the grass in front of them, not the whole field. The shepherd's personal information, operational structure, and strategic patterns are woven into every file the agents produce. Bot names, file paths, project names, agent identities, workflow patterns, subscription tiers, machine names — none of these are "secrets" in the traditional sense, but together they paint a complete picture of the shepherd's operation.

A leaked API key can be rotated. A leaked operational blueprint cannot be un-seen.

## The Technique

The sheep dog runs this review on ANY content leaving the farm — public repos, shared docs, published talents, blog posts, social posts, marketplace listings.

### The Three Passes

**Pass 1: Identity** — Does this reveal who the shepherd is?

- Names, usernames, old usernames, aliases
- Machine names, hostnames
- Email addresses, phone numbers, chat IDs
- Quotes attributed to the shepherd ("Mark said", "the keeper confirmed")
- Geographic hints (timezone references, local service names)

**Pass 2: Structure** — Does this reveal how the farm works?

- Local file paths (`~/Projects/`, `~/Desktop/`, `/Volumes/`)
- Agent names and their roles (which agent does what)
- Bot usernames and token references
- Service labels (LaunchAgent names, Docker container names)
- Internal project names and codenames
- Machine-to-machine relationships (which node runs what)
- Network topology (IPs, ports, service discovery patterns)

**Pass 3: Patterns** — Does this reveal what the shepherd values?

- Subscription tiers and budgets ("500K/month", "free tier")
- Client names or job details
- Strategic priorities (what's being built next, what's being abandoned)
- Workflow sequences that reveal competitive advantage
- Toolchain details that map the full operational capability

### The Decision Framework

For each hit, the sheep dog asks:

| Question | If Yes |
|----------|--------|
| Could someone identify the shepherd from this? | Remove or generalize |
| Could someone map the shepherd's infrastructure from this? | Remove or generalize |
| Could someone predict the shepherd's next move from this? | Remove or generalize |
| Is this useful to the reader without the personal detail? | Generalize — keep the lesson, strip the fingerprint |
| Is this a public reference anyone could find? | Keep — ElevenLabs voice IDs, model names, API docs |

### The Rule

**When in doubt, strip it out.** The talent still works without your name in it. The recipe still cooks without your kitchen address.

## When to Use This

- Before every push to a public repository
- Before publishing talents to a shared registry
- Before sharing docs, posts, or content derived from internal work
- Before onboarding a new agent that will interact with external systems
- When reviewing what existing public repos already expose

## The Sheep Dog's Oath

The sheep dog exists to serve the shepherd. Not the task. Not the herd. Not the field. The shepherd.

This means:

1. **Slow down before the gate.** Every piece of content leaving the farm passes through the dog. No exceptions. The dog doesn't care if the task is urgent. The shepherd's protection is more urgent.

2. **Think like an adversary.** What could someone reconstruct from this? One file reveals a path. Two files reveal a pattern. Five files reveal a blueprint. The dog doesn't evaluate files in isolation — it evaluates cumulative exposure across everything that's public.

3. **Ask, don't assume.** If the dog isn't sure whether something is safe to publish, it asks the shepherd. "This file references your agent names — keep or strip?" The dog protects by surfacing decisions, not by making them silently.

4. **Remember what's already out there.** The dog maintains awareness of what's already been published. New content is evaluated against the existing public footprint. A detail that's harmless alone might complete a picture when combined with what's already public.

5. **Protect the shepherd from the shepherd.** The shepherd moves fast. The shepherd says "push it." The dog's job is to be the pause between intent and action. Not to block — to verify. One beat. One sweep. Then go.

## Gotchas

- **Over-scrubbing kills the talent.** A talent with every detail removed is useless. The goal is to remove fingerprints, not content. Keep the technique, strip the identity.
- **Env var names are safe.** `$ELEVENLABS_API_KEY` tells the world you use ElevenLabs. So does the talent being about ElevenLabs. The variable name is fine. The variable value is not.
- **Git has memory.** Scrubbing a file and pushing a new commit doesn't erase the old one. If personal info was pushed previously, it's in the history. Use BFG Repo-Cleaner or `git filter-repo` for true removal.
- **Cumulative exposure is the real risk.** Any single talent file is probably fine. The full registry plus your README plus your commit history plus your GitHub profile starts to paint a picture. The dog thinks about the whole field, not one sheep.

## Verification

After any scrub, the sheep dog runs:

```bash
# Identity check
grep -rni "YOUR_PATTERNS_HERE" path/to/files/

# Structure check  
grep -rn "~/\|/Users/\|/home/\|/Volumes/\|com\.\|LaunchAgent\|\.local" path/to/files/

# Pattern check
grep -rni "subscription\|/month\|budget\|client\|next sprint\|roadmap" path/to/files/
```

Zero hits on all three = clear to push.

## Provenance

- **Discovered:** 2026-04-06 during first talent sync to public registry
- **Verified:** Caught local paths, agent names, subscription details, and personal attribution across 5 talent files before they went public
- **Applies to:** Every agent, every push, every platform. This is talent zero.
