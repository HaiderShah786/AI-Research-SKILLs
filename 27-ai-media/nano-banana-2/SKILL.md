---
name: nano-banana-2
description: Provides guidance for generating images with Google's Nano Banana 2 flash-tier text-to-image model via the RunComfy CLI. Use for rapid ideation, social-thumbnail batches, draft variants, and prompts requiring strong in-image typography. Route here when the user wants fast, cost-efficient image generation with predictable framing — not maximum realism.
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [Image Generation, Google, RunComfy, Text-to-Image, Gemini, AI Media]
dependencies: [runcomfy-cli>=1.0.0]
---

# Nano Banana 2 — Image Generation on RunComfy

Google's Nano Banana 2 is a flash-tier text-to-image model from the Gemini family, hosted on RunComfy's Model API. Optimized for speed, predictable framing, and in-image typography.

## When to Use

| Need | Route to |
|------|---------|
| Rapid ideation, social thumbnails, batch drafts | **Nano Banana 2** ← this skill |
| Portraits, fine facial detail | Nano Banana Pro |
| Heavy stylization, artistic control | Flux 2 |
| Maximum photorealism | Seedream 5 |
| Editing / inpainting existing images | GPT Image 2 |

Choose Nano Banana 2 when iteration speed and in-image text accuracy matter more than maximum detail or photorealism.

## Prerequisites

```bash
# 1. Install RunComfy CLI
npm install -g @runcomfy/cli

# 2. Authenticate (interactive — opens browser)
runcomfy auth login

# 3. For CI environments — store token in env
export RUNCOMFY_API_KEY="<your-token>"
# Token stored at ~/.runcomfy/token with 0600 permissions
```

## Quick Start

```bash
runcomfy run google/nano-banana-2/text-to-image \
  --prompt "A sun-drenched café terrace in Lisbon, morning light" \
  --aspect-ratio 16:9 \
  --num-images 1 \
  --output ./outputs/
```

## Input Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--prompt` | string | required | Text description of the image |
| `--num-images` | int | 1 | Number of images to generate (batch) |
| `--seed` | int | random | Fix for reproducible outputs |
| `--aspect-ratio` | string | `1:1` | See aspect ratios below |
| `--resolution` | string | `1K` | `0.5K`, `1K`, `2K`, `4K` |
| `--output-format` | string | `png` | `png`, `jpg`, `webp` |
| `--safety-tolerance` | int | 2 | `1`–`5` (5 = most permissive) |
| `--enable-web-search` | bool | false | Ground prompt in current events |

### Supported Aspect Ratios

`1:1` · `4:3` · `3:4` · `16:9` · `9:16` · `2:3` · `3:2` · `4:5` · `5:4` · `21:9` · `9:21`

## Prompting Guide

**Subject-first declarative grammar** works best. Lead with the primary subject, then layer action → environment → style:

```
✓  "A golden retriever puppy leaping through autumn leaves, soft bokeh, warm light"
✗  "There is a scene where in autumn a puppy that is golden colored is doing a leap"
```

**For in-image text** — quote exact characters explicitly:

```
✓  "Social media card; the headline reads 'LAUNCH DAY' in clean bold sans-serif white text on dark navy"
✗  "A card with some launch text on it"
```

**Style suffixes** that consistently work:
- `editorial photography, 35mm`
- `flat illustration, minimal palette`
- `isometric 3D render`
- `product photography, studio white`
- `infographic style, clean lines`

**Avoid conflicting descriptors** in the same prompt:

```
✗  "ultra-realistic photographic painting watercolor digital art 8K"
✓  "editorial photography, natural light, muted tones"
```

## Reproducible Outputs

Fix the seed to reproduce or iterate on a composition:

```bash
# First run — note the seed printed in stdout
runcomfy run google/nano-banana-2/text-to-image \
  --prompt "Abstract geometric poster in terracotta and cream" \
  --seed 42 \
  --aspect-ratio 2:3 \
  --output ./outputs/

# Iterate — same seed, adjusted prompt
runcomfy run google/nano-banana-2/text-to-image \
  --prompt "Abstract geometric poster in terracotta and cream, bolder shapes" \
  --seed 42 \
  --aspect-ratio 2:3 \
  --output ./outputs/
```

