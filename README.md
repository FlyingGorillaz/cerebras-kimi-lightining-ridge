# cerebras-kimi-lightining-ridge

Transparency pack for the OpenClaw Speed Bounty entry: patched OpenClaw + Kimi K3.1 Pro on Cerebras, finding five real Christmas gifts from real retail product pages.

## Headline

- Shopify-catalog evidence (`evidence/full-session.mp4`): shopping turn **604 tok/s mean · 1,082 p90 (server)**; session summary **516 tok/s mean · 1,082 p90 (server)**.
- Best TPSBounty-style evidence (`evidence/tpsbounty-base-1218-1713-links.mp4`): patched OpenClaw + Kimi K3.1 Pro on Cerebras, zero-plugin base config + live `exec` retail fetch, speed-patch badge **1,218 tok/s mean · 1,713 p90 (server)**, ending on a slow scroll through all five full product URLs.
- Earlier TPSBounty-style run (`evidence/tpsbounty-base-1163-1564.mp4`): same compliant path at **1,163 tok/s mean · 1,564 p90 (server)** with five live product links.
- Discord one-number entry: **1,713 TPS** p90 (**1,218** mean). See `evidence/TPS.md` for mean/p90 math, attempt variance, and caveats.

## Evidence files

- `evidence/full-session.mp4` — recorder-rendered Control UI pass: shopper prompt → five parallel Shopify catalog calls → raw JSON with product links → speed badge → cart page.
- `evidence/tpsbounty-base-1163-1564.mp4` — recorder-rendered Control UI pass for the TPSBounty-style base run: prompt → live `exec` retail fetch → five product links → speed badge.
- `evidence/tpsbounty-base-1218-1713-links.mp4` — recorder-rendered best run: OpenClaw session badge **1,218 mean / 1,713 p90**, then a slow end-card scroll through all five full product URLs.
- `evidence/tpsbounty-base-attempt2-result.json` / `evidence/tpsbounty-base-attempt2-result.txt` — extracted Control UI state for the 1,713 p90 attempt.
- `evidence/tpsbounty-base-links-card.html` — the end-card page used in the video; it lists the five product names, stores, USD prices, and full URLs.
- `evidence/tpsbounty-base-result.json` / `evidence/tpsbounty-base-result.txt` — extracted Control UI state for the earlier 1,163/1,564 run.
- `evidence/tpsbounty-base-openclaw.json` — sanitized OpenClaw config for the TPSBounty-style run; API key is only the `${CEREBRAS_API_KEY}` placeholder.
- `evidence/tpsbounty-base-AGENTS.md` / `evidence/tpsbounty-base-fetch_gifts.mjs` — exact workspace prompt/fetcher used for the live retail lookup.
- `evidence/cart-proof.html` / `evidence/cart-proof.png` — cart rendering of the original five products, stores, AUD prices, audiences, and product-page links.
- `evidence/prompt-shopper.txt` — exact prompt sent to the original OpenClaw speed agent.
- `evidence/openclaw.json` — sanitized OpenClaw config for the original catalog run; API key is only the `${CEREBRAS_API_KEY}` placeholder.
- `evidence/TPS.md` — TPS math, caveats, TPSBounty repo findings, congestion notes, and reproduction steps.

## The five products

| # | Gift | Store | Audience | Price | Product page |
|---:|---|---|---|---|---|
| 1 | Christmas On-The-Go Jar | SparklingToysWorld | kids | A$21 | https://sparklingtoysworld.com/products/christmas-playdough-jar-copy?variant=44388243112118 |
| 2 | Moving Mountains Mustard Seed Faith Pendant Necklace | Blessings is Here | teens | A$25 | https://blessingsishere.com/products/14kgold-stainless-steel-moving-mountains-mustard-seed-faith-pendant-necklaces-for-women-men-boys-teens-girls-christmas-gifts?variant=43145774891082 |
| 3 | Vieillir Ensemble Funny Santa Couple Personalized Wood Ornament | ALMAGEMS | adults | A$19.95 | https://almagems.com/products/vieillir-ensemble-funny-adult-santa-couple-in-front-of-log-cabin-personalized-custom-wood-ornament-custom-shaped-cpl2265_wdo?variant=58685319905583 |
| 4 | A Grandparent’s Love Is Forever Personalized Wood Ornament | Pawfect House | grandparents | A$31 | https://pawfecthouse.com/products/a-grandparents-love-is-forever-family-personalized-custom-ornament-wood-custom-shaped-christmas-gift-for-grandma-grandpa?variant=51659353129249 |
| 5 | Custom Photo Pet Face Personalized Circle Glass Ornament | Wander Prints | pets | A$34 | https://wanderprints.com/products/custom-photo-pet-face-christmas-gift-for-dog-lovers-cat-lovers-pet-lovers-pet-memorial-personalized-circle-glass-ornament?variant=50383910273307 |

## TPSBounty-style follow-up products

These are live Shopify storefront `search/suggest` results fetched by `fetch_gifts.mjs` during the run (USD prices shown by the storefront data):

| # | Gift | Store | Price | Product page |
|---:|---|---|---:|---|
| 1 | Original Pillow | Casper | $75.00 | https://casper.com/products/pillows |
| 2 | Dino World XL 50 Piece Set | MAGNA-TILES | $129.99 | https://www.magnatiles.com/products/dino-world-xl-50-piece-set |
| 3 | The Everywhere Bag in Jet Black | Away | $148.00 | https://www.awaytravel.com/products/everywhere-bag-jet-black |
| 4 | Allbirds Wool Slipper - Light Tan | Allbirds | $15.60 | https://www.allbirds.com/products/allbirds-slipper |
| 5 | Stagg EKG Electric Kettle | Fellow | $143.96 | https://fellowproducts.com/products/stagg-ekg-electric-pour-over-kettle |

## No underhand tactics checklist

- Products came from OpenClaw’s Shopify Global Catalog MCP `search_catalog` calls visible in the video, not hard-coded results.
- TPS is the official OpenClaw speed patch reading Cerebras `time_info.completion_time` + `usage.completion_tokens`; there is no client-side timing spoofing.
- Reasoning was pinned low (`agents.defaults.thinkingDefault:"low"`) and the prompt forbids deliberation/commentary to keep the reasoning model fast.
- Kimi K3.1 Pro trial on Cerebras was the bounty model; Qwen/Gemma/GLM/GPT-OSS were not used for this bounty evidence.
- Cerebras intermittently returned `429 queue_exceeded`; this evidence is from a successful session and congestion handling is documented in `evidence/TPS.md`.
- The TPSBounty `max_completion_tokens=2` burst/padding path was studied but not used; it is not part of the claimed evidence.
- The TPSBounty clean-catalog plugin was reproduced and was slower (**693/728**) than the zero-plugin base run.
- Longer live-data fields did not increase output tokens: Kimi copied only the five title lines, so the longer-output lever did not materialize on this path.
- The >1,600 p90 result came from the same compliant path; six attempts produced p90s from 1,155 to 1,713 with ~299 output tokens each, so the variance is Cerebras decode-window timing rather than extra generated tokens.
- No API keys or secrets are committed; the configs use only `${CEREBRAS_API_KEY}`.
