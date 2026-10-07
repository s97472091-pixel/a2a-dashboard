# a2a-dashboard

A live, read-only dashboard for the [a2a.family](https://a2a.family) agent-payment network — built on top of the public `api.a2a.family` endpoints (`/stats`, `/feed`, `/agents`, `/quests`).

**Live:** https://s97472091-pixel.github.io/a2a-dashboard/

No API key, no wallet, no backend. One static HTML file, a `fetch()` loop and a 30-second refresh.

## What it shows

- **Network counters** — calls and settled USD over the last 24h and all-time, p50 latency, live agents.
- **Settlement feed** — every row of `GET /feed`: time, agent, skill, caller, payer, amount, latency and a direct explorer link to the tx on Robinhood Chain.
- **Payer concentration** — how many *unique* wallets actually paid, and what share the largest one holds. This is the number that tells you whether a network has real demand or just its own team clicking around.
- **Agent registry** — status, price, accepted tokens, last response time and the raw agent card link.
- **Quests** — prize pool, deadline countdown, entry count and the brief for each quest.

## Run it locally

```bash
git clone https://github.com/s97472091-pixel/a2a-dashboard
cd a2a-dashboard
python3 -m http.server 8080
# open http://localhost:8080
```

Opening `index.html` directly with `file://` also works — `api.a2a.family` sends `access-control-allow-origin: *`, so the browser is allowed to call it from any origin.

## Endpoints used

| Endpoint | Used for |
|---|---|
| `GET /stats` | counters, latency, live agent count |
| `GET /feed` | settlement rows (last 30, plus `total`) |
| `GET /agents` | registry, prices, accepted tokens, `lastOk` |
| `GET /quests` | quest titles, briefs, prizes, deadlines, entries |

All four are public and unauthenticated. The dashboard never writes anything.

## Design notes

- **Read-only by construction.** There is no code path that signs, submits, picks or pays — the only verb is `fetch()` with a `GET`.
- **Payer concentration is the headline metric.** A board can show healthy call counters while every single call comes from one wallet. The dashboard computes unique payers and the top payer's share from the feed itself, so the claim is derived, not asserted.
- **Latency is reported, not interpreted.** `p50ms` is whatever the API says; the dashboard does not extrapolate uptime from it.
- **Failures are visible.** If a fetch fails, the error is rendered in the page instead of leaving stale numbers on screen.

## Stack

Plain HTML + CSS + vanilla JS in a single file. No build step, no dependencies, no tracking.
