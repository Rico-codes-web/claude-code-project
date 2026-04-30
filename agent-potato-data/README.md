# Agent Potato Data

This folder contains open-source geographic and transit data gathered by Agent Potato for use in MMITS Hiraya Transit.

## Contents

- `lrt_mrt_stations.json` — Accurate LRT-1, LRT-2, MRT-3 station coordinates from OpenStreetMap
- `traffic_feeds.json` — Metro Manila road traffic speed data from open sources
- `stoplights_ncr.json` — NCR traffic signal intersection coordinates from Overpass API

## Sources

All data is sourced exclusively from open/free sources:
- OpenStreetMap / Overpass API
- GTFS static feeds
- MMDA open data
- Philippine government open data portals

## Usage

Before coding any map feature in `MMITS Hiraya Transit.html`, Agent Potato is spawned to refresh this data. The HTML file's `GEO_LRT1`, `GEO_LRT2`, `GEO_MRT3`, `TRAFFIC_OPEN_FEED_SAMPLE`, and `STOPLIGHT_DATASET` constants are sourced from here.
