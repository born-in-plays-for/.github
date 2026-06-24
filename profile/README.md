# Born In, Plays For

Interactive visualisation of the 2026 FIFA World Cup tracking player "exports": players born in one country who represent another.

**Live at [mundial.cthiebaud.com](https://mundial.cthiebaud.com/)**

## Repositories

| Repo | Purpose |
|---|---|
| [mundial](https://github.com/born-in-plays-for/mundial) | Static frontend — D3.js choropleth map, Elo rankings, live game page |
| [mundial-data](https://github.com/born-in-plays-for/mundial-data) | Shared data files (JSON, GeoJSON) — git submodule consumed by both mundial and mundial-build |
| [mundial-build](https://github.com/born-in-plays-for/mundial-build) | Data pipeline — Python scrapers, CSV processing, JSON generation |
| [mundial-server](https://github.com/born-in-plays-for/mundial-server) | Backend — Flask API, Google Sign-In, WebSocket, API-Football proxy |

## Architecture

```mermaid
graph LR
    subgraph mundial-build
        pipeline[Pipeline<br/>Python scrapers]
    end

    subgraph mundial-data
        data[(JSON / GeoJSON)]
    end

    subgraph mundial
        frontend[Frontend<br/>D3.js + lit-html]
    end

    subgraph mundial-server
        backend[Backend<br/>Flask + Socket.IO]
    end

    pipeline -- writes --> data
    data -- submodule --> frontend
    data -- submodule --> pipeline
    backend <-- WebSocket --> frontend
```

## CI / GitHub Actions

```mermaid
graph LR
    A["⏰ daily 07:00 UTC<br/><b>mundial-build</b><br/>Update Elo Rankings"] -- pushes JSON --> B["<b>mundial-data</b>"]
    A -- repository_dispatch --> C["<b>mundial</b><br/>Update data submodule"]
    C -- pulls latest --> B
    C -- push triggers --> D["<b>mundial</b><br/>Deploy to GitHub Pages"]
    D -- publishes --> E["mundial.cthiebaud.com"]
```

| Workflow | Repo | Trigger | What it does |
|---|---|---|---|
| Update Elo Rankings | mundial-build | Daily 07:00 UTC / manual | Scrapes eloratings.net, pushes to mundial-data, dispatches to mundial |
| Update data submodule | mundial | `repository_dispatch` / manual | Pulls latest mundial-data, commits new submodule pointer |
| Deploy to GitHub Pages | mundial | Push to main / manual | Deploys site (data submodule cached for fast code-only deploys) |

Cross-repo actions use a fine-grained PAT (`MUNDIAL_DISPATCH_TOKEN` secret on mundial-build) with Contents write access to mundial and mundial-data.
