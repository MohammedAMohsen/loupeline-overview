# Architecture decision records (summary)

The full records are in the private design repository. Each one states the context, the options studied, the decision and its consequences.

| ADR | Decision | In short |
|---|---|---|
| 001 | **Shared tables, composite keys, RLS with three roles** | All agencies in one database; isolation in layers so that a code mistake still cannot cross agencies. See [tenant-isolation.md](tenant-isolation.md) |
| 002 | **UUIDv7 primary keys** | Every row that appears in a URL or the API gets a UUIDv7: long, unguessable, and time-ordered so it does not fragment indexes. Internal tables that need strict order (activity log, real-time events) use a sequence |
| 003 | **Guest identity: link, code, session** | A client reviewer is not a user but a record inside the agency. They enter with a personal link, confirm with a code sent to their email, and get a guest-only session that can never open a team endpoint — and the reverse — because the split is in the authentication layer itself |
| 004 | **Real time: Channels and WebSocket, writes through REST** | Changes are saved first, then broadcast after the commit. Broadcasting is only acceleration: after a disconnect the browser asks for everything after event N. Team and guest audiences are separate |
| 005 | **Direct upload, private storage, an untouched original** | Files go from the browser straight to storage through a signed URL, never through Django. The server verifies the real type and size, fingerprints the file and builds previews in a background task. The original is never modified |
| 006 | **The approval engine as a state machine** | Stages move only through defined states and events. Each stage runs one round per version, and decisions are stored on the round. Every transition happens in one transaction after locking the file's flow row |
| 007 | **Plans as data, subscription as a state machine, payment behind an interface** | Limits and features are database columns the platform owner edits. Code asks about features, not plan names. Payment is manual today, as the first provider behind one interface |
| 008 | **Where the team works, where the client reviews** | The team works on `app.loupeline.com`; clients review on the agency's own subdomain. Chosen partly because Google sign-in cannot work across unlimited subdomains |
| 009 | **New libraries and their licences** | Eight libraries added beyond the starter base, all licensed for commercial use. PyMuPDF was rejected on purpose because of its AGPL licence |
| 010 | **Demo isolation** | Visitors try the product without signing up. In every option, no email leaves the demo and no demo certificate can pass as a real one |
