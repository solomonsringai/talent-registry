# Talent: Send Voice Message via Telegram

> Any agent can generate speech from text and deliver it as a Telegram voice message. Three commands: generate, convert, send.

## The Pipeline

```bash
# 1. Generate speech with Edge TTS
edge-tts --text "Your message here" \
  --voice en-US-GuyNeural \
  --write-media /private/tmp/claude-501/voice.mp3

# 2. Convert to OGG Opus (Telegram requires this for voice messages)
ffmpeg -y -i /private/tmp/claude-501/voice.mp3 \
  -c:a libopus -b:a 64k \
  /private/tmp/claude-501/voice.ogg

# 3. Send via Telegram
curl -s -X POST "https://api.telegram.org/bot<BOT_TOKEN>/sendVoice" \
  -F chat_id=<USER_ID> \
  -F voice=@"/private/tmp/claude-501/voice.ogg" \
  -F caption="Optional caption text"
```

## One-Liner Version

```bash
edge-tts --text "Message here" --voice en-US-GuyNeural --write-media $TMPDIR/v.mp3 && \
ffmpeg -y -i $TMPDIR/v.mp3 -c:a libopus -b:a 64k $TMPDIR/v.ogg 2>/dev/null && \
curl -s -X POST "https://api.telegram.org/bot<BOT_TOKEN>/sendVoice" \
  -F chat_id=<USER_ID> -F voice=@"$TMPDIR/v.ogg"
```

## Available Voices

| Voice | Style | Good For |
|-------|-------|----------|
| `en-US-GuyNeural` | Male, clear, neutral | Status updates, reports |
| `en-US-AndrewNeural` | Male, warm, conversational | Narration, longer messages |
| `en-US-ChristopherNeural` | Male, authoritative | Announcements |
| `en-US-JennyNeural` | Female, professional | Briefings |
| `en-US-AriaNeural` | Female, natural | General purpose |

Full voice list: `edge-tts --list-voices`

### Voice Tuning

```bash
# Slow down for narration
edge-tts --text "..." --voice en-US-AndrewNeural --rate=-10% --write-media out.mp3

# Speed up for quick updates
edge-tts --text "..." --voice en-US-GuyNeural --rate=+15% --write-media out.mp3

# Adjust pitch
edge-tts --text "..." --voice en-US-GuyNeural --pitch=+5Hz --write-media out.mp3
```

## Parameters

| Param | What | Example |
|-------|------|---------|
| `BOT_TOKEN` | Any Shop bot token (see tokens.md) | `8727917950:AAFpLBFnf1...` |
| `USER_ID` | Telegram user ID | `8525138299` (Mark) |
| `voice` | OGG Opus file, prefixed with `@` | `@/private/tmp/claude-501/voice.ogg` |
| `caption` | Optional text shown with the voice bubble | `"Status update from Cameron"` |

## Why OGG Opus?

Telegram displays voice messages as a playable waveform bubble (like WhatsApp voice notes). This ONLY works with OGG Opus format. MP3 sent via `sendVoice` will either fail or display as a regular audio file without the voice UI. The FFmpeg conversion step is required.

## Audio File Alternative

If you want to send an audio FILE (with title/artist metadata, like a podcast) instead of a voice message bubble:

```bash
curl -s -X POST "https://api.telegram.org/bot<BOT_TOKEN>/sendAudio" \
  -F chat_id=<USER_ID> \
  -F audio=@"/path/to/audio.mp3" \
  -F title="Episode Title" \
  -F performer="Cameron"
```

## When to Use This

- Delivering voice briefings or status updates to Mark
- Reading back generated scripts or narration drafts for review
- Sending audio previews before committing to full video assembly
- Any time Mark asks to "hear" something instead of read it
- Accessibility — voice is faster to consume than text on mobile

## Gotchas

- Edge TTS requires internet (it's Microsoft's cloud TTS, not local)
- Edge TTS was installed with `--break-system-packages` — fragile, may break on Python update
- Max voice message duration on Telegram: technically unlimited, but keep under 5 min for UX
- `$TMPDIR` resolves to the sandbox temp dir — use it for intermediate files
- Sandbox may block curl and edge-tts — use `dangerouslyDisableSandbox: true` if needed

## Dependencies

| Tool | Installed | Notes |
|------|-----------|-------|
| edge-tts | YES | `pip install edge-tts` (system-wide) |
| ffmpeg | YES | Via Homebrew, no libfreetype |
| curl | YES | System default |

---

*Discovered: 2026-03-19 by Cameron (Claude Code) during iPod Nano case session*
*Verified: Sent test voice to Mark via @Camerongrinbot — message_id 671, 7 seconds, 51KB*
