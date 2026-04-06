# Talent: Pexels Stock Footage & Images

> Pull free stock video and images via the Pexels API.
> Commercial license, no watermarks, no attribution required.
> Any agent can use this.

## When to Use
- Supplement AI-generated visuals with real footage
- Pull B-roll for documentaries and video content
- Source images for thumbnails, social posts, presentations
- When you need footage that looks real, not AI-generated

## API Key

Set `PEXELS_API_KEY` in your environment before running commands.
Get a key at https://www.pexels.com/api/

## Commands

### Search Videos
```bash
curl -s "https://api.pexels.com/videos/search?query=welding+sparks&per_page=5" \
  -H "Authorization: $PEXELS_API_KEY" | python3 -c "
import json, sys
data = json.load(sys.stdin)
for v in data.get('videos', []):
    best = [f for f in v['video_files'] if f.get('quality') == 'hd']
    url = best[0]['link'] if best else v['video_files'][0]['link']
    print(f\"{v['id']}: {v['url']}  Duration: {v['duration']}s\")
    print(f\"  Download: {url}\")
    print()"
```

### Download a Video
```bash
# Get the direct download URL from search results, then:
curl -L -o /path/to/output.mp4 "DIRECT_VIDEO_URL"
```

### Search Photos
```bash
curl -s "https://api.pexels.com/v1/search?query=workbench+tools&per_page=5" \
  -H "Authorization: $PEXELS_API_KEY" | python3 -c "
import json, sys
data = json.load(sys.stdin)
for p in data.get('photos', []):
    print(f\"{p['id']}: {p['url']}\")
    print(f\"  Original: {p['src']['original']}\")
    print(f\"  Large: {p['src']['large']}\")
    print()"
```

### Download a Photo
```bash
curl -L -o /path/to/output.jpg "PHOTO_URL"
```

## Parameters

| Parameter | Default | Notes |
|-----------|---------|-------|
| `query` | required | Search terms, use `+` for spaces |
| `per_page` | 15 | Results per page (max 80) |
| `page` | 1 | Pagination |
| `orientation` | any | `landscape`, `portrait`, `square` |
| `size` | any | `large`, `medium`, `small` |

## Search Tips for Documentary Content
- Use specific, cinematic terms: "aerial city timelapse," "factory workers slow motion"
- Combine subject + mood: "sunrise mountains peaceful," "server room blue light"
- For historical feel: "vintage machinery," "old workshop," "sepia industrial"
- For tech content: "code on screen," "typing keyboard dark," "server rack lights"
- For trades: "welding sparks," "pipe fitting," "construction site sunrise"

## License
- Free for commercial use
- No attribution required (but appreciated)
- Cannot sell unmodified Pexels content
- Cannot imply Pexels endorsement

## Gotchas
- Rate limit: 200 requests/hour, 20,000 requests/month
- Video files come in multiple qualities — filter for 'hd' or 'sd'
- Some videos are very short (3-5 seconds) — good for B-roll cuts
- Downloaded files can be large — check file size before bulk downloading
- Use FFmpeg to trim/resize clips to match your timeline

## Proof
- Tested: March 2026
- API confirmed working
