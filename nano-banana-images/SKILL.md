---
name: nano-banana-images
description: A formalized skill for generating hyper-realistic, highly-controlled images using the Nano Banana 2 (Gemini 3.1 Flash) model through parameterized JSON prompting.
---

# Nano Banana 2 Image Generation Master

## Goal
Provide a standardized, highly controlled method for generating images using AI model Nano Banana 2 (or any underlying model connected to the `generate_image` tool). By strictly enforcing a structured JSON parameter schema, this skill neutralizes native model biases (like over-smoothing, dataset-averaging, or "plastic" AI styling) and ensures raw, unretouched, hyper-realistic outputs.

## Skill Folder Structure

```
nano-banana-images/
├── SKILL.md                                  # This file
├── gemini.md                                 # Gemini project organizer notes
├── master_prompt_reference                   # Full JSON schema reference guide
├── scripts/
│   ├── generate_kie.py                       # Kie.ai nano-banana-2 API
│   ├── generate_gemini.py                    # Google Gemini API (direct)
│   ├── generate_sdxl.py                      # Free cloud SDXL via Puter.ai
│   ├── generate_local.py                     # Free local SDXL via diffusers
│   ├── generate_hf.py                        # Hugging Face Spaces
│   └── export_prompt.py                      # Convert JSON prompt → plain text
├── prompts/
│   ├── image_restoration.json                # Restoration prompt (for API backends)
│   ├── image_restoration_plain_text.txt      # Restoration prompt (for Gemini web UI)
│   └── *.json                                # Image generation prompts
└── images/
    └── *.jpg / *.png                         # Reference images and generated outputs
```

## Reference Images

**IMPORTANT:** The `images/` folder contains reference images that users may refer to by filename. When a user mentions a reference image in their request (e.g., "use image_15.jpg as a reference", "based on photo.png", "reference the butterfly shot"), you **MUST**:

1. **Look in the `images/` folder first.** Check if the referenced file exists at `images/<filename>`.
2. **Read the reference image** using the Read tool to visually analyze its content — composition, lighting, colors, subject, style, mood, and technical qualities.
3. **Use the reference image to build a better prompt.** Incorporate what you observe into the structured JSON or plain-text prompt:
   - Match the lighting style, direction, and quality from the reference
   - Mirror the camera angle, focal length, and depth of field
   - Replicate the color palette and mood
   - Note the composition and framing approach
   - For identity preservation (faces/subjects), add the image path to the `image_input` array in the JSON prompt
4. **For API paths (Kie.ai, Gemini API):** Add the reference image path to `"image_input": ["images/<filename>"]` in the JSON prompt. The generation scripts handle uploading automatically.
5. **For Gemini web UI path:** Instruct the user to upload the reference image alongside the text prompt.

**Example flow:**
- User says: "Generate an image of a butterfly macro shot on a flower, and use image_15.jpg as a reference"
- Claude reads `images/image_15.jpg` to analyze the visual style
- Claude builds the prompt incorporating observed details from the reference
- Claude adds `"image_input": ["images/image_15.jpg"]` to the JSON prompt

## Prerequisites
- Access to the `generate_image` tool or one of the backend scripts.
- A clear understanding of the user's desired Subject, Lighting, and Camera characteristics.
- API keys in a `.env` file at the project root (for API backends).

## Core Schema Structure
When constructing a prompt for the `generate_image` tool, you **MUST** use the following JSON schema as the foundation. Fill in the string values with extreme, microscopic detail.

