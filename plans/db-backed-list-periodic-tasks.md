# DB-backed list_periodic_tasks

## Goal

Make `list_periodic_tasks` report the live database state instead of the in-memory registry.

## Behavior

- By default, list only tasks managed by `django-beat-periodic`.
- A task is managed when its `PeriodicTask.description` matches `MANAGED_DESCRIPTION`.
- Add `--all` to include every `django-celery-beat` `PeriodicTask` row.
- When `--all` is used, label each task as `managed` or `unmanaged`.
- Show the live DB `enabled` state, not the decorator default.
- Show the live DB schedule relation where available.

## Operator Semantics

- `enable_periodic_task` and `disable_periodic_task` remain temporary DB toggles.
- On the next sync or process restart, synced tasks return to the decorator `enabled=` value.
- Developers should permanently change task state by updating the decorator.

## Implementation Steps

- Remove registry reads from `list_periodic_tasks`.
- Query `PeriodicTask` directly.
- Filter to managed tasks unless `--all` is provided.
- Add DB schedule formatting for interval, crontab, solar, and clocked schedules.
- Update tests to assert DB-only listing, managed filtering, and `--all` labeling.
