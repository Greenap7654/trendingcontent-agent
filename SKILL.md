---
name: trendingcontent
description: >
  Research trending topics across Reddit, X/Twitter, YouTube, HackerNews, Bluesky
  and more over a configurable time window (7–90 days), then generate ready-to-post
  social media content (Twitter/X threads, LinkedIn posts, Instagram captions) in
  English, Spanish, or Portuguese.
compatibility:
  - hermes
  - openclaw
metadata:
  author: "@gabogabucho"
  contributor: "@mvanhorn"
  version: "1.0.0"
  license: MIT
  repository: https://github.com/gabogabucho/trendingcontent-agent
  requires:
    env:
      - SCRAPECREATORS_API_KEY   # required — ScrapeCreators API key
      - BRAVE_API_KEY            # optional — Brave Search API key (enriches results)
    python: ">=3.9"
    packages:
      - yt-dlp
      - requests
---

# trendingcontent

A two-phase skill: first it **researches** what's trending on a topic across major
platforms over a configurable number of days; then it **generates** polished social
media content based on those findings.

Built on top of [last30days](https://github.com/mvanhorn/last30days-skill) by
[@mvanhorn](https://github.com/mvanhorn), extended with variable time windows and
multi-platform content generation.

## Setup

1. Install Python dependencies:
   ```
   pip install -r skills/social-media/trendingcontent/requirements.txt
   ```
2. Set required environment variables in `.env`:
   ```
   SCRAPECREATORS_API_KEY=your_key_here
   BRAVE_API_KEY=your_key_here          # optional but recommended
   ```

## Usage

Invoke this skill when the user asks to:
- "Find what's trending about [topic] this week / last 15 days / this month"
- "Generate a Twitter thread about [topic] based on recent trends"
- "Create a LinkedIn post about what's happening with [topic]"
- "Write Instagram content about [topic] trends"
- "Research [topic] trends and create social media posts"

## How to invoke

Run the skill script:

```
python skills/social-media/trendingcontent/scripts/trendingcontent.py <topic> [options]
```

### Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `topic` | (required) | Topic to research |
| `--days=N` | 30 | Time window: 7–90 days |
| `--platform` | all | `twitter` \| `linkedin` \| `instagram` \| `all` |
| `--tone` | professional | `professional` \| `casual` \| `educational` \| `viral` |
| `--lang` | en | `en` \| `es` \| `pt` |
| `--sources=S1,S2` | all | Only use these sources (comma-separated) |
| `--disable=S1,S2` | none | Disable specific sources (comma-separated) |
| `--list-sources` | — | Show all available sources and exit |
| `--quick` | false | Fewer sources, faster results |
| `--deep` | false | More sources, thorough research |
| `--research-only` | false | Output only research, skip content generation |

### Examples

```bash
# Research AI trends from the last 7 days and generate all platform content
python trendingcontent.py "artificial intelligence" --days=7 --platform=all

# Generate a professional LinkedIn post about Web3 in Spanish
python trendingcontent.py "web3" --days=15 --platform=linkedin --lang=es --tone=professional

# Get a viral Twitter thread about climate tech in the last 30 days
python trendingcontent.py "climate tech" --days=30 --platform=twitter --tone=viral

# Niche topic — only Reddit and YouTube (skip HackerNews, Twitter, etc.)
python trendingcontent.py "sourdough bread" --sources=reddit,youtube,brave

# Disable HackerNews (too tech-specific) and TikTok
python trendingcontent.py "fintech" --disable=hackernews,tiktok

# List all available sources
python trendingcontent.py --list-sources

# Research only (no content generation)
python trendingcontent.py "LLMs" --days=14 --research-only
```

### Source management

Different topics benefit from different source mixes:

| Topic type | Recommended sources |
|------------|-------------------|
| General / pop culture | `reddit,twitter,youtube,tiktok,bluesky` |
| Tech / developer | `reddit,twitter,hackernews,youtube,brave` |
| Niche lifestyle (food, fitness, etc.) | `reddit,youtube,instagram,tiktok` |
| Finance / investing | `reddit,twitter,brave` (disable hackernews, tiktok) |
| Academic / research | `hackernews,brave,youtube` |

## Output

The skill outputs two sections:

1. **Research summary** — ranked trending content from Reddit, X, YouTube, HackerNews,
   Bluesky, and (if configured) Brave Search. Includes titles, engagement scores, and
   source URLs.

2. **Content generation brief** — a structured prompt block with:
   - Platform-specific format instructions (thread structure, character limits, hashtag rules)
   - Tone guidance
   - The research data as factual grounding

The agent should use the content generation brief to produce the final social media copy.

## Agent workflow

```
User request
    │
    ▼
[Phase 1] trendingcontent.py → last30days.py research engine
    │       Sources: Reddit, X, YouTube, HackerNews, Bluesky, Brave
    │       Scoring: engagement + recency + deduplication
    ▼
[Phase 2] Content generation brief
    │       Platform specs + tone + research data
    ▼
Agent generates final social media copy
```

## Sources researched

| Source | Requires |
|--------|----------|
| Reddit | ScrapeCreators API |
| X/Twitter | ScrapeCreators API |
| YouTube | ScrapeCreators API + yt-dlp |
| TikTok | ScrapeCreators API |
| Bluesky | ScrapeCreators API |
| HackerNews | Public API (no key needed) |
| Brave Search | Brave API key (optional) |

## Credits

- Original research engine: [last30days-skill](https://github.com/mvanhorn/last30days-skill) by [@mvanhorn](https://github.com/mvanhorn)
- Social media generation layer: [@gabogabucho](https://twitter.com/gabogabucho)
- License: MIT