```json
{
  "task": "string - High-level goal (e.g., 'sports_selfie_collage', 'single_macro_portrait')",

  "output": {
    "type": "string - e.g., 'single_image', '4-panel_collage'",
    "layout": "string - e.g., '1x1', '2x2_grid', 'side-by-side'",
    "aspect_ratio": "string - e.g., '3:4', '16:9', '4:5'",
    "resolution": "string - e.g., 'ultra_high', 'medium_low'",
    "camera_style": "string - e.g., 'smartphone_front_camera', 'professional_dslr'"
  },

  "image_quality_simulation": {
    "sharpness": "string - e.g., 'tack_sharp', 'slightly_soft_edges'",
    "noise": "string - e.g., 'unfiltered_sensor_grain', 'visible_film_grain', 'clean_digital'",
    "compression_artifacts": "boolean - true if attempting to simulate uploaded UGC",
    "dynamic_range": "string - e.g., 'limited', 'hdr_capable'",
    "white_balance": "string - e.g., 'slightly_warm', 'cool_fluorescent'",
    "lens_imperfections": [
      "array of strings - e.g., 'subtle chromatic aberration', 'minor lens distortion', 'vignetting'"
    ]
  },

  "subject": {
    "type": "string - e.g., 'human_portrait', 'nature_macro', 'infographic_flatlay'",
    "human_details": {
      "//": "Use this block ONLY for human subjects",
      "identity": "string",
      "appearance": "string - Extremely specific (e.g., visible pores, mild redness)",
      "outfit": "string"
    },
    "object_or_nature_details": {
      "//": "Use this block for non-human subjects",
      "material_or_texture": "string - e.g., 'brushed aluminum', 'dew-covered velvety petals'",
      "wear_and_tear": "string - e.g., 'subtle scratches on the anodized finish', 'browning edges on leaves'",
      "typography": "string - e.g., 'clean sans-serif overlaid text, perfectly legible'"
    }
  },

  "multi_panel_layout": {
    "grid_panels": [
      {
        "panel": "string - e.g., 'top_left', 'full_frame' (if not a grid)",
        "pose": "string - e.g., 'slight upward selfie angle, relaxed smile'",
        "action": "string - e.g., 'holding phone with one hand, casual posture'"
      }
    ]
  },

  "environment": {
    "location": "string - e.g., 'gym or outdoor sports area'",
    "background": "string - What is behind the subject (e.g., 'blurred gym equipment')",
    "lighting": {
      "type": "string - e.g., 'natural or overhead gym lighting', 'harsh direct sunlight'",
      "quality": "string - e.g., 'uneven, realistic, non-studio', 'high-contrast dramatic'"
    }
  },

  "embedded_text_and_overlays": {
    "text": "string (optional)",
    "location": "string (optional)"
  },

  "structural_preservation": {
    "preservation_rules": [
      "array of strings - e.g., 'Exact physical proportions must be preserved'"
    ]
  },

  "controlnet": {
    "pose_control": {
      "model_type": "string - e.g., 'DWPose'",
      "purpose": "string",
      "constraints": ["array of strings"],
      "recommended_weight": "number"
    },
    "depth_control": {
      "model_type": "string - e.g., 'ZoeDepth'",
      "purpose": "string",
      "constraints": ["array of strings"],
      "recommended_weight": "number"
    }
  },

  "explicit_restrictions": {
    "no_professional_retouching": "boolean - typically true for realism",
    "no_studio_lighting": "boolean - typically true for candid shots",
    "no_ai_beauty_filters": "boolean - mandatory true to avoid plastic look",
    "no_high_end_camera_look": "boolean - true if simulating smartphones"
  },

  "negative_prompt": {
    "forbidden_elements": [
      "array of strings - Massive list of 'AI style' blockers required for extreme realism. Example stack: 'anatomy normalization', 'body proportion averaging', 'dataset-average anatomy', 'wide-angle distortion not in reference', 'lens compression not in reference', 'cropping that removes volume', 'depth flattening', 'mirror selfies', 'reflections', 'beautification filters', 'skin smoothing', 'plastic skin', 'airbrushed texture', 'stylized realism', 'editorial fashion proportions', 'more realistic reinterpretation'"
    ]
  }
}
```

## Paradigm 2: The Dense Narrative Format (Optimized for APIs like Kie.ai)
When executing API calls to standard generation endpoints (which often only accept string prompts), condense the logic above into a dense, flat JSON string containing a massive descriptive text block.

```json
{
  "prompt": "string - A dense, ultra-descriptive narrative. Use specific camera math (85mm lens, f/1.8, ISO 200), explicit flaws (visible pores, mild redness, subtle freckles, light acne marks), lighting behavior (direct on-camera flash creating sharp highlights), and direct negative commands (Do not beautify or alter facial features).",
  "negative_prompt": "string - A comma-separated list of explicit realism blockers (no plastic skin, no CGI).",
  "image_input": [
    "array of strings (file paths or URLs) - Optional. Input images to transform or use as reference (up to 14). Local file paths (e.g., 'images/reference.jpg') are uploaded automatically by the scripts."
  ],
  "api_parameters": {
    "google_search": "boolean - Optional. Use Google Web Search grounding",
    "resolution": "string - Optional. '1K', '2K', or '4K' (default 1K)",
    "output_format": "string - Optional. 'jpg' or 'png' (default jpg)",
    "aspect_ratio": "string - Optional. Overrides CLI aspect_ratio (e.g., '16:9', '4:5', 'auto')"
  },
  "settings": {
    "resolution": "string",
    "style": "string - e.g., 'documentary realism'",
    "lighting": "string - e.g., 'direct on-camera flash'",
    "camera_angle": "string",
    "depth_of_field": "string - e.g., 'shallow depth of field'",
    "quality": "string - e.g., 'high detail, unretouched skin'"
  }
}
```

## Best Practices & Natural Language Hacks

1.  **Camera Mathematics:** Always define exact focal length, aperture, and ISO (e.g., `85mm lens, f/2.0, ISO 200`). This forces the model to mimic optical physics rather than digital rendering.
2.  **Explicit Imperfections:** Words like "realistic" are not enough. Dictate flaws: `mild redness`, `subtle freckles`, `light acne marks`, `unguided grooming`.
3.  **Direct Commands:** Use imperative negative commands *inside* the positive prompt paragraph: `Do not beautify or alter facial features. No makeup styling.`
4.  **Lighting Behavior:** Don't just name the light, name what it does: `direct flash photography, creating sharp highlights on skin and a slightly shadowed background.`
5.  **Non-Human Materials (Products/Nature):** When generating non-humans, replace skin/outfit logic with extreme material physics. Define surface scoring (e.g., "micro-scratches on anodized aluminum"), light scattering (e.g., "subsurface scattering through dew-covered petals"), or graphic layouts (e.g., "flat-lay composition, clean sans-serif typography").
6.  **Mandatory Negative Stack:** You MUST include the extensive negative prompt block (e.g., forbidding "skin smoothing" and "anatomy normalization").
7.  **Avoid Over-Degradation (The Noise Trap):** While simulating camera flaws (like `compression artifacts`) can help realism, pushing extreme `ISO 3200` or `heavy film grain` in complex, contrast-heavy environments (like neon night streets) actually triggers the model's "digital art/illustration" biases. Keep ISO settings below 800 and rely on *physical subject imperfections* (like peach fuzz or asymmetrical pores) rather than heavy camera noise to sell the realism.

