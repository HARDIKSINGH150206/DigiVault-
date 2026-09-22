cat > ~/Documents/VScode/Code/digivault/README.md << 'ENDOFFILE'
# DigiVault

> Secure digital evidence management for NCRB's Women Safety Division — Smart India Hackathon 2026 (Track 2: Web3 and Privacy for Billions)

Tamper-evident document storage with cryptographic hash chains, AI-assisted redaction, blockchain anchoring on Polygon Amoy, and consent-based sharing for courts and NGOs.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend + Backend | Next.js 15 (App Router, TypeScript) |
| AI Service | FastAPI (Python 3.14) |
| Database | PostgreSQL 17 (via Docker) |
| Object Storage | MinIO (via Docker) |
| Smart Contracts | Solidity + Hardhat, Polygon Amoy Testnet |
| ORM | Prisma |
| Crypto | WebCrypto API, js-sha256, Merkle trees |

---

## Prerequisites

- Node.js 20+
- pnpm (`npm install -g pnpm`)
- Docker + Docker Compose
- Python 3.12+ with `uv` (`pip install uv`)

---

## Running Locally

### 1 — Environment setup

\`\`\`bash
cp .env.example .env
cp apps/web/.env.example apps/web/.env.local
cp apps/ai-service/.env.example apps/ai-service/.env
\`\`\`

Fill in the values in \`.env\`. The defaults in \`.env.example\` work for local development as-is.

### 2 — Install dependencies

\`\`\`bash
pnpm install
\`\`\`

### 3 — Start Docker (Postgres + MinIO)

\`\`\`bash
docker compose up -d
\`\`\`

Confirm: \`docker compose ps\` — both \`digivault-postgres\` and \`digivault-minio\` should show \`Up\`.

### 4 — Run database migrations

\`\`\`bash
npx prisma migrate dev
\`\`\`

### 5 — Start the AI service

Open a new terminal:
\`\`\`bash
cd apps/ai-service
uv run uvicorn app.main:app --port 8001 --reload
\`\`\`

Confirm: \`curl http://localhost:8001/health\` → \`{"status":"ok"}\`

### 6 — Start the Next.js backend + frontend

Open another terminal:
\`\`\`bash
cd apps/web
pnpm dev
\`\`\`

App is live at **http://localhost:3000**

---

## Demo Golden Path

1. Open \`http://localhost:3000\` → log in
2. Create a case → upload a PDF (FIR scan)
3. Wait for AI redaction suggestions (status: READY)
4. Select suggestions → confirm redactions
5. Anchor the version to Polygon Amoy blockchain
6. Generate a share link for a court recipient (no login required for recipient)
7. Open \`http://localhost:3000/verify\` → load proof bundle → verify tamper-evidence

---

## Project Structure

\`\`\`
docs/                   — architecture, crypto spec, API spec, demo script
packages/
  crypto-core/          — shared Merkle tree & WebCrypto logic
  contracts/            — EvidenceAnchor.sol, Polygon Amoy deployment
apps/
  web/                  — Next.js: officer dashboard + Court Verification Portal
  ai-service/           — FastAPI: AI redaction suggestion engine
prisma/
  schema.prisma         — database source of truth
  migrations/           — applied migrations (do not edit manually)
docker-compose.yml      — Postgres 17 + MinIO
CLAUDE.md               — AI coding assistant rules (auto-loaded by Claude Code)
\`\`\`

---

## Branch Strategy

| Branch | Purpose |
|---|---|
| \`main\` | Stable, demo-ready. Protected — no direct pushes. |
| \`dev\` | Integration branch. All PRs target here. |
| \`feat/*\` | Feature branches. Open PRs into \`dev\`. |

\`\`\`bash
git checkout dev
git checkout -b feat/your-feature-name
git push origin feat/your-feature-name
# Open PR into dev on GitHub
\`\`\`

---

## Key Architectural Rules

- **PNG only** — never JPEG for tile storage. Lossy compression breaks Merkle hashes.
- **AI is advisory only** — OCR/NER suggests tiles; Merkle root computed from raw pixels only.
- **Dual anchor** — every version must go to MinIO Object Lock AND Polygon Amoy.
- **Client-side hashing first** — SHA-256 in-browser before upload; server re-verifies.
- **Court Verifier is stateless** — no auth, no DB calls, reads contract address from proof file.

See \`CLAUDE.md\` for the full rules before writing any code.

---

## Smart Contract

Deployed on Polygon Amoy Testnet:
- Address: \`0x6B8c4e1Dce347b9fa40381818217C6262EB56Ba4\`
- Explorer: [amoy.polygonscan.com](https://amoy.polygonscan.com/address/0x6B8c4e1Dce347b9fa40381818217C6262EB56Ba4)
ENDOFFILE