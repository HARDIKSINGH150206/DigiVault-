# DigiVault

Secure evidence document system for NCRB's Women Safety Division — Smart India Hackathon.

## Before you open Claude Code

1. Read `CLAUDE.md` — it's auto-loaded every session, but skim it once yourself so you know what rules are in force.
2. Read `docs/01-problem-and-novelty.md` and `docs/02-architecture-and-dataflow.md`.
3. Check `.claude/settings.json` — GitHub push actions are hard-denied there. Verify the rules loaded correctly with `/permissions` inside a Claude Code session before you start real work.

## Quickstart

```
pnpm install
docker compose up -d        # Postgres + MinIO
npx prisma migrate dev --name init
```

## Structure

```
docs/                  — architecture, crypto spec, API spec, demo script
packages/crypto-core/  — shared Merkle tree & WebCrypto logic (build this first)
packages/contracts/    — EvidenceAnchor.sol, Polygon Amoy deployment
apps/web/               — Next.js app: dashboard + Court Verification Portal
apps/ai-service/        — FastAPI: Bhashini OCR + IndicNER redaction targeting
prisma/schema.prisma    — database source of truth
```

See `CLAUDE.md` for the build order and the non-negotiable architectural rules.
