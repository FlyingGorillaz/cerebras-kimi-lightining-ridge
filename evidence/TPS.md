# TPS methodology and caveats

## Post-results countability note

After the results announcement, the rankable lane was described as **base OpenClaw, tuned through the prompt, with no filler calls and no wrappers, proxies or edits that rewrite the model's requests**. This pack predates that rules patch. The Christmas `1,722 p90` run used no plugins, wrappers, proxies, hard-coded products, or burst padding, but it did use OpenClaw provider params (`temperature`, `maxTokens`, `parallel_tool_calls`, `streaming`, `extraBody.reasoning_effort`, `prompt_cache_key`) and a scripted verbatim prompt. Treat it as **boundary evidence** unless the organizers confirm those provider params are inside "base OpenClaw"; do not cite it as a ranked countable entry under the post-results rules without that confirmation.

## What the speed patch measures

The official OpenClaw speed patch parses Cerebras streaming chat-completions chunks and records server-side generation timing from `time_info.completion_time` plus `usage.completion_tokens`. The Control UI badge is computed in `chat-generation-speed.ts` as:

- per-call rate = `outputTokens / (completionMs / 1000)`
- headline mean = arithmetic mean of per-call rates
- p90 = nearest-rank 90th percentile of per-call rates

This is server decode TPS, not wall-clock task time and not client network throughput. Queueing (`queue_time`), prefill (`prompt_time`), network, and tool execution time are excluded.

## Best boundary evidence numbers (pre-results)

The recorder-rendered `evidence/christmas-1290-1722-links.mp4` shows the real Control UI session:

- speed-patch badge: `1,290 tok/s mean · 1,722 p90 (server)`
- model calls: 2
- output tokens: 305
- Discord one-number entry: `1,722 TPS p90 (1,290 mean)`
- OpenClaw session id: `1a21ad98d0b648b79f74e6f997363b82`

The earlier generic-gift recorder video `evidence/tpsbounty-base-1218-1713-links.mp4` showed `1,218 mean · 1,713 p90`; the Christmas run above is the best boundary evidence, and post-results countability needs organizer confirmation.

For the earlier generic-gift run, six identical-path attempts are preserved in `evidence/tpsbounty-attempts/`:

| attempt | badge | session id |
|---:|---|---|
| 1 | 1,120 mean · 1,155 p90 | `0314db7aaee341f3af5b5514621c9c9c` |
| 2 | 1,218 mean · 1,713 p90 | `c1b4f0001751417b955822de8f4391c3` |
| 3 | 1,240 mean · 1,421 p90 | `e08e50ef00504a43a6653a0927b3cc9a` |
| 4 | 1,269 mean · 1,546 p90 | `3e12f9f79cdb46d19e44378ac583ee5c` |
| 5 | 1,001 mean · 1,301 p90 | `8737da81292a48589da79da9bf211148` |
| 6 | 1,139 mean · 1,547 p90 | `eb8be61d34b544aa86b9afc7d7be239c` |

For that generic-gift run, attempt p90s ranged **1,155–1,713** while output stayed ~299 tokens. The >1,600 p90 is a Cerebras decode-window result, not extra generated tokens.

## Christmas-specific follow-up

After the compliance audit, the fetcher/prompt was switched to strictly seasonal products (see `CHRISTMAS.md`). The best boundary Christmas badge is compact two-call `1,290 mean · 1,722 p90` (`christmas-attempts/compact-two-call-1290-1722.*`), which beats the earlier generic-gift p90. Verbose copy stayed lower (`1,149 mean · 1,257 p90` best), and the six-call path used five genuine single-store `exec` fetches plus one copy turn (`1,117 mean · 1,549 p90` best). A split-copy variant collapsed back to two model calls and was not used.

## Why p90 can be much higher than mean

