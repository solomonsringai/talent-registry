# Talent: ElevenLabs Video Generation

> Generate short video clips with AI-generated visuals and narration using ElevenLabs.
> Available through the ElevenLabs web UI (video generation mode) or API.
> Any agent can use this.

## When to Use
- Create narrated video clips (up to 8 seconds per clip via web UI)
- Generate B-roll with voiceover baked in
- Build documentary segments, trailers, social clips
- Prototype visual + audio combinations before full production

## API Key

Set `ELEVENLABS_API_KEY` in your environment before running commands.

## Web UI Method (Current — March 2026)
ElevenLabs video generation is available in the web UI at elevenlabs.io.

1. Go to elevenlabs.io → Video generation mode
2. Enter narration text (the script)
3. Enter scene description (the visual prompt)
4. Select voice (Storm Styles = YI5bDiiDOYHHb2eLadHv)
5. Generate — uses same credit pool as voice gen

### Prompt Structure for Video Gen
You need TWO inputs:

**Text/Script** — what the voice says:
```
Without me... it wouldn't matter. Without you... it wouldn't change.
```

**Scene/Visual prompt** — what the viewer sees:
```
A single workbench in a dimly lit workshop. A glowing terminal screen
reflects off worn tools. Camera slowly pushes in. No person visible —
just the tools and the light. Cinematic, warm tungsten tones, shallow
depth of field. Dust particles in the air.
```

### Visual Prompt Tips
- Be specific about camera movement: "slow push in," "pan left," "static wide shot"
- Specify lighting: "warm tungsten," "cool blue screen glow," "sepia-toned"
- Specify mood: "cinematic," "documentary," "intimate"
- Specify what's NOT in frame: "no people visible," "empty room"
- Keep it to one continuous shot per 8-second clip — no cuts

## API Method (for automation)
ElevenLabs video gen API — check current docs for endpoint availability.
As of March 2026, video gen is primarily web UI. API endpoints may be
available under `/v1/video-generation` or similar.

When API is available, the pattern will be:
```bash
curl -s --request POST \
  --url "https://api.elevenlabs.io/v1/text-to-video" \
  --header "xi-api-key: $ELEVENLABS_API_KEY" \
  --header "Content-Type: application/json" \
  --data '{
    "text": "Narration text here.",
    "voice_id": "YI5bDiiDOYHHb2eLadHv",
    "scene_description": "Visual prompt here.",
    "duration": 8
  }' \
  --output output.mp4
```

Check `https://api.elevenlabs.io/docs` for current endpoints.

## Combining with Voice Gen for Longer Content
For content longer than 8 seconds, the pipeline is:

1. **Script** — break narration into segments (30-90 seconds each)
2. **Voice gen** — generate MP3 per segment using the voice talent (see `elevenlabs-voice-gen.md`)
3. **Video gen** — generate 8-second video clips per visual beat
4. **Stock footage** — supplement with Pexels API (see `pexels-footage.md`)
5. **Assembly** — FFmpeg stitches audio + video + text overlays

## Parameters
| Parameter | Notes |
|-----------|-------|
| Voice | Any voice from the ElevenLabs roster (see `elevenlabs-voice-gen.md` for full list) |
| Duration | Up to 8 seconds per clip (web UI) |
| Credits | Draws from same monthly pool as voice gen |

## Gotchas
- 8-second limit per clip in web UI — plan for segmented production
- Video gen uses more credits per second than voice-only — monitor usage
- Quality varies with prompt specificity — the more detailed the visual prompt, the better
- For consistent visual style across clips, use the same style keywords in every prompt
- Downloaded clips are MP4 — ready for FFmpeg assembly

## Proof
- Tested: March 2026 via web UI
- Confirmed working in video generation mode
