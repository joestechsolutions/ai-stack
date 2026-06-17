# JTS Brand Spec — for IG + all visual extensions

> Captured 2026-06-16 from joestechsolutions.com.
> Source of truth: this file. Re-verify quarterly.

## Color Palette

| Role | Hex | Usage |
|---|---|---|
| Background primary | `#0A0A0B` | Solid base for all visuals |
| Background secondary | `#0F0F12` | Subtle gradients, card surfaces |
| Card surface | `#16161A` | Elevated tiles |
| Accent (signature) | `#2DD4BF` | ONE element per frame — headline word, CTA, badge, underline |
| Primary text | `#FFFFFF` | Headlines, logo wordmark |
| Secondary text | `#9CA3AF` | Body copy |
| Muted text | `#6B7280` | Footer, fine print |
| Border | `#1F1F23` | Dividers |

**Rule of thumb:** Black canvas + ONE teal element per frame. Teal carries 90% of brand energy. Never use multiple accent colors in the same frame.

## Typography

- **Headline:** Heavy/ExtraBold geometric sans (Inter Display, Geist, Satoshi). Tight tracking. Large x-height.
- **Two-tone pattern:** White statement + teal punchline. E.g. "I run" (white) + "16 AI agents." (teal). This is the brand's signature.
- **Body:** Same family, light/regular weight, generous line height.
- **UI labels:** Uppercase, tracked-out, small caps.

## Layout

- Minimalist, hero-first
- Centered single-column hero
- Card grid for product/feature showcase
- Rounded pill buttons; slightly squared cards
- Generous vertical breathing room

## Mood

Premium tech / "Stealth Mode SaaS":
- Dark, calm, confident
- AI/automation expertise
- Boutique agency feel
- Modern, developer-adjacent

## Imagery Rules

**Photos:** 0% — never use human imagery, never product photography
**Illustrations:** ≤5% — thin-line monoline icons only (1.5–2px stroke)
**Type & UI:** 95% — the brand IS typography + color + space

**Never:**
- Smiling-headshot people
- Stock photos
- Photoreal AI generations of products
- Multiple accent colors per frame

**Sometimes (with care):**
- Abstract gradient meshes
- Code snippets (syntax-highlighted, monospace)
- Terminal scroll captures
- Dashboard screenshots
- Isometric tech shapes (monoline)
- Real product screenshots (when the product exists)

## IG-Specific Tokens

- **Post size:** 1080×1080 (feed), 1080×1920 (Stories/Reels)
- **Carousel:** 5–7 slides, each like a "tile" from the website grid
- **Bio:** 3 lines, teal accent on the third line
- **Avatar wordmark:** Teal "TTS" or "JTS" on black, square
- **Link-in-bio:** styled like the "Get Private AI" pill button (teal pill, black text)

## What the Brand Is Selling

> Private AI for SMBs. Built and battle-tested in-house.
> Joe runs a 16-agent AI team for his own business — now he builds the same for yours.
> Custom systems. No vendor lock-in. 100% on your hardware or private server.

**The 22 → 16 migration story is the differentiator.** Site still says 22 (legacy OpenClaw); fix is in PR #1 (CloudyJoe.com migration), not yet deployed.

## Anti-Slop Prompt Pattern (for any AI gen that ever slips in)

```
[Concept], [brand palette: dark navy #0A0A0B + teal #2DD4BF], [camera format: Hasselblad 500C / Mamiya 7 / Contax T2], [film stock: Portra 400 / Kodachrome 64 / Ektar 100], [lighting: Rembrandt / 4:1 fill / kicker at 45°], [specific defects: minor dust on sensor, slight film grain, no halation], negative: gibberish text, decorative patterns, painted-on textures, generic gradient backgrounds, stock-photo lighting, smiling people, vector-art logos
```

## Sources

- Vision analysis of joestechsolutions.com hero (browser_vision, 2026-06-16)
- CloudyJoe.com migration plan (joestechsolutions/cv-joseph PR #1)
- JTS positioning one-pager (~/ai-stack/05-artifacts/jts-positioning-onepager.md)

---

*This spec is for any agent, human, or tool rendering JTS visuals. Match the palette + typography + mood; ignore everything else.*
