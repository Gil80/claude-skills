---
name: nano-banana-image-restoration
description: Restore and enhance scanned vintage film photographs using Nano Banana 2 (Gemini) AI — upscale to 4K, sharpen, color-correct, and remove artifacts without altering content. Supports API (.json prompt) and Gemini web UI (.txt prompt) workflows.
---

# Nano Banana Image Restoration

## Goal

Provide a structured, repeatable workflow for restoring scanned vintage film photographs using AI. The restoration process upscales, sharpens, color-corrects, and removes artifacts (grain, dust, scratches) — while strictly preserving all original content. No hallucinated details, no composition changes, no content alteration.

This skill covers two prompt formats and multiple execution backends.

## When to Use This Skill

- User wants to restore, enhance, or upscale an old/vintage/scanned photograph
- User wants to remove grain, dust, scratches, or color casts from a photo
- User mentions "image restoration", "photo restoration", "enhance old photo", or "fix scanned photo"
- User wants to upscale a photo to 4K while preserving detail

## Restoration Pipeline

The restoration prompt instructs the model to perform these steps in order:

1. **Analyze** — Evaluate resolution, sharpness, noise level, color balance, film grain density, dust/scratch marks, compression artifacts, and dynamic range
2. **Upscale to 4K** — 3840px on the long edge, maintain aspect ratio, reconstruct fine detail from existing pixel data only (fabric weave, hair strands, surfaces)
3. **Sharpen** — Unsharp mask + detail recovery, restore edge crispness, enhance micro-contrast, remove softness from original optics or scanning
4. **Color Correct** — Neutralize aged film dye color casts, restore white balance, recover true colors, improve dynamic range (open shadows, recover highlights)
5. **Artifact Removal** — Remove film grain, dust specks, scratches, and scanning artifacts while preserving genuine detail

## Hard Constraints

These constraints are embedded in the prompt and must NEVER be relaxed:

- Do NOT alter, modify, or regenerate any content
- Do NOT add any detail that does not exist in the source pixels
- Do NOT change framing, crop, or composition
- Only apply: upscaling, sharpening, color correction, and artifact removal
- Output format: PNG at 4K resolution

## Prompt Files

The project includes two pre-built restoration prompts at `/home/gil/projects/nano-banana-images/prompts/`:

### 1. `image_restoration.json` — For API backends

Use with `generate_kie.py` or `generate_gemini.py`. This is the structured JSON format that the generation scripts consume directly.

**Before running**, you must add the source image to the `image_input` array:

```json
{
  "image_input": ["images/my_old_photo.jpg"]
}
```

The JSON prompt includes:
- `prompt` — The full restoration instruction text
- `negative_prompt` — Blocklist preventing content alteration, hallucination, CGI, etc.
- `api_parameters` — Resolution: 4K, format: PNG, aspect_ratio: auto
- `settings` — Style: professional photo restoration, archival quality

### 2. `image_restoration_plain_text.txt` — For Gemini web UI

Use by copying the text and pasting it into [Gemini](https://gemini.google.com) alongside the uploaded photo. This is the same restoration instruction as the JSON but formatted as plain text for manual use.

## Execution

### Option 1: Kie.ai API (highest quality, requires `KIE_API_KEY`)

1. Edit `prompts/image_restoration.json` — add source image path to `image_input`
2. Run:
```bash
cd /home/gil/projects/nano-banana-images
source .venv/bin/activate
python scripts/generate_kie.py prompts/image_restoration.json images/restored_output.png
```

### Option 2: Google Gemini API (requires `GEMINI_API_KEY`)

1. Edit `prompts/image_restoration.json` — add source image path to `image_input`
2. Run:
```bash
cd /home/gil/projects/nano-banana-images
source .venv/bin/activate
python scripts/generate_gemini.py prompts/image_restoration.json images/restored_output.png
```

### Option 3: Gemini Web UI (free, manual)

1. Open [gemini.google.com](https://gemini.google.com)
2. Upload the photo you want to restore
3. Copy the contents of `prompts/image_restoration_plain_text.txt` and paste it into the chat
4. Download the restored image from Gemini's response

### Option 4: Other API Wrappers

Any API wrapper that accepts a text prompt + image input can use the restoration prompt. Extract the `prompt` field from `image_restoration.json` and pass it alongside the source image. The key requirement is that the backend model supports image-to-image transformation (not just text-to-image generation).

## Workflow: Step by Step

When a user asks to restore an image:

1. **Identify the source image** — Ask the user for the path to the scanned photo (or confirm it's already in `images/`)
2. **Choose prompt format**:
   - API backend? Use `image_restoration.json` — update `image_input` with the source path
   - Gemini web UI? Use `image_restoration_plain_text.txt` — output the text for the user to copy
3. **Choose backend** — Present these options:
   - **Kie.ai API** — Highest quality, requires `KIE_API_KEY`
   - **Google Gemini API** — Direct API call, requires `GEMINI_API_KEY`
   - **Gemini web UI** — Free, manual copy/paste workflow
   - **Other API wrapper** — Extract prompt text and use with any compatible service
4. **Execute** — Run the appropriate command or output the text prompt
5. **Output** — Restored PNG at 4K resolution saved to `images/`

## Customizing the Restoration Prompt

The default prompt targets scanned vintage film photographs. To adapt for other restoration scenarios:

- **Digital photos** — Remove references to "film grain" and "aged film dyes"; focus on compression artifacts and noise reduction
- **Damaged photos** — Emphasize scratch and tear removal in the prompt
- **Partial restoration** — If the user only wants color correction (no upscaling), modify the prompt to remove the upscale step
- **Different output resolution** — Change `3840px` in the prompt and `"resolution": "4K"` in `api_parameters`

When customizing, always preserve the hard constraints section — it prevents the model from hallucinating or altering content.

## Creating New Restoration Prompts

Follow the project's versioning convention for iterations:

```
image_restoration.json      # base version
image_restoration_v1.json   # first iteration
image_restoration_v1b.json  # refinement of v1
image_restoration_v2.json   # major revision
```

To create a plain-text version from a JSON prompt:
```bash
cd /home/gil/projects/nano-banana-images
source .venv/bin/activate
python scripts/export_prompt.py prompts/image_restoration.json
```

This outputs the prompt as plain text for Gemini web UI usage.
