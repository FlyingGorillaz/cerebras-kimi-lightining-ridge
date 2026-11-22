# cerebras-kimi-lightining-ridge

Transparency pack for the OpenClaw Speed Bounty entry: patched OpenClaw + Kimi K3.1 Pro on Cerebras, finding five real Christmas gifts from real retail product pages.

## Headline

- Speed-patch badge shown in `evidence/full-session.mp4`: shopping turn **604 tok/s mean · 1,082 p90 (server)**; session summary **516 tok/s mean · 1,082 p90 (server)**.
- Discord one-number entry used: **1,082 TPS** (p90 server decode). See `evidence/TPS.md` for why mean and p90 differ and why an earlier inflated 923 mean is not used.

## Evidence files

- `evidence/full-session.mp4` — recorder-rendered Control UI pass: shopper prompt → five parallel Shopify catalog calls → raw JSON with product links → speed badge → cart page.
- `evidence/cart-proof.html` / `evidence/cart-proof.png` — cart rendering of the same five products, stores, AUD prices, audiences, and product-page links.
- `evidence/prompt-shopper.txt` — exact prompt sent to the OpenClaw speed agent.
- `evidence/openclaw.json` — sanitized OpenClaw config; API key is only the `${CEREBRAS_API_KEY}` placeholder.
- `evidence/TPS.md` — TPS math, caveats, congestion notes, and reproduction steps.

## The five products

| # | Gift | Store | Audience | Price | Product page |
|---:|---|---|---|---|---|
| 1 | Christmas On-The-Go Jar | SparklingToysWorld | kids | A$21 | https://sparklingtoysworld.com/products/christmas-playdough-jar-copy?variant=44388243112118 |
| 2 | Moving Mountains Mustard Seed Faith Pendant Necklace | Blessings is Here | teens | A$25 | https://blessingsishere.com/products/14kgold-stainless-steel-moving-mountains-mustard-seed-faith-pendant-necklaces-for-women-men-boys-teens-girls-christmas-gifts?variant=43145774891082 |
| 3 | Vieillir Ensemble Funny Santa Couple Personalized Wood Ornament | ALMAGEMS | adults | A$19.95 | https://almagems.com/products/vieillir-ensemble-funny-adult-santa-couple-in-front-of-log-cabin-personalized-custom-wood-ornament-custom-shaped-cpl2265_wdo?variant=58685319905583 |
| 4 | A Grandparent’s Love Is Forever Personalized Wood Ornament | Pawfect House | grandparents | A$31 | https://pawfecthouse.com/products/a-grandparents-love-is-forever-family-personalized-custom-ornament-wood-custom-shaped-christmas-gift-for-grandma-grandpa?variant=51659353129249 |
| 5 | Custom Photo Pet Face Personalized Circle Glass Ornament | Wander Prints | pets | A$34 | https://wanderprints.com/products/custom-photo-pet-face-christmas-gift-for-dog-lovers-cat-lovers-pet-lovers-pet-memorial-personalized-circle-glass-ornament?variant=50383910273307 |

## No underhand tactics checklist

- Products came from OpenClaw’s Shopify Global Catalog MCP `search_catalog` calls visible in the video, not hard-coded results.
- TPS is the official OpenClaw speed patch reading Cerebras `time_info.completion_time` + `usage.completion_tokens`; there is no client-side timing spoofing.
- Reasoning was pinned low (`agents.defaults.thinkingDefault:"low"`) and the prompt forbids deliberation/commentary to keep the reasoning model fast.
- Kimi K3.1 Pro trial on Cerebras was the bounty model; Qwen/Gemma/GLM/GPT-OSS were not used for this bounty evidence.
- Cerebras intermittently returned `429 queue_exceeded`; this evidence is from a successful session and congestion handling is documented in `evidence/TPS.md`.
- No API keys or secrets are committed; the config uses only `${CEREBRAS_API_KEY}`.
