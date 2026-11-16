# Tenant isolation

Every agency lives in the same database and the same tables. A query that forgets the agency condition, or an ID changed in a URL, could expose one agency's designs to another. Being careful in REST views is not enough: work enters the system through many doors — WebSockets, Celery tasks, management commands, the demo. So isolation is enforced in layers, and each layer still holds if the one above it fails.

## Options considered

| Option | Verdict |
|---|---|
| A database per agency | Strongest isolation, but migrations, backups and cost multiply with every agency |
| A schema per agency | Migrations repeat per agency, and `search_path` is a setting, not a lock |
| **Shared tables with `workspace_id`** | **Chosen** |

For the platform owner and cross-agency jobs, two shortcuts were rejected: switching RLS off in code when needed, and an exception inside the policy itself ("…or if the request comes from the platform owner") — one wrong setting would open everything. Instead, a **third database role** was created that has no privileges at all on content tables.

## The layers

1. **Every tenant table carries `workspace_id` itself**, even when it could reach the agency through a parent row. The RLS policy is then the same single line on every table, and indexes start with `workspace_id`.
2. **Composite foreign keys.** Every foreign key from one tenant table to another is `(workspace_id, id)`, so a comment in agency A cannot point to a version in agency B even if the code is wrong. Django's ORM does not support composite foreign keys, so the model keeps a normal `ForeignKey` and the composite constraint is added next to it in the same migration. **A test walks every model** and fails, naming the field, if a cross-tenant foreign key is missing its composite constraint.
3. **The code filters explicitly.** The current agency comes from the server — a member's membership or a guest's session — never from the request body. Every queryset filters by it visibly in the view. An ID from another agency returns **404, not 403**, so the caller cannot even learn that it exists.
4. **Row-Level Security with `FORCE`**, under three roles:

   | Role | Owns tables? | Used for | Bypasses RLS? |
   |---|---|---|---|
   | owner | yes | migrations only | no (because of `FORCE`) |
   | app | no | every request, WebSocket and Celery task | **no** |
   | platform | no | the owner's dashboard and daily subscription jobs | yes — but it has **no privileges on content tables** |

5. **The agency is set per transaction.** Each request, task or connection sets the current agency inside its own transaction before touching tenant data; a row outside that agency is invisible to the database itself.

## How it is verified

- The cross-tenant constraint test above.
- Isolation tests: objects that belong to another agency answer 404.
- Mutation testing: removing an explicit filter must make a test fail. Where a mutation survives because RLS still blocks the row, that is recorded as the second layer doing its job.
