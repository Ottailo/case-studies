# VibesMeet: keeping live events online when the mobile network is not

VibesMeet runs live event experiences for large in-person audiences. My work sat across the event platform: live messaging, staff verification, giveaway flows, identity checks, fraud controls, and the database work behind the attendee and admin dashboards.

## The constraint

A live event is a bad time to discover that your system assumes a stable network. A large crowd arrives in one place, connect to the same congested cell towers, and open the app at the same moment. When the network hiccups, they do not fail politely one at a time. They drop together, reconnect together, and turn a small outage into a traffic spike.

The second constraint was abuse. Event tickets and giveaways carry real value. A weak identity check or a predictable giveaway flow becomes a way to manipulate the event, not just a bug.

## The decision

I treated reconnects as a scheduled load problem, not an error path.

- The client reconnected with randomized delays instead of a fixed retry interval, so the audience did not hit the server in one synchronized wave.
- Incoming messages were buffered in memory during short disconnects, so the database did not absorb the entire reconnect spike.
- Connection limits and backoff behavior kept the socket layer from accepting more work than it could drain.
- Identity checks and fraud controls were kept on the server, with audit trails and administrative review paths for high-risk actions.

The alternative was to keep adding capacity to the WebSocket tier. That would have hidden the reconnect pattern for one event and made the next one more expensive. The fix was to shape the traffic before it reached the expensive parts.

## The implementation

The platform used WebSockets for live messaging and event state, PostgreSQL for the relational data, and server-side services for KYC and fraud checks. The work included:

- WebSocket messaging for attendees and event staff.
- QR-code staff verification.
- Automated giveaway flows with server-side eligibility checks.
- Biometric KYC and fraud detection services, including facial recognition pipelines and audit trails.
- PostgreSQL query optimization and production data migrations on high-traffic tables.
- Administrative dashboards for disputes, churn signals, and audit logs.

## The result

Events ran with a full venue connected at once, including periods when mobile networks were unstable. The reconnect behavior stopped being the thing that took the platform down. The KYC and fraud services gave the operations team a review path instead of an all-or-nothing block.

I am not publishing the exact attendance figure; the case study uses a qualitative description: a full venue connected at once.

## What I would change

I would instrument the reconnect wave from the beginning: reconnects per second, time to recover, message backlog, and database saturation. We had the behavior under control, but a better dashboard would have made the capacity conversation much shorter.

## What stays private

Client data, attendee identities, internal service names, production credentials, and the event platform's source code are not included here.