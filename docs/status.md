# Status and roadmap

_Last updated: November 2026_

## Built (backend)

- Accounts: registration, activation, JWT sign-in and refresh, logout with token blacklisting, password reset and change, email change, Google sign-in, account closing
- Agencies (workspaces): creation, settings, roles and permissions, invitations, members, suspension and restore when a plan shrinks
- Branding: logos (light and dark), colours, sender name, badge — applied per plan
- Billing: plans as data, trial, subscription life cycle, upgrades and downgrades, invoices, manual payment activation, the platform owner's dashboard
- Clients, guest reviewers and projects, with archiving and plan limits
- Files and versions: direct signed uploads, real content-type detection, fingerprinting, previews with colour profiles, comparison, downloads, deletion with storage clean-up
- Approval flows: stages, reviewers, due dates, templates, rounds per version, decisions, automatic stage progression, final approval and version locking
- Guest portal: personal links, one-time email codes, guest sessions, the guest's files, versions, decisions and bell
- Events and real time: catch-up after a disconnect, WebSocket connections for team and guests, presence, live bell
- Activity log and in-app notifications; emails in the recipient's language and the agency's identity
- English and Arabic throughout

## Next

- Pinned comments, replies and internal comments
- Approval certificate (PDF) and public verification page
- Reminders for late reviewers and phone (Web Push) notifications
- Platform dashboard filters and statistics
- Public demo
- The front end (React, TypeScript, Vite), then deployment and launch — **planned for early 2027**
