# RealDoor development

See the [project README](../README.md) for the product and demo, and
[project guidance](../CLAUDE.md) for domain rules and contribution conventions.
Code paths below are relative to the repository root.

## Repository layout

```
frontend/   Next.js 16 app (App Router, strict TS) — pipeline UI, PDF viewer, AI chat,
            receipt, i18n; api/ carries the Python engine when deployed
engine/     Python extraction + rules engine (classify, extract, score) + fixtures/tests;
            deployed as a Vercel Python function at /api/engine/*
supabase/   understand-chat edge function + _shared policy/contract modules, tests
            (node --test), migrations (i18n, ai_request_events)
docs/       Architecture, frontend design, AI specification, and development history
README.md   Product overview, local development, testing, and deployment
CLAUDE.md   Project guidance, domain rules, and development workflow
```

## Hosting & services

- **Vercel** project `realdoor-boston` (team `chefcurrys-projects`) serves the frontend and the
  Python engine function. **Pushes do not auto-deploy** — deploy manually (below).
- **Supabase** project `snAI` (`zgfanoruqwftbqhhvtwg`, eu-central-1): anonymous auth, i18n tables,
  the `understand-chat` function, and the AI audit table. The URL + publishable key in
  `frontend/lib/supabase.ts` are public by design (anon-equivalent); real secrets
  (`OPENAI_API_KEY`, `OPENAI_MODEL`) live **only** in Supabase Edge Function secrets.

> Why the app isn't hosted on Supabase: the shared `*.supabase.co` domain rewrites `text/html` to
> `text/plain` (anti-phishing), so it can't serve web pages.

## Develop, test, deploy

Start the frontend locally:

```bash
cd frontend
npm ci
npm run dev
```

Build and test the frontend from the repository root:

```bash
npm --prefix frontend run build
npm --prefix frontend test
```

Run the AI policy and evaluation suites from the repository root with Node.js 22
or newer:

```bash
node --test supabase/functions/tests/ai_policy.test.ts supabase/functions/tests/ai_eval.test.ts
```

The engine's own tests and the six-household oracle fixtures live under
`engine/tests/`. See the [engine guide](../engine/README.md) for Python setup,
OCR dependencies, command-line entry points, and fixture evaluation.

Production deployment is manual. From the repository root:

```bash
./frontend/deploy.sh
```

## Ground rules for all frontend work

1. **Mobile friendly, always** — verified at 320 / 375 / 768 px, no horizontal overflow, touch
   targets ≥ 44px.
2. **WCAG 2.2 AA, always** — keyboard-complete, visible focus, labeled controls, no color-only
   signaling, live-region announcements, AA contrast on the warm palette.
3. **i18n is law** — every user-facing string ships in all five languages
   (`frontend/lib/dictionaries.ts` / `lib/pipeline/copy.ts`, mirrored in Supabase
   `i18n_translations`); never hardcode copy in components.
4. **Never-strings** — no decision / approval / eligibility language anywhere: UI, logs, exports,
   or model output. Refusals only, phrased in the negative.

## Local tooling (MCP)

`.mcp.json` (machine-local, gitignored) wires **Playwright** (browser E2E / responsive
checks) and **Remotion** (programmatic demo video). Both run on demand via `npx …@latest`.

## Tech stack

#### Frontend
<p align="left">
  <img src="https://img.shields.io/badge/Next.js%2016-000000?style=for-the-badge&logo=next.js&logoColor=white" />
  <img src="https://img.shields.io/badge/React%2019-61DAFB?style=for-the-badge&logo=react&logoColor=black" />
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white" />
  <img src="https://img.shields.io/badge/Tailwind%20CSS-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white" />
  <img src="https://img.shields.io/badge/Leaflet-199900?style=for-the-badge&logo=leaflet&logoColor=white" />
  <img src="https://img.shields.io/badge/pdf.js-B30B00?style=for-the-badge" />
</p>

#### Engine & AI
<p align="left">
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" />
  <img src="https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white" />
  <img src="https://img.shields.io/badge/Tesseract%20OCR-5A5A5A?style=for-the-badge" />
  <img src="https://img.shields.io/badge/OpenAI-412991?style=for-the-badge&logo=openai&logoColor=white" />
</p>

#### Platform & Data
<p align="left">
  <img src="https://img.shields.io/badge/Vercel-000000?style=for-the-badge&logo=vercel&logoColor=white" />
  <img src="https://img.shields.io/badge/Supabase-3FCF8E?style=for-the-badge&logo=supabase&logoColor=white" />
  <img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white" />
  <img src="https://img.shields.io/badge/Deno-000000?style=for-the-badge&logo=deno&logoColor=white" />
  <img src="https://img.shields.io/badge/OpenStreetMap-7EBC6F?style=for-the-badge&logo=openstreetmap&logoColor=white" />
</p>

#### Testing & Tooling
<p align="left">
  <img src="https://img.shields.io/badge/Vitest-6E9F18?style=for-the-badge&logo=vitest&logoColor=white" />
  <img src="https://img.shields.io/badge/pytest-0A9EDC?style=for-the-badge&logo=pytest&logoColor=white" />
  <img src="https://img.shields.io/badge/Playwright-2EAD33?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Mermaid-FF3670?style=for-the-badge&logo=mermaid&logoColor=white" />
</p>
