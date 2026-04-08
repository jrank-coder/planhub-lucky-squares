# Contest Template
_Copy this folder to start a new contest. Rename to: <contest-name>-<year>\_

## Scaffold Checklist
- [ ] index.html — main app file
- [ ] build-notes.md — decisions, open questions, deployment steps
- [ ] Firebase project created and config pasted into index.html
- [ ] Scheduled tasks created in scheduled-tasks\ and referenced below
- [ ] GitHub Pages or hosting URL confirmed

## Architecture Pattern
- **Hosting:** GitHub Pages (index.html at public URL)
- **Data:** Firebase Realtime Database (all contest state)
- **Sync:** Claude Code scheduled task → HubSpot MCP → Firebase REST API (see Sync Pattern below)
- **UI:** Single-page app with admin panel, leaderboard, real-time updates

## Scoring Definitions
⚠️ All contest scoring metrics (calls, warm transfers, closes, etc.) **must align with** `context\metric-definitions.md`. Do not redefine metrics within the contest — reference the official definitions and apply the same filters (exclude `"gong"` calls, exclude `"playbook"` source, $0 closed won invalid).

## HubSpot → Firebase Sync Pattern
The reusable sync pattern for all contests:
1. Scheduled Claude Code task triggers on defined cadence
2. Claude queries HubSpot via MCP — pulls call counts, close counts, transfers per rep for the contest period
3. Applies scoring logic consistent with `context\metric-definitions.md`
4. Writes scores to Firebase Realtime Database via REST API (`PATCH` to update, `PUT` to overwrite)
5. Firebase listeners push updates to all connected browsers in real-time

**Key parameters to configure per contest:**
- HubSpot date range filter (contest start/end)
- Rep roster and team assignments
- Firebase project URL and auth token
- Scoring weights per metric

**Reference implementation:** `contests\march-madness-2026\` scheduled tasks in `scheduled-tasks\`

## Scheduled Tasks
List the scheduled-tasks\ folders that belong to this contest:
- scheduled-tasks\<contest>-hubspot-sync
- scheduled-tasks\<contest>-daily-recap

## Deployment Steps
1. Create Firebase project → copy firebaseConfig into index.html
2. Create GitHub repo → enable Pages on main branch
3. Update scheduled task to write to Firebase REST API instead of data.json
4. Confirm real-time sync across two browsers before launch

## Post-Contest Cleanup
- Set Firebase security rules to read-only
- Archive scheduled tasks (disable, do not delete)
- Add entry to active-projects.md completed log
- Update _template\ with any new patterns learned
