# March Madness 2026 — Build Notes
_Contest period: March 2026. Status: Completed._

## Overview
Sales team bracket contest for SDR and AE teams. Firebase Realtime Database backend, HubSpot MCP sync via scheduled tasks, GitHub Pages hosting.

## Scoring
- Warm transfers
- Calls/day thresholds
- Closed won

## Architecture
- Hosting: GitHub Pages
- Data: Firebase Realtime Database (project: planhub-march-madness)
- Sync: Claude Code scheduled tasks → HubSpot MCP → Firebase REST API
- Real-time: Firebase listeners pushed to all connected browsers

## Scheduled Tasks
- scheduled-tasks\march-madness-hubspot-sync
- scheduled-tasks\march-madness-sync-v2
- scheduled-tasks\march-madness-daily-recap
- scheduled-tasks\march-madness-recap-v2

## Deployment Plan
See lovely-pondering-melody.md in this folder for full Firebase + GitHub Pages deployment steps.

## Lessons for _template\
- Seeding was availability-adjusted — document this logic in template
- Snake draft for team construction worked well — add to template
- Firebase test mode (30-day open rules) was sufficient for contest window
