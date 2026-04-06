# Talent: Telegram Bridge Conversation Memory

> Fix the "forgetting" problem across all Telegram bridge bots (claude-code-telegram). By default, the bridge stores messages in SQLite but never replays them to Claude on session resume. This talent patches that gap and hardens session persistence.

## The Problem

The `claude-code-telegram` bridge (used by Marshal Martin, Cameron, Chuck, XPulse, and any future bot) has three amnesia vectors:

1. **No history injection** — Messages are stored in SQLite (`messages` table) but never loaded back into the prompt. `options.resume = session_id` restores CLI working state (files, tools), NOT conversation context. Claude starts blank every message.
2. **24-hour session timeout** — `SESSION_TIMEOUT_HOURS=24` auto-expires sessions. After a day of inactivity, the session is marked `is_active=FALSE` and the next message starts fresh.
3. **SDK timeout nukes sessions** — `CLAUDE_TIMEOUT_SECONDS=300` (5 min). When the SDK times out, the facade catches the error, destroys the session, and creates a new one. Context gone. Marshal Martin was hitting this 3x/hour.

## The Fix (3 files per bot)

### 1. `src/claude/facade.py` — Conversation History Injection

Add a `storage` parameter to `ClaudeIntegration.__init__()` and a `_build_history_context()` method that loads prior messages from SQLite and prepends them to the prompt.

**Key constants:**
```python
MAX_HISTORY_MESSAGES = 20   # How many prior exchanges to load
MAX_HISTORY_CHARS = 8000    # Character cap to avoid bloating prompts
```

**Where to inject:**
- In `run_command()`, after determining `should_continue` and `claude_session_id`
- Build history with `await self._build_history_context(claude_session_id)`
- Prepend to prompt: `enriched_prompt = history_context + "Current message:\n" + prompt`
- Use `enriched_prompt` in BOTH the normal `_execute()` call AND the fallback fresh-session retry

**The method:**
```python
async def _build_history_context(self, session_id: str) -> str:
    if not self.storage or not session_id:
        return ""
    try:
        messages = await self.storage.messages.get_session_messages(
            session_id, limit=MAX_HISTORY_MESSAGES
        )
        if not messages:
            return ""
        messages = list(reversed(messages))  # newest-first -> chronological
        history_parts = []
        total_chars = 0
        for msg in messages:
            entry = f"User: {msg.prompt}\nAssistant: {msg.response}"
            if total_chars + len(entry) > MAX_HISTORY_CHARS:
                break
            history_parts.append(entry)
            total_chars += len(entry)
        if not history_parts:
            return ""
        return (
            "\n\n# Conversation History (previous messages in this session)\n"
            "Use this context to maintain continuity. The user expects you "
            "to remember what was discussed.\n\n"
            + "\n\n---\n\n".join(history_parts)
            + "\n\n---\n\n"
        )
    except Exception:
        return ""
```

**Critical: also fix the fallback path.** When session resume fails and the facade retries with a fresh session, use `enriched_prompt` (with history), NOT the raw `prompt`. This is the path that fires on every SDK timeout — the exact moment context matters most.

### 2. `src/main.py` — Wire Storage

Pass storage to the facade constructor:
```python
claude_integration = ClaudeIntegration(
    config=config,
    sdk_manager=sdk_manager,
    session_manager=session_manager,
    storage=storage,          # <-- add this
)
```

The `storage` object is already created earlier in `create_bot()`. No new imports needed.

### 3. `.env` — Harden Timeouts

```env
CLAUDE_TIMEOUT_SECONDS=600      # was 300 (5min -> 10min)
SESSION_TIMEOUT_HOURS=168       # was 24  (1 day -> 7 days)
```

## After Patching

Restart the LaunchAgent:
```bash
launchctl stop com.theshop.<bot-name> && launchctl start com.theshop.<bot-name>
```

Verify startup is clean:
```bash
tail -10 ~/Projects/<bot-dir>/*.log | grep -v getUpdates
```

## Verification

Check that history injection is working in the logs:
```bash
grep "Injected conversation history" ~/Projects/<bot-dir>/*.log
```

You should see entries like:
```json
{"session_id": "abc123", "message_count": 5, "chars": 3200, "event": "Injected conversation history"}
```

## Notes

- `facade.py` is identical across all 4 bots — copy once, deploy everywhere
- `sdk_integration.py` differs slightly (Cameron/Chuck/XPulse have agent identity loading) but is NOT modified by this fix
- The `messages` table already exists in all bot databases — no migration needed
- `get_session_messages()` already exists in `src/storage/repositories.py` — just wasn't being called
- History is only injected on resume, not on fresh `/new` sessions
- The TYPE_CHECKING import of Storage avoids circular import issues

## Applies To

| Bot | Project Dir | LaunchAgent |
|-----|-------------|-------------|
| Marshal Martin | `~/Projects/claude-code-telegram/` | `com.theshop.marshal-martin` |
| Cameron | `~/Projects/cameron-bot/` | `com.theshop.cameron` |
| Chuck | `~/Projects/chuck-bot/` | `com.theshop.chuck` |
| XPulse | `~/Projects/dottie-bot/` | `com.theshop.dottie` |

---

*Created: 2026-03-20 by Claude Code (The Specialist)*
*Applied to all 4 bots same session*