## Master Reference Guide
For the absolute full schema breakdown, parameter options, or the complex JSON structuring for multi-panel grids, refer to: `master_prompt_reference` in this skill directory.

## Execution: Backend Selection

**BEFORE** constructing the prompt, you **MUST** ask the user which output path they want. Present these options:

1. **Kie.ai API** — Highest quality, requires `KIE_API_KEY` in `.env` -> creates structured JSON
2. **Google Gemini API** — Direct Gemini call, requires `GEMINI_API_KEY` in `.env` -> creates structured JSON
3. **Free cloud SDXL** — No API key needed, good quality (Puter.ai) -> creates structured JSON
4. **Local SDXL** — No API key, runs on local GPU/CPU (slow, downloads ~5GB model) -> creates structured JSON
5. **Gemini web UI** — Free, no API key, manual copy/paste -> creates structured plain text prompt

For options 1-4, construct the JSON prompt, save it to `prompts/`, and run the corresponding script. For option 5, create a structured plain-text prompt with the same photography parameters and negative constraints for the user to paste into gemini.google.com.

### Backend Commands

All scripts live in the `scripts/` directory within this skill. Run from the project root (`/home/gil/projects/nano-banana-images/`) where the `.env` and `.venv` are located.

**Kie.ai:**
```bash
source .venv/bin/activate
python scripts/generate_kie.py prompts/<file>.json images/output.jpg "4:5"
```

**Google Gemini API:**
```bash
source .venv/bin/activate
python scripts/generate_gemini.py prompts/<file>.json images/output.jpg
```

**Free cloud SDXL:**
```bash
source .venv/bin/activate
python scripts/generate_sdxl.py "<prompt_text>" images/output.jpg
```

**Local SDXL:**
```bash
source .venv/bin/activate
python scripts/generate_local.py "<prompt_text>" images/output.jpg
```

**Export for Gemini web UI:**
```bash
source .venv/bin/activate
python scripts/export_prompt.py prompts/<file>.json
```
This outputs the prompt as plain text. Copy and paste it into Gemini's "Create Images" tool at gemini.google.com.

## How to use this skill

When a user asks you to generate a highly detailed, realistic, or complex image:

1. **Check for reference images.** If the user mentions a reference image by filename, look in `images/` first. Read the image to visually analyze it and incorporate its visual qualities into the prompt.
2. **Ask the user which output path they want** (present the 5 options above).
3. **Construct the prompt** in the appropriate format:
   - **API paths (options 1-4):** Build the structured JSON prompt, include any reference images in the `image_input` array, and save it to `prompts/`
   - **Gemini web UI (option 5):** Build a structured plain-text prompt with the same photography parameters and negative constraints. If there are reference images, tell the user to upload them alongside the text in Gemini.
4. **Execute the selected backend command** (API paths) or output the plain text for the user to copy/paste (Gemini web UI).
5. **Save generated images** to the `images/` folder.

## Image Restoration

This skill also supports restoring scanned vintage film photographs. Pre-built restoration prompts are included:

| File | Format | Use With |
|------|--------|----------|
| `prompts/image_restoration.json` | Structured JSON | API backends (`generate_kie.py`, `generate_gemini.py`) |
| `prompts/image_restoration_plain_text.txt` | Plain text | Copy/paste into Gemini web UI |

To restore an image via API, add the source image path to the `image_input` array in `image_restoration.json` and run the appropriate generation script. For Gemini web UI, upload the photo and paste the contents of `image_restoration_plain_text.txt`.

## Prompt JSON Schema Reference

| Field | Required | Description |
|-------|----------|-------------|
| `prompt` | Yes | Dense, detailed text description with camera math (lens, aperture, ISO), lighting behavior, skin/texture directives, and negative commands |
| `negative_prompt` | Yes | Comma-separated list of things to avoid (AI artifacts, beauty filters, CGI, etc.) |
| `image_input` | No | Array of local file paths to reference images. Used for identity preservation (generation) or as the source photo (restoration) |
| `api_parameters.resolution` | No | `"1K"`, `"2K"`, or `"4K"` (default: `"1K"`) |
| `api_parameters.output_format` | No | `"jpg"` or `"png"` (default: `"jpg"`) |
| `api_parameters.aspect_ratio` | No | e.g. `"3:4"`, `"16:9"`, `"4:5"`, `"auto"` |
| `settings` | No | Metadata for style, lighting, camera angle, depth of field, quality |
