# Christmas-specific follow-up

After the compliance audit, the live fetcher/workspace prompt was switched from generic giftable products to strictly seasonal Christmas products fetched from the same five real Shopify storefronts.

## Products

USD prices are storefront `search/suggest` data fetched live during the runs:

| # | Christmas gift | Store | Price | Product page |
|---:|---|---|---:|---|
| 1 | Digital Gift Card | Casper | $50.00 | https://casper.com/products/digital-gift-card |
| 2 | The Stocking Stuffer Bundle | MAGNA-TILES | $64.93 | https://www.magnatiles.com/products/the-stocking-stuffer-bundle |
| 3 | Holiday Hustle Bundle | Away | $377.00 | https://www.awaytravel.com/products/holiday-hustle-bundle |
| 4 | Men's Dasher NZ - Blizzard (Blizzard Sole) | Allbirds | $98.00 | https://www.allbirds.com/products/mens-dasher-nz-blizzard |
| 5 | Sweet and Festive Box Set | Fellow | $45.00 | https://fellowproducts.com/products/sweet-and-festive-box-set |

## Attempt results

| path | best badge | files |
|---|---:|---|
| compact two-call | 1,285 mean · 1,707 p90 | `christmas-attempts/compact-two-call-1285-1707.*` |
| verbose two-call | 1,149 mean · 1,257 p90 | `christmas-attempts/verbose-two-call-1149-1257.*` |
| six-call live fetch + copy | 1,117 mean · 1,549 p90 | `christmas-attempts/six-call-1117-1549.*` |
| six-call faster sample | 1,097 mean · 1,527 p90 | `christmas-attempts/six-call-1097-1527.*` |

Across Christmas two-call samples the compact path ranged roughly 797–1,707 p90, verbose copy stayed near 1,060–1,257 p90, and the six-call path produced 1,272–1,549 p90 depending on congestion and whether all six genuine calls completed. The closest compliant Christmas run was compact two-call `1,285 mean · 1,707 p90`, six TPS below the preserved generic-gift p90. A split-copy variant collapsed back to two model calls, so it was not used as evidence.

No Christmas-specific compliant run has beaten the preserved generic-gift entry badge of **1,218 mean · 1,713 p90** yet. The same constraints still apply: Kimi K3.1 Pro on Cerebras only, official speed patch only, live product links, no hard-coded products/URLs, no client-side TPS spoofing, and no burst padding.