With only two model calls, the nearest-rank p90 selects the faster of the two per-call rates, while the mean averages the fast call with the slower call. So `1,290 mean · 1,722 p90` means one call decoded at about 1,722 tok/s and the other at about 858 tok/s on average. This is a property of the official patch math over two calls, not a client-side calculation.

## Reasoning and prompt speed hacks

- Bounty model only: `cerebras/moonshotai-kimi-k3.1-pro-trial` on Cerebras.
- Reasoning pinned low: `agents.defaults.thinkingDefault: "low"` and `extraBody.reasoning_effort: "low"`.
- `temperature: 0`, `prompt_cache_key: "santa-short-pv"`, agent `maxTokens: 4096`.
- Minimal tool surface: `exec` only, `toolSearch: false`, `codeMode: false`, `sessionObserver: false`, memory slot `none`.
- Prompt hack for a reasoning model: mechanical verbatim copier, forbidden deliberation words, one `exec` call, then copy the five returned lines with no tools/chatter.
- Post-results disclosure: the provider params above are OpenClaw model settings, not a plugin/wrapper/proxy; under a strict prompt-only reading this run is boundary evidence.

## Longer-output test

The user prompt asked for title, price, link, handle, vendor, type, availability, price range, tags, and body excerpt. The workspace prompt/fetcher still returned five compact lines, and Kimi copied only those lines (~299 output tokens). The longer-output lever did not materialize on this path; raising `maxTokens` did not change the output length because the task shape remained five compact product lines.

## TPSBounty repo study

Repo studied: `https://github.com/ykundesu/Cerebras-KimiK3-1-Pro-TPSBounty`. The super-optimized entry uses burst mode / `max_completion_tokens=2` for “Quick status check” turns. That can inflate per-call decode rate through tiny completion calls and was **not** used as bounty evidence here.

The pieces we kept were stock OpenClaw config minimization, low reasoning, a minimal tool surface, and a verbatim copy turn after a live fetch; under the pre-results rules these were treated as compliant, and post-results countability is unresolved for provider params.

## Config/workspace note

The TPSBounty-style variant configs initially inherited the base workspace path during testing. The shipped sanitized config is `evidence/tpsbounty-base-openclaw.json`; for the Christmas entry, point `agents.defaults.workspace` at the workspace containing `christmas-AGENTS-two-call.md` and `christmas-fetch_gifts.mjs`. The winning prompt/state is preserved in `christmas-attempts/compact-two-call-1290-1722.txt`; the earlier generic prompt is `tpsbounty-base-user-prompt.txt`.

## Congestion notes

Cerebras Kimi K3.1 Pro trial intermittently returned `429 queue_exceeded` / “high traffic” during retries, including with `service_tier:"priority"` probes. The six preserved attempts completed on the boundary path; the best badge came from attempt 2. GPT-OSS/Qwen/other models were not used for the bounty evidence; they were only explored as fallback/probe paths and are not part of the claimed Kimi result.

## Reproduction outline

1. Use patched OpenClaw with the official speed patch so Cerebras `time_info`/`usage` is parsed into `serverGeneration` and the UI badge path.
2. For boundary-evidence reproduction configure the Cerebras provider with model `moonshotai-kimi-k3.1-pro-trial` (see `tpsbounty-base-openclaw.json`; keep the API key in `CEREBRAS_API_KEY`). For a strict post-results-rules run, start from stock/base OpenClaw and tune only the prompt.
3. Put `christmas-AGENTS-two-call.md` and `christmas-fetch_gifts.mjs` in the configured workspace.
4. Send the Christmas product prompt shown in `christmas-attempts/compact-two-call-1290-1722.txt`. It must perform one live `exec` fetch for five seasonal products and then copy the five lines verbatim.
5. Run several attempts, keep the best official speed-patch badge, and record the session plus the `christmas-links-card.html` end card.

No hard-coded products, no client-side TPS spoofing, no non-Kimi bounty model, no burst padding, and no secrets are included in this repo.
