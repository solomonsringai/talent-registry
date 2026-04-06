# Talent: Google AI Studio (Gemini, Veo, Imagen)

> Generate video, images, and text using Google AI Studio APIs.
> Free tier, wide open as of March 2026.
> Any agent can use this.

## When to Use
- Generate AI video clips (Veo)
- Generate AI images (Imagen)
- Use Gemini for script assistance, analysis, or multimodal tasks
- Complement ElevenLabs video gen with additional visual options

## API Key

Set `GOOGLE_AI_STUDIO_API_KEY` in your environment before running commands.
Get a key at https://aistudio.google.com/apikey

## Commands

### Gemini Text Generation
```bash
curl -s "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=$GOOGLE_AI_STUDIO_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [{"text": "Your prompt here"}]
    }]
  }' | python3 -c "
import json, sys
data = json.load(sys.stdin)
for c in data.get('candidates', []):
    for p in c.get('content', {}).get('parts', []):
        print(p.get('text', ''))"
```

### Imagen (Image Generation)
```bash
curl -s "https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-002:predict?key=$GOOGLE_AI_STUDIO_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "instances": [{"prompt": "A cinematic workshop with warm tungsten lighting, tools on a workbench, dust particles in the air"}],
    "parameters": {"sampleCount": 1}
  }' | python3 -c "
import json, sys, base64
data = json.load(sys.stdin)
for i, pred in enumerate(data.get('predictions', [])):
    img = base64.b64decode(pred['bytesBase64Encoded'])
    with open(f'generated_image_{i}.png', 'wb') as f:
        f.write(img)
    print(f'Saved generated_image_{i}.png ({len(img)} bytes)')"
```

### Veo (Video Generation)
Check current API availability at https://ai.google.dev/docs
As of March 2026, Veo is available in AI Studio web UI.
API access may require specific model names — check docs for current endpoints.

## Models Available
| Model | Purpose |
|-------|---------|
| `gemini-2.0-flash` | Fast text generation, analysis |
| `gemini-2.0-pro` | Higher quality text, multimodal |
| `imagen-3.0-generate-002` | Image generation |
| Veo 3 | Video generation (check current availability) |

## Parameters
| Parameter | Notes |
|-----------|-------|
| `sampleCount` | Number of images to generate (Imagen) |
| `temperature` | Creativity (Gemini text, 0.0-2.0) |
| `maxOutputTokens` | Response length limit (Gemini text) |

## Gotchas
- Google AI Studio has rate limits on free tier — generous but not unlimited
- Imagen may have content filters — avoid prompts that trigger safety blocks
- Veo API access may differ from web UI availability
- Use the AI Studio specific key, not a Google Cloud or Maps key
- Model names change — verify current model IDs at ai.google.dev

## Proof
- Tested: March 2026
- Free tier confirmed working
