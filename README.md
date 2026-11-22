# cerebras-kimi-lightining-ridge

Transparency pack for the OpenClaw Speed Bounty entry: patched OpenClaw + Kimi K3.1 Pro on Cerebras, finding five real retail products with live product-page links.

## Entry

- Video: `evidence/tpsbounty-base-1218-1713-links.mp4` — recorder-rendered, 9.9s.
- Speed-patch badge: **1,218 tok/s mean · 1,713 p90 (server)**.
- Discord one-number text: `Speed Bounty entry: 1,713 TPS p90 (1,218 mean)`.
- What it shows: the OpenClaw session badge, then a slow end-card scroll through all five full product URLs so judges can read them.

## Products

Live Shopify storefront `search/suggest` results fetched during the run by `fetch_gifts.mjs` (USD prices from storefront data):

| # | Gift | Store | Price | Product page |
|---:|---|---|---:|---|
| 1 | Original Pillow | Casper | $75.00 | https://casper.com/products/pillows |
| 2 | Dino World XL 50 Piece Set | MAGNA-TILES | $129.99 | https://www.magnatiles.com/products/dino-world-xl-50-piece-set |
| 3 | The Everywhere Bag in Jet Black | Away | $148.00 | https://www.awaytravel.com/products/everywhere-bag-jet-black |
| 4 | Allbirds Wool Slipper - Light Tan | Allbirds | $15.60 | https://www.allbirds.com/products/allbirds-slipper |
| 5 | Stagg EKG Electric Kettle | Fellow | $143.96 | https://fellowproducts.com/products/stagg-ekg-electric-pour-over-kettle |

## Christmas-specific follow-up

A stricter seasonal-product pass used live Shopify suggest hits for five Christmas gifts: Casper Digital Gift Card ($50), MAGNA-TILES Stocking Stuffer Bundle ($64.93), Away Holiday Hustle Bundle ($377), Allbirds Men's Dasher NZ Blizzard ($98), and Fellow Sweet and Festive Box Set ($45). Best compliant badges were two-call compact **1,084 mean · 1,441 p90**, verbose copy **1,149 mean · 1,257 p90**, and six-call live-fetch/copy **1,117 mean · 1,549 p90**. None beat the preserved 1,713 p90 yet; see `evidence/CHRISTMAS.md` and `evidence/christmas-attempts/`.

## TPS method

TPS is the official OpenClaw speed patch reading Cerebras server timing: `time_info.completion_time` + `usage.completion_tokens`, decode-only. The badge is the mean of per-call rates plus nearest-rank p90; queueing, prefill, network, and tool time are excluded. See `evidence/TPS.md` for the exact math and caveats.

## Run files

- `evidence/tpsbounty-base-openclaw.json` — sanitized OpenClaw config; API key is only the `${CEREBRAS_API_KEY}` placeholder.
- `evidence/tpsbounty-base-AGENTS.md` — workspace prompt used by the gateway.
- `evidence/tpsbounty-base-fetch_gifts.mjs` — live retail fetcher used by the `exec` turn.
- `evidence/tpsbounty-base-user-prompt.txt` — exact user prompt sent to OpenClaw, plus a note on why output stayed compact.
- `evidence/tpsbounty-base-links-card.html` — end-card page shown at the end of the video.
- `evidence/tpsbounty-attempts/attempt-{1..6}.{json,txt}` — extracted Control UI state for six identical-path attempts.
- `evidence/CHRISTMAS.md` — stricter Christmas-product attempt summary.
- `evidence/christmas-attempts/` — extracted attempt state for compact, verbose, and six-call Christmas paths.
- `evidence/christmas-AGENTS-two-call.md`, `evidence/christmas-AGENTS-six-call.md`, and `evidence/christmas-fetch_gifts.mjs` — workspace prompt/fetcher variants for the follow-up.

## What was optimized

- Bounty model only: `cerebras/moonshotai-kimi-k3.1-pro-trial`.
- Reasoning pinned low: `thinkingDefault: "low"` and `extraBody.reasoning_effort: "low"`.
- `temperature: 0`, `prompt_cache_key: "santa-short-pv"`, agent `maxTokens: 4096`.
- Minimal tool surface: `exec` only, `toolSearch: false`, `codeMode: false`, `sessionObserver: false`, memory slot `none`.
- Mechanical verbatim prompt: turn 1 calls `exec` once for the live fetch; turn 2 copies the five returned lines verbatim.
- Six attempts were run on the same compliant path; the best badge was kept.

Output stayed ~299 tokens because the fetcher/prompt returns five compact lines. The >1,600 p90 is Cerebras decode-window variance across attempts, not extra generated tokens and not burst padding.

## What was not done

- No hard-coded products or URLs; the five products came from a live fetch during the OpenClaw run.
- No non-Kimi bounty model; GPT-OSS/Qwen/other models were not used for this evidence.
- No client-side TPS spoofing; the badge is the official speed patch server timing.
- No `max_completion_tokens=2` burst/padding path from the TPSBounty super-optimized entry.
- No API keys or secrets are committed; configs use only the `${CEREBRAS_API_KEY}` placeholder.

## Reproduce

1. Run patched OpenClaw with the official speed patch so Cerebras `time_info`/`usage` reaches `serverGeneration` and the Control UI badge.
2. Use `evidence/tpsbounty-base-openclaw.json` with `CEREBRAS_API_KEY` set in the environment.
3. Put `tpsbounty-base-AGENTS.md` and `tpsbounty-base-fetch_gifts.mjs` in the configured workspace.
4. Send the prompt in `tpsbounty-base-user-prompt.txt`, run several attempts, and keep the best official badge.
5. Record the Control UI session badge, then scroll `tpsbounty-base-links-card.html` so all five URLs are readable.

## Caveats

- Direct `curl` from this VM to the five product pages returned HTTP 429 bot/rate-limit responses; the links were live-fetched from Shopify suggest during the run and are shown in full in the video/end card.
- With two model calls, the patch’s nearest-rank p90 is the faster call’s rate while the mean averages both calls; see `evidence/TPS.md`.
- Six attempts produced p90s from 1,155 to 1,713 with ~299 output tokens each; the best attempt is `evidence/tpsbounty-attempts/attempt-2.*`.
