# Lucky Squares — St. Patrick's Day 2026 — Build Notes
_Contest period: March 2026. Status: Completed._

## Overview
Real-time multiplayer browser game for remote sales team. Firebase Firestore backend (not Realtime Database — note the distinction from March Madness).

## Features
- Prize reveals
- Admin controls
- Leaderboard
- Sound effects
- Real-time multiplayer via Firestore listeners

## Architecture
- Hosting: (confirm URL)
- Data: Firebase Firestore
- No HubSpot sync — standalone game, not tied to sales metrics

## Scheduled Tasks
None — this contest had no HubSpot sync requirement.

## Lessons for _template\
- Firestore worked well for multiplayer state — consider as alternative to Realtime DB for future games with complex state
- Sound effects were a strong engagement driver — include audio layer in template scaffold