## Batch Generation

```bash
# Generate 4 variants in one call
runcomfy run google/nano-banana-2/text-to-image \
  --prompt "Thumbnail for a cooking video: pasta with fresh herbs, overhead shot" \
  --num-images 4 \
  --aspect-ratio 16:9 \
  --resolution 1K \
  --output ./thumbnails/
```

## Web Search Grounding

For prompts that reference current events or trending topics:

```bash
runcomfy run google/nano-banana-2/text-to-image \
  --prompt "Visual summary of today's top tech news" \
  --enable-web-search true \
  --output ./outputs/
```

## Exit Codes

| Code | Meaning | Action |
|------|---------|--------|
| 0 | Success | — |
| 1 | General error | Check stderr |
| 2 | Invalid arguments | Check parameter names/values |
| 64 | Usage error | Check CLI help |
| 77 | Authentication failure | Run `runcomfy auth login` |

## Security

- API tokens stored at `~/.runcomfy/token` with `0600` permissions
- Prompts transmitted as JSON over HTTPS — no shell expansion risk
- Never pass tokens via shell arguments: use env var `RUNCOMFY_API_KEY` for CI
- Download size capped at 2 GiB per call

## Common Issues

**Exit code 77 (auth failure)**: Token expired or missing. Run `runcomfy auth login` interactively, or set `RUNCOMFY_API_KEY` in the environment.

**Blurry or incoherent output**: Prompt likely has conflicting style descriptors. Strip to one clear aesthetic directive.

**In-image text garbled**: Quote the exact string explicitly in the prompt ("the label reads 'EXACT TEXT' in bold sans-serif"). Short phrases (1–4 words) are most reliable; avoid long sentences as image text.

**Resolution looks wrong**: `--resolution` sets the tier (`0.5K`–`4K`); actual pixel dimensions vary by aspect ratio within each tier.

**Batch taking too long**: Nano Banana 2 is flash-tier but `--num-images 8+` at `4K` will be slow. Use `1K` for draft batches; upscale selectively.

## Workflow Checklist

### Rapid Batch Ideation
- [ ] Write a subject-first prompt (subject → action → environment → style)
- [ ] Set `--num-images 4` and `--resolution 1K` for speed
- [ ] Use `--aspect-ratio` matching the target surface (16:9 for thumbnails, 9:16 for Reels)
- [ ] Note the seed from the first output you like
- [ ] Iterate with the fixed seed, adjusting prompt details

### Social Thumbnail Production
- [ ] Start with `16:9` at `1K` resolution
- [ ] If in-image text needed: quote exact characters in prompt
- [ ] Generate 4–8 variants (`--num-images 4`)
- [ ] Promote winner to `2K` with same seed for final delivery
- [ ] Verify text legibility at thumbnail size (120 × 68 px equivalent)

### Reproducible Asset Series
- [ ] Pick seed once and document it
- [ ] Keep style suffix identical across prompts in the series
- [ ] Only vary subject/action per image
- [ ] Use same `--aspect-ratio` and `--resolution` throughout

## Resolution vs. Speed Trade-off

| Resolution | Approx. size | Best for |
|-----------|-------------|---------|
| `0.5K` | ~512 px | UI mockups, rapid tests |
| `1K` | ~1024 px | Drafts, social thumbnails |
| `2K` | ~2048 px | Final deliverables, print-ready |
| `4K` | ~4096 px | High-res print, large format |

For batch runs of > 4 images, use `1K` and upscale selectors individually to avoid long wait times.

## Comparison with Related Skills

| Skill | Model | Speed | Best for |
|-------|-------|-------|---------|
| `nano-banana-2` | Google Nano Banana 2 | Fast | Drafts, thumbnails, typography |
| `flux-kontext` | Flux Kontext | Medium | Stylized, artistic |
| `gpt-image-2` | GPT Image 2 | Medium | Editing existing images |
| `seedance-v2` | Seedance v2 | Slow | Maximum realism |

## References

**Full parameter reference and advanced workflows**: See [references/README.md](references/README.md)
