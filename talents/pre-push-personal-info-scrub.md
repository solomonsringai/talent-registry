# Talent: Pre-Push Personal Info Scrub

> Audit files for personal identifiers before pushing to a public repository. Catch local paths, usernames, tokens references, agent names, and project-specific details that shouldn't go public.

## The Problem

Talent files (and any internal docs) are written for private use — they reference local paths, env file locations, bot names, user IDs, subscription details, and agent identities. Pushing these to a public repo leaks your operational structure even if no actual secrets are exposed.

## The Technique

### Step 1: Broad sweep for known personal patterns

```bash
grep -rn \
  "~/Desktop\|~/Projects\|~/Documents\|tokens\.md\|\.env\|walden\|simongrin\|YOUR_USERNAME" \
  path/to/files/
```

Replace `YOUR_USERNAME` and add any aliases, old usernames, or machine names.

### Step 2: Sweep for identifiers

```bash
grep -rn \
  "[0-9]\{10,\}\|@.*bot\|com\.theshop\|LaunchAgent\|YOUR_AGENT_NAMES" \
  path/to/files/
```

Catches: Telegram user/chat IDs (10+ digit numbers), bot usernames, LaunchAgent labels, internal service names.

### Step 3: Sweep for subscription/account details

```bash
grep -rni \
  "subscription\|my account\|my plan\|[0-9]\+K.*month\|YOUR_NAME.*confirmed\|YOUR_NAME.*said" \
  path/to/files/
```

Catches: plan details, quotes attributed to you, account-specific metrics.

### Step 4: Sweep for API key patterns (real secrets)

```bash
grep -rn \
  "sk-[a-zA-Z0-9]\{20,\}\|gho_[a-zA-Z0-9]\+\|AAF[a-zA-Z0-9]\+\|Bearer [^<\$]\|xox[bpas]-" \
  path/to/files/
```

Catches: OpenAI keys, GitHub tokens, Telegram bot tokens, hardcoded Bearer values, Slack tokens.

### Step 5: Fix each hit

For each match, decide:

| Pattern | Action |
|---------|--------|
| Local file paths (`~/Desktop/...`, `source ~/.env`) | Replace with generic instruction: "Set `VAR` in your environment" |
| Your name in attribution (`Mark confirmed`, `Cameron session`) | Generalize: "Tested March 2026" |
| Agent/bot names specific to your system | Remove or generalize to role: "orchestrator," "voice agent" |
| Subscription details (`500K/month`) | Remove or say "check your plan" |
| Actual API keys or tokens | DELETE IMMEDIATELY — rotate the key |
| Public IDs (ElevenLabs voice IDs, model names) | Keep — these are public catalog entries |

### Step 6: Final verification

```bash
grep -rn \
  "YOUR_USERNAME\|YOUR_MACHINE\|YOUR_AGENTS\|tokens\.md\|\.env\|~/Desktop\|~/Projects" \
  path/to/files/
```

Zero hits = clean to push.

## Parameters

| Param | What | Required | Example |
|-------|------|----------|---------|
| `path/to/files/` | Directory being pushed | Yes | `talents/` |
| Personal patterns | Your usernames, machine names, agent names | Yes | `jdoe\|oldhandle\|AgentAlpha\|AgentBeta` |
| ID patterns | Bot usernames, chat IDs, service labels | Yes | `@MyAgentBot\|1234567890\|com.myorg` |

## When to Use This

- Before every push to a public repo
- When migrating internal docs to open source
- When submitting talents to a shared registry
- Before creating a PR on someone else's repo from internal work
- Anytime files were written for private use and are going public

## Gotchas

- **Variable references are fine.** `$ELEVENLABS_API_KEY` is safe — it's a variable name, not a value. Don't over-scrub.
- **Public catalog IDs are fine.** ElevenLabs voice IDs, model names, API endpoint URLs — these are public. Don't remove them.
- **Local paths reveal structure.** `~/Desktop/Entropothy/shop-media.env` doesn't leak a secret, but it tells the world your project name, directory layout, and env file location. Strip it.
- **Quotes attributed to you are personal.** "Mark confirmed it works" — replace with "Confirmed working." The proof matters, the name doesn't.
- **Git remembers.** If you already pushed personal info and then scrub it, the old commit still has it. Use `git filter-branch` or BFG Repo-Cleaner to rewrite history, or rotate any exposed credentials.
- **Check the diff, not just the files.** Run `git diff --cached` before committing to see exactly what's going public.

## Verification

After scrubbing, run the full sweep one more time:

```bash
grep -rn "PERSONAL_PATTERN_1\|PERSONAL_PATTERN_2\|tokens\.md\|\.env\|~/Desktop\|~/Projects" path/to/files/
```

Expected output: empty. Zero matches.

Then check the staged diff:

```bash
git diff --cached
```

Read every line. If you wouldn't put it on a billboard, don't push it.

## Provenance

- **Discovered:** 2026-04-06 during talent-registry sync to solomonsringai/talent-registry
- **Verified:** Caught env file refs, local project paths, personal attribution quotes, agent names, and subscription details across 5 files before push
- **Applies to:** Any agent pushing to public repos, any platform, any file format
