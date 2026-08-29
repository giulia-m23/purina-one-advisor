# Purina ONE Advisor

A web app that turns a short profile of a dog or cat into a Purina ONE recommendation you can actually defend: three ranked products, each with the reason it was chosen, a feeding plan computed from published energy equations, and a shareable result card.

> 🏆 **1st place — Nestlé × Bocconi competition**, ranked first across all sections of the course.

**Live demo:** `https://purina-one-advisor.vercel.app/`

---

## Overview

A four-step questionnaire collects the pet's life stage, activity level, health needs and weight, plus pet type and, for cats, whether it is sterilized. The app scores the catalog against that profile and returns the three closest products, each with a match percentage and a plain-language explanation of *why* it came out on top. It then builds a personalized daily feeding plan, suggests complementary products, and lets the user assemble a cart and print or share the result.

The point was to make the recommendation defensible, not just pleasant. Every suggestion traces back to something the user actually said about their pet, and every nutritional figure comes from a published source instead of being invented.

## Screenshots

`[add 2–3 screenshots: the questionnaire, the results page with the animated match ring, and the feeding plan / shareable card]`

## How it works

**1. Profile capture.** A four-step flow collects life stage (`Junior` / `Adult` / `Senior`), activity (`Sedentario` → `Molto Attivo`), health concerns (digestion, skin & coat, weight control, picky appetite, intolerances), and weight, plus pet type and a sterilization flag for cats.

**2. Weighted scoring engine** (`src/lib/recommendation.ts`). Each product carries match criteria and accumulates a score against the profile:

| Dimension | Points |
|---|---|
| Life-stage match | +10 |
| Activity match | +5 |
| Health-need match | +15 |
| Priority boost for critical health → product pairings (e.g. intolerance → monoprotein, weight control → light formula, skin & coat → delicate) | +20 |
| Sterilization match → Sterilcat (cats only) | +25 |

Products are ranked by score; the top three are returned. The raw score-to-max ratio is mapped to a rank-calibrated display percentage (top match 88–98%, second 75–90%, third 65–82%) so the UI reads cleanly without overstating fit. Each matched dimension produces a bilingual "why we recommend this" reason.

**3. Science-based feeding plan** (`src/lib/feedingPlan.ts`). For the recommended dry food (paired with a complementary wet food where defined), the app computes daily intake from established energy equations rather than guesswork:

- Resting Energy Requirement: `RER = 70 × bodyweight^0.75` (kcal/day)
- Maintenance Energy Requirement: `MER = RER × activity factor`
- Dry-food grams: `(MER − wet-food kcal) ÷ caloric density`

Figures are grounded in NRC 2006 (*Nutrient Requirements of Dogs and Cats*), AAFCO, FEDIAF, and VOHC — no fabricated percentages.

**4. Cross-sell.** Rule-based add-ons (dental chews for larger dogs, treats, cat snacks) shown alongside the main recommendation.

## Tech stack

- **Framework:** Next.js 16 (App Router), React 19
- **Language:** TypeScript
- **Styling:** Tailwind CSS 4
- **State:** Zustand
- **Animation:** Framer Motion
- **Sharing:** html-to-image (Spotify-Wrapped-style result cards)
- **Other:** PWA manifest, EN/IT internationalization, dark/light theme, mobile-first responsive design
- **Deployment:** Vercel

## Project structure

```
src/
├── app/                    # App Router routes
│   ├── page.tsx                # Landing / hero
│   ├── questionnaire/          # 4-step profile builder
│   ├── results/                # Ranked recommendations + match scores
│   ├── cart/                   # Cart
│   └── confirmation/           # Order summary + printable feeding plan
├── components/             # UI by domain (hero, questionnaire, results, cart, layout)
├── lib/
│   ├── recommendation.ts       # Weighted scoring engine
│   ├── products.ts             # Product catalog + match criteria
│   ├── feedingPlan.ts          # NRC 2006 energy-requirement feeding plan
│   └── scienceStats.ts         # Sourced nutrition stats for the share card
├── stores/                 # Zustand store (useAdvisorStore.ts)
├── i18n/                   # EN / IT translations
└── types/                  # Shared TypeScript types
```

## Getting started

Requires Node.js 18+.

```bash
npm install
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

Other scripts: `npm run build` (production build), `npm run start` (serve the build), `npm run lint`.

## Team

Giulia Moroni, co-founder — https://www.linkedin.com/in/giuliamoroni/

*Built for the Nestlé × Bocconi competition. Not affiliated with or endorsed by Nestlé/Purina; product names and nutritional claims are used for the academic project only.*
