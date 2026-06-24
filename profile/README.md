# Born In, Plays For

Interactive visualisation of the 2026 FIFA World Cup tracking player "exports": players born in one country who represent another.

**Live at [mundial.cthiebaud.com](https://mundial.cthiebaud.com/)**

## Repositories

| Repo | Purpose |
|---|---|
| [mundial](https://github.com/born-in-plays-for/mundial) | Static frontend — D3.js choropleth map, Elo rankings, live game page, infographics |
| [mundial-data](https://github.com/born-in-plays-for/mundial-data) | Shared data files (JSON, GeoJSON) — git submodule consumed by both mundial and mundial-build |
| [mundial-build](https://github.com/born-in-plays-for/mundial-build) | Data pipeline — Python scrapers, CSV processing, JSON generation |
| [mundial-server](https://github.com/born-in-plays-for/mundial-server) | Backend — Flask API, Google Sign-In, WebSocket, API-Football proxy |

## Architecture

```
mundial-build                mundial
  pipeline/ ──writes──▶ data/ (submodule)◀──reads── frontend (JS)
                          │
                    mundial-data repo
                          │
mundial-server ◀──Socket.IO──▶ auth-bar.js
```

The **pipeline** (mundial-build) scrapes player data, Elo ratings, and economic indicators, then writes JSON output to the **data** submodule. The **frontend** (mundial) fetches these files at runtime to render the map. The **backend** (mundial-server) handles authentication and live game updates via WebSocket.
