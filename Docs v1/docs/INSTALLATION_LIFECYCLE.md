# Planned installation, recovery and factory reset

User requirement recorded on 2026-10-19 (decision 100): provide an easy guided
installation/setup method, a recovery flow, and an AdminCP factory reset. After
factory reset, website configuration returns to defaults and the website can be
installed again. These are planned capabilities; no installer, recovery shortcut
or reset endpoint is implemented or enabled by this note.

The intended lifecycle is: uninstalled → guided setup → installed website;
an authorized AdminCP factory reset returns the website to defaults and a guarded
uninstalled/setup state. Recovery handles failed/interrupted setup or lost website
administrator access through verified authority. An unavailable or corrupt database
must never be interpreted as an uninstalled website or permission to take it over.

Setup should guide operators through prerequisites, the game/account/website
connection topology, read-only compatibility checks, website storage ownership,
reviewed schema/initialization and creation of a separate website administrator.
Game/player credentials and native GM roles cannot grant installer or AdminCP
authority. Installation state and all website configuration remain authoritative
in website-owned MSSQL records. Files are not a settings/install-state fallback;
connection/recovery secrets remain private deployment inputs. Default seeds are
reviewed explicit installation inputs, never automatic request-time repair.

Factory reset must restore the reviewed default website configuration, leave
native writes/payments/unimplemented publication gates disabled, invalidate the
appropriate website sessions and permit setup again without default passwords.
Define a secure setup/recovery authority that survives the reset and prevents
public installer takeover. AdminCP reset requires server-side permission, recent
administrator authentication, CSRF, throttling and a clear confirmation showing
the reset's scope and backup/recovery options. These checks do not authorize any
reset during current development merely because a SQL administrator is available.

The reset scope is website-owned configuration and installation state. It must
never reset MU game/account databases, player passwords, inventories, warehouses,
balances or server settings. In a unified database topology, ownership is determined
by explicit website table/module declarations; dropping a whole shared database
is forbidden. Physically separate premium modules and template packs retain
independent ownership and distribution boundaries.

Before implementation, define the retention/reset policy for website content,
administrator records, support tickets, templates, licenses, orders, markets and
escrow/payment history. Returning configurations to defaults does not implicitly
authorize erasing those records or abandoning pending transactions. A protected
immutable audit of the reset must survive the reset. Design website operation
locking, coherent lifecycle transitions, rollback/recovery after interruption,
module coordination and fixtures for both separate and unified database layouts.
Do not assume a single SQL transaction settles every topology or game-server
synchronization concern.

Public contracts, migrations, reset capability declarations, server authorization,
real storage integration and consumer/visual tests require their own implementation
review. Existing CLI imports and the isolated SQL test runner are not a working
installer or factory reset. STATE.md records the current implementation checkpoint.
