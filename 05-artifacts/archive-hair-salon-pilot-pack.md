# Archive Salon — Brand AI Media Pilot Pack

> Prepared 2026-06-20 for the JTS Brand AI Media Studio pilot.
> Goal: train one style LoRA on LTX-2.3 and generate 10 short brand-consistent clips.
> Cloud runtime: fal.ai (top-up required — account currently locked).

---

## 1. CLIENT SUMMARY (VERIFIED FROM PUBLIC SOURCES)

| Field | Value |
|-------|-------|
| **Business name** | Archive Salon (also Archive Hair Salon) |
| **Instagram** | [@archivehairsalon](https://www.instagram.com/archivehairsalon/) |
| **Website** | [archivehairsalon.com](https://archivehairsalon.com) |
| **Location** | 1535 Meridian Ave Ste 40, San Jose, CA 95125 |
| **Phone** | (408) 644-8956 |
| **Email** | archivehairsalon@gmail.com |
| **Booking** | Link in bio / website booking |
| **Tagline** | "A collective dedicated to your hair story" |
| **Positioning** | "A curated space for hair artistry in San Jose's Design District" |
| **Brand promise** | Luxurious yet personal and approachable; hand-crafted wood accents; "one hair chapter at a time" |
| **Instagram status** | 2 posts, 279 followers — basically dormant, high-value pilot opportunity |
| **Avatar** | Dark red / maroon "A" on light beige / blush circular background |

**CRITICAL CORRECTION:** Earlier notes described "dusty rose / chocolate brown / editorial luxury." That was **not verified** from Archive's actual brand. Their real palette and interior are **unknown until we collect assets directly from the client or visit.**

**Mood from website copy:** warm, personal, curated, hair-story-focused — not generic luxury, not stock beauty.

---

## 2. ASSET COLLECTION CHECKLIST (PRIORITIZED)

| Priority | Asset | Format | Why needed |
|----------|-------|--------|------------|
| **P0** | 20–40 salon interior + detail photos | JPG/PNG | Core LoRA training data |
| **P0** | Logo/wordmark (transparent PNG or SVG) | PNG/SVG | Overlay + brand-safe frame composition |
| **P0** | Brand color hex codes / style guide | text/hex | Lock LoRA palette |
| **P1** | Existing brand video clips (if any) | MP4 | Motion timing reference |
| **P1** | Styling/texture close-up stills (no faces unless released) | JPG/PNG | Texture LoRA + clip variety |
| **P1** | Preferred music/audio beds | MP3/WAV | Audio-to-video or post-sync |
| **P2** | Instagram booking link | URL | CTA on deliverables |

**Privacy rule:** No client faces in training data unless the client signs a likeness release.

---

## 3. BRAND RESEARCH NOTES

### From search results (not first-hand visuals)
- Website copy emphasizes **wood accents**, **curated space**, **hair artistry**, **Design District** location.
- Reviews mention 5.0 rating, full-service salon, stylists at Level 1–4.
- Instagram is nearly empty — this pilot can establish their content baseline.

### What we still need directly from client
- Actual interior photos (lighting, wall colors, furniture, texture).
- Logo files and brand hex codes.
- Any existing brand direction or mood board.
- Client's preference on showing people, hands-only, or pure atmosphere.

---

## 4. FAL.AI PILOT WORKFLOW

### Step A — Top up fal.ai
- Visit `https://fal.ai/dashboard/billing`
- Add payment method and credits
- Verify `FAL_KEY` in `~/.hermes/.env`

### Step B — Prepare training zip
```
archive-salon-lora/
├── images/
│   ├── interior_01.jpg
│   ├── interior_02.jpg
│   ├── detail_wood_accent_01.jpg
│   ├── styling_texture_01.jpg
│   └── ... (20–40 curated images)
└── metadata.jsonl
```

Metadata format per image:
```json
{"file_name": "images/interior_01.jpg", "prompt": "Archive Salon interior, curated hair artistry space, hand-crafted wood accents, warm personal lighting, San Jose Design District salon"}
```

### Step C — Train LoRA
- Endpoint: `fal-ai/ltx23-trainer-v2/t2v`
- Target: ~1,000–1,500 steps
- Expected cost: ~$6–$9
- Output: `.safetensors` LoRA weights owned by client

### Step D — Generate clips
Use `fal-ai/ltx-2.3-22b/image-to-video/lora` with:
- `image_url`: hero still generated from LoRA or supplied by client
- `lora`: Archive salon LoRA
- `prompt`: slow cinematic camera movement, salon interior, texture-focused
- `duration`: 5 seconds
- `resolution`: 720p or 1080p depending on IG placement

Expected cost: ~$0.30–$0.80 per 5s clip

### Step E — Deliver
- 10 MP4 clips (1080×1920 Reels, 1080×1080 square)
- 1 LoRA weights file
- Prompt recipes used
- Optional: IG caption + hashtag set

---

## 5. SAMPLE PROMPTS (DRAFT — REVISE AFTER COLOR PALETTE LOCK)

**Style-prompt base (placeholder until real palette confirmed):**
> Archive Salon interior, curated hair artistry space, hand-crafted wood accents, warm personal lighting, soft shadows, calm approachable atmosphere, shallow depth of field, slow dolly shot, cinematic

**Variation A — Texture focus:**
> macro shot of hands working through hair, warm salon light, slow motion, soft natural tones, editorial beauty cinematography, Archive Salon aesthetic

**Variation B — Atmosphere:**
> wide slow pan across a quiet curated salon interior, hand-crafted wood details, warm personal light, calm before-hours atmosphere

**Variation C — Branding overlay-safe:**
> cinematic salon scene with clean lower-third safe area for logo, shallow depth of field, warm curated atmosphere, no faces

---

## 6. PROJECTED PILOT COST

| Item | Cost |
|------|------|
| fal.ai LoRA training (1,000 steps) | ~$6 |
| 10 video clips at 720p/5s | ~$3–$5 |
| Iterations + testing buffer | ~$5 |
| **Total pilot cost** | **~$15–$20** |

Client pilot price: **$1,500** (Motion Starter Pack) includes LoRA build + 10 clips.
Net margin before Joe labor: ~$1,480.

---

## 7. NEXT ACTIONS

- [ ] Ask client for logo, brand hex codes, and 20–40 interior/detail photos
- [ ] Top up fal.ai balance
- [ ] Run first LoRA training test
- [ ] Generate 3 test clips before full 10-clip batch
- [ ] Deliver + get client feedback
- [ ] Decide whether to offer monthly retainer

---

## 8. FILES CREATED

- `~/.hermes/skills/creative/ltx-brand-video-studio/SKILL.md` — reusable workflow
- `~/.hermes/skills/jts-business/media-studio/SKILL.md` — pricing/service tier
- `~/Documents/JTS-Redesign/content/jts-homepage-v1.md` — updated positioning
- `~/ai-stack/05-artifacts/archive-hair-salon-pilot-pack.md` — this file

---

*Pilot ready. Waiting on client assets and fal.ai top-up. Do not generate with guessed colors — lock real palette first.*
