# MaritimeRisk

A single-file, Kpler-style maritime intelligence dashboard focused on the
**Strait of Hormuz** — realistic looks, real geography, and (optionally) real
live AIS data.

## Goal

Make it work like Kpler Maritime watching around Hormuz, with real data and
realistic looks.

## Running

Open `index.html` in a browser — no build step, no server required. Internet
access is needed for the basemap tiles (CARTO / OpenSeaMap) and the Leaflet
CDN.

## Data

- **Default:** a realistic simulation. Vessels follow the actual Traffic
  Separation Scheme lanes through the strait, call at real ports (Fujairah,
  Jebel Ali, Bandar Abbas, Sohar, …), and carry plausible AIS static data
  (MMSI with correct flag MID, IMO, DWT, draught, destination, ETA).
- **Live:** click the feed badge (or ⚙) and paste a free
  [aisstream.io](https://aisstream.io) API key to stream real AIS positions
  for the Hormuz bounding box over WebSocket. The key is kept in
  `localStorage` only.

## Architecture (index.html)

| Section | What it does |
|---|---|
| Map layer | Leaflet + CARTO dark tiles + OpenSeaMap seamarks; TSS lanes, anchorages, GNSS-interference zone, port markers |
| Simulation | Waypoint routes through the real TSS; per-vessel speed/course; time-lapse control (1×/60×/300×) |
| Live AIS | `aisstream.io` WebSocket client; merges PositionReport + ShipStaticData into the same vessel model |
| UI | Vessel list with type filters and search, events feed, vessel detail card, KPI strip, risk index |

## Agent roles

Project subagents used for larger tasks (definitions live in `~/.claude/agents/`):

- **scout** — read-only exploration; answers "where is X / who calls Y" with a
  condensed summary, never raw file dumps.
- **implementer** — executes an approved, scoped plan exactly; small
  reviewable changes; never touches files outside the plan's "Files touched"
  list; writes an audit to `feature-research/<task>/audit.md`.
- **reviewer** — independent, read-only review of plans and diffs; scope is
  the union of the plan's "Files touched" and the audit's "Files changed";
  reports Blocking issues, Non-blocking issues, and a ship / fix-first verdict.
