# Nano Banana 2 — Extended Reference

## Full CLI Reference

```
runcomfy run google/nano-banana-2/text-to-image [options]

Options:
  --prompt              <string>   Image description (required)
  --num-images          <int>      Number of images, default 1
  --seed                <int>      Reproducibility seed, default random
  --aspect-ratio        <string>   One of: 1:1, 4:3, 3:4, 16:9, 9:16,
                                   2:3, 3:2, 4:5, 5:4, 21:9, 9:21
  --resolution          <string>   0.5K | 1K | 2K | 4K, default 1K
  --output-format       <string>   png | jpg | webp, default png
  --output              <path>     Directory for output files
  --safety-tolerance    <int>      1–5 (5 = most permissive), default 2
  --enable-web-search   <bool>     Ground in web search, default false
  --help                           Show this help
```

## Authentication Setup

### Interactive (local dev)
```bash
runcomfy auth login
# Opens browser → authenticate → token saved to ~/.runcomfy/token
```

### Non-interactive (CI/CD)
```bash
# Set env var — takes precedence over stored token
export RUNCOMFY_API_KEY="rc_live_xxxxxxxxxxxx"

# Verify auth status
runcomfy auth status
```

### Token Security
- Stored at `~/.runcomfy/token`, permissions `0600`
- Never pass token via `--api-key` CLI flag (visible in process list)
- Rotate tokens via RunComfy dashboard if exposed

## Advanced Prompt Patterns

### Product Photography
```
"[Product name] on a [surface], [lighting], [angle], product photography, studio white background, no shadows"
```

### Infographic / Data Visualization
```
"Minimalist infographic showing [topic], flat design, sans-serif labels, [brand color palette], clean lines, white background"
```

### Editorial Illustration
```
"Editorial illustration of [subject/concept], [publication style: NYT / Wired / The Atlantic], [color palette], bold graphic shapes"
```

### Consistent Character Series
Fix seed + same character description across prompts:
```bash
BASE="A tall chef with a red bandana and vintage apron"
for scene in "plating a dish" "shopping at a market" "reading a recipe book"; do
  runcomfy run google/nano-banana-2/text-to-image \
    --prompt "$BASE, $scene, editorial illustration style" \
    --seed 1337 \
    --aspect-ratio 4:5 \
    --output "./series/"
done
```

## Output File Naming

Output files are named `{seed}_{index}.{format}` by default, e.g. `42_0.png`, `42_1.png`. To rename on download:
```bash
runcomfy run google/nano-banana-2/text-to-image \
  --prompt "..." \
  --seed 42 \
  --num-images 3 \
  --output ./raw/ && \
  for f in ./raw/42_*.png; do
    cp "$f" "./final/hero_$(basename $f)"
  done
```

## Troubleshooting Reference

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| Exit 77 | Token expired/missing | `runcomfy auth login` |
| Exit 2 | Bad parameter name or value | Check `--aspect-ratio` spelling; `resolution` values are `0.5K`/`1K`/`2K`/`4K` |
| Garbled text in image | In-image text not quoted | Wrap exact string: `the sign reads 'OPEN'` |
| Output looks identical across seeds | Prompt too constrained | Add stylistic variation words |
| Download timeout | Large batch at 4K | Reduce `--num-images` or lower resolution |
