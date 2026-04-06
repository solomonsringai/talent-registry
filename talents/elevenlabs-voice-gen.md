# Talent: ElevenLabs Voice Generation

> Generate narration/voice audio from text using the ElevenLabs API.
> Any agent can use this. No bot process needed — just curl.

## When to Use
- Narrate scripts, documentaries, video content
- Generate voice messages for Telegram or any output
- Test narration pacing before committing to visuals
- Create audiobook chapters

## API Key

Set `ELEVENLABS_API_KEY` in your environment before running commands.

## Available Voices (Heavy Hitters)

| Voice | ID | Vibe |
|-------|-----|------|
| **Storm Styles** (default) | `YI5bDiiDOYHHb2eLadHv` | Dark, suspenseful. Sam Elliott meets Ken Burns. |
| Bill | `pqHfZKP75CvOlQylNhV4` | Wise, mature, balanced. Old American. |
| George | `JBFqnCBsd6RMkjVDRZzb` | Warm, captivating storyteller. British. |
| Brian | `nPczCjzI2devNBz1zQrb` | Deep, resonant, comforting. |
| Daniel | `onwK4e9ZLuTAKqWW03F9` | Steady broadcaster. British. |
| True | `tZssYepgGaQmegsMEXjK` | Crime & horror narrator. |
| Chuck Miller | `HIGUfNOdjuWQwwapnTRW` | Deep, raspy, American. Trades voice. |
| Eric | `cjVigY5qzO86Huf0OWal` | Smooth, trustworthy. Explainer. |
| Adam | `pNInz6obpgDQGcFmaJgB` | Dominant, firm. Declarations. |
| Roger | `CwhRBWXzGAHq8TQ4Fs17` | Laid-back, casual. |
| Alice | `Xb7hH8MSUJpSbSDYk0k2` | Clear educator. British. |
| Lily | `pFZP5JQG7iQjIQuC4Bku` | Velvety actress. British. |
| Vishal | `mns5K1FhCnNocEbmYxhJ` | Deep, authoritative. Indian. |

Full roster: 27 voices. Run the list command to see all.

## Commands

### Generate Voice (basic)
```bash
curl -s --request POST \
  --url "https://api.elevenlabs.io/v1/text-to-speech/VOICE_ID" \
  --header "xi-api-key: $ELEVENLABS_API_KEY" \
  --header "Content-Type: application/json" \
  --data '{
    "text": "Your text here.",
    "model_id": "eleven_multilingual_v2",
    "voice_settings": {
      "stability": 0.6,
      "similarity_boost": 0.8,
      "style": 0.4
    }
  }' \
  --output /path/to/output.mp3
```

### Generate Voice (Storm Styles, one-liner)
```bash
curl -s --request POST \
  --url "https://api.elevenlabs.io/v1/text-to-speech/YI5bDiiDOYHHb2eLadHv" \
  --header "xi-api-key: $ELEVENLABS_API_KEY" \
  --header "Content-Type: application/json" \
  --data '{"text": "Your text here.", "model_id": "eleven_multilingual_v2", "voice_settings": {"stability": 0.6, "similarity_boost": 0.8, "style": 0.4}}' \
  --output output.mp3
```

### List All Voices
```bash
curl -s --request GET \
  --url "https://api.elevenlabs.io/v1/voices" \
  --header "xi-api-key: $ELEVENLABS_API_KEY" | python3 -c "
import json, sys
data = json.load(sys.stdin)
for v in data.get('voices', []):
    print(f\"{v['voice_id']}: {v['name']}\")"
```

### Check Credit Balance
```bash
curl -s --request GET \
  --url "https://api.elevenlabs.io/v1/user/subscription" \
  --header "xi-api-key: $ELEVENLABS_API_KEY" | python3 -c "
import json, sys
d = json.load(sys.stdin)
print(f\"Characters used: {d['character_count']}/{d['character_limit']}\")"
```

## Parameters

| Parameter | Default | Range | Effect |
|-----------|---------|-------|--------|
| `stability` | 0.6 | 0.0-1.0 | Lower = more expressive/variable, Higher = more consistent |
| `similarity_boost` | 0.8 | 0.0-1.0 | How closely to match the original voice |
| `style` | 0.4 | 0.0-1.0 | Style exaggeration — higher = more dramatic |
| `model_id` | `eleven_multilingual_v2` | See docs | Best quality model for narration |

## Cost Math
- ~150 words/minute at documentary pace
- ~5.5 characters/word average
- 10 minutes of narration = ~1,500 words = ~8,250 characters
- Check your subscription tier for monthly character limits

## Gotchas
- Use `eleven_multilingual_v2` for best quality narration
- Ellipses (...) create natural pauses in speech — use them for pacing
- Very long texts should be split into segments (aim for 500-1000 characters per call for best quality)
- Output is MP3 by default
- API returns raw audio bytes — pipe to file, not stdout

## Proof
- Tested: March 2026
- Storm Styles voice, HTTP 200, 84,889 bytes, clean audio
