# cerebras-kimi-lightining-ridge

Transparency pack for the OpenClaw Speed Bounty boundary experiments: patched OpenClaw + Kimi K3.1 Pro on Cerebras, finding five real Christmas gifts from real retail sites with live product-page links.

## Post-results countability note

After the results announcement, the rankable lane was described as **base OpenClaw, tuned through the prompt, with no filler calls and no wrappers, proxies or edits that rewrite the model's requests**. This pack predates that rules patch. The Christmas `1,722 p90` run used no plugins, wrappers, proxies, hard-coded products, or burst padding, but it did use OpenClaw provider params (`temperature`, `maxTokens`, `parallel_tool_calls`, `streaming`, `extraBody.reasoning_effort`, `prompt_cache_key`) and a scripted verbatim prompt. Treat it as **boundary evidence** unless the organizers confirm those provider params are inside "base OpenClaw"; do not cite it as a ranked countable entry under the post-results rules without that confirmation.

## Best boundary evidence (pre-results)

- Video: `evidence/christmas-1290-1722-links.mp4` — recorder-rendered; shows the OpenClaw session badge, five Christmas products, then a slow end-card scroll through all five full product URLs.
- Boundary speed-patch badge: **1,290 tok/s mean · 1,722 p90 (server)**.
- Pre-results Discord text used: `Speed Bounty entry: 1,722 TPS p90 (1,290 mean)` — not a post-results ranked claim without organizer confirmation.
- OpenClaw session id: `1a21ad98d0b648b79f74e6f997363b82`.

## Christmas products

Live Shopify storefront `search/suggest` results fetched during the run by `christmas-fetch_gifts.mjs` (USD prices from storefront data):

| # | Christmas gift | Store | Price | Product page |
|---:|---|---|---:|---|
| 1 | Digital Gift Card | Casper | $50.00 | https://casper.com/products/digital-gift-card |
| 2 | The Stocking Stuffer Bundle | MAGNA-TILES | $64.93 | https://www.magnatiles.com/products/the-stocking-stuffer-bundle |
| 3 | Holiday Hustle Bundle | Away | $377.00 | https://www.awaytravel.com/products/holiday-hustle-bundle |
| 4 | Men's Dasher NZ - Blizzard (Blizzard Sole) | Allbirds | $98.00 | https://www.allbirds.com/products/mens-dasher-nz-blizzard |
| 5 | Sweet and Festive Box Set | Fellow | $45.00 | https://fellowproducts.com/products/sweet-and-festive-box-set |

## Earlier generic-gift run

A prior boundary run on the same pre-results OpenClaw + Kimi K3.1 Pro/Cerebras path used generic giftable products and reached **1,218 mean · 1,713 p90** (`evidence/tpsbounty-base-1218-1713-links.mp4`). The Christmas run above beats that p90 with stricter seasonal products.

## TPS method

TPS is the official OpenClaw speed patch reading Cerebras server timing: `time_info.completion_time` + `usage.completion_tokens`, decode-only. The badge is the mean of per-call rates plus nearest-rank p90; queueing, prefill, network, and tool time are excluded. See `evidence/TPS.md` for the exact math and caveats.

## Run files

- `evidence/tpsbounty-base-openclaw.json` — sanitized OpenClaw config; API key is only the `${CEREBRAS_API_KEY}` placeholder. The Christmas run used the same Kimi/Cerebras low-reasoning settings.
- `evidence/christmas-AGENTS-two-call.md` — workspace prompt used by the winning Christmas run.
- `evidence/christmas-fetch_gifts.mjs` — live retail fetcher used by the `exec` turn.
- `evidence/christmas-links-card.html` — end-card page shown at the end of the video.
- `evidence/christmas-attempts/compact-two-call-1290-1722.{json,txt}` — extracted Control UI state for the winning attempt.
- `evidence/christmas-attempts/` — extracted attempt state for compact, verbose, and six-call Christmas paths.
- `evidence/CHRISTMAS.md` — Christmas-product attempt summary.
- `evidence/tpsbounty-base-*` and `evidence/tpsbounty-attempts/` — preserved files for the earlier generic-gift 1,713 p90 run.

## What was optimized

- Bounty model only: `cerebras/moonshotai-kimi-k3.1-pro-trial`.
- Reasoning pinned low: `thinkingDefault: "low"` and `extraBody.reasoning_effort: "low"`.
- `temperature: 0`, `prompt_cache_key: "santa-short-pv"`, agent `maxTokens: 4096`.
- Minimal tool surface: `exec` only, `toolSearch: false`, `codeMode: false`, `sessionObserver: false`, memory slot `none`.
- Mechanical verbatim prompt: turn 1 calls `exec` once for the live Christmas fetch; turn 2 copies the five returned lines verbatim.
- Multiple pre-results attempts were run on the same path; the best official badge was kept.
- Post-results disclosure: the provider params above are OpenClaw model settings, not a plugin/wrapper/proxy; under a strict prompt-only reading this run is boundary evidence.

Output stayed ~305 tokens because the fetcher/prompt returns five compact lines. The >1,700 p90 is Cerebras decode-window variance across attempts, not extra generated tokens and not burst padding.

## What was not done

- No hard-coded products or URLs; the five Christmas products came from a live fetch during the OpenClaw run.
- No non-Kimi bounty model; GPT-OSS/Qwen/other models were not used for this evidence.
- No client-side TPS spoofing; the badge is the official speed patch server timing.
- No `max_completion_tokens=2` burst/padding path from the TPSBounty super-optimized entry.
- No santa-fast/trim plugin, proxy, or request-rewriting wrapper in the shipped Christmas path.
- No API keys or secrets are committed; configs use only the `${CEREBRAS_API_KEY}` placeholder.

## Reproduce

1. Run patched OpenClaw with the official speed patch so Cerebras `time_info`/`usage` reaches `serverGeneration` and the Control UI badge.
2. For boundary-evidence reproduction use `evidence/tpsbounty-base-openclaw.json` with `CEREBRAS_API_KEY` set; for a strict post-results-rules run, start from stock/base OpenClaw and tune only the prompt.
3. Put `christmas-AGENTS-two-call.md` and `christmas-fetch_gifts.mjs` in the configured workspace.
4. Send the Christmas product prompt shown in `christmas-attempts/compact-two-call-1290-1722.txt`, run several attempts, and keep the best official badge.
5. Record the Control UI session badge, then scroll `christmas-links-card.html` so all five URLs are readable.

## Caveats

- Direct `curl` from this VM to the five product pages returned HTTP 429 bot/rate-limit responses; the links were live-fetched from Shopify suggest during the run and are shown in full in the video/end card.
- With two model calls, the patch’s nearest-rank p90 is the faster call’s rate while the mean averages both calls; see `evidence/TPS.md`.
- Christmas two-call samples produced p90s up to 1,722; the best attempt is `evidence/christmas-attempts/compact-two-call-1290-1722.*`.
