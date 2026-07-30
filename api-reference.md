# API reference (overview)

Base URL: `https://api.plannie.io/v1`. Every endpoint takes a `fam_live_…` API key as a
Bearer token. Full request/response schemas: [openapi.json](./openapi.json)
(fetched live from `https://api.plannie.io/openapi.json` — always in sync with the
deployed API), or browse them at `https://api.plannie.io/docs`.

## members

Read-only household member directory — use it to resolve the member ids referenced by events, tasks, chores and check-ins. Available to any valid key, no scope required.

| Endpoint | Summary |
| --- | --- |
| `GET /v1/households/me/members` | List household members |

## events

Calendar events on the family calendar, including recurring events. Scopes: `calendar.read` / `calendar.write`.

| Endpoint | Summary |
| --- | --- |
| `GET /v1/events/` | List calendar events |
| `POST /v1/events/` | Create a calendar event |
| `GET /v1/events/{id}` | Get an event |
| `PATCH /v1/events/{id}` | Update an event |
| `DELETE /v1/events/{id}` | Delete an event |

## tasks

One-off to-dos on the family task board. Scopes: `tasks.read` / `tasks.write`.

| Endpoint | Summary |
| --- | --- |
| `GET /v1/tasks/` | List tasks |
| `POST /v1/tasks/` | Create a task |
| `PATCH /v1/tasks/{id}` | Update or complete a task |
| `DELETE /v1/tasks/{id}` | Delete a task |

## chores

Recurring chores with per-member assignments and star values. Read-only through the API (`chores.read`); chores are managed and completed in the app.

| Endpoint | Summary |
| --- | --- |
| `GET /v1/chores/` | List chores |

## check-ins

Recurring check-in prompts family members answer (mood, gratitude, …). Scopes: `checkins.read` / `checkins.write`.

| Endpoint | Summary |
| --- | --- |
| `GET /v1/check-ins/` | List check-ins |
| `POST /v1/check-ins/` | Create a check-in |
| `PATCH /v1/check-ins/{id}` | Update a check-in |
| `DELETE /v1/check-ins/{id}` | Delete a check-in |

## lists

Lists (grocery, packing, notes) with sections and blocks — checklist items, headings and text. Scopes: `lists.read` / `lists.write`.

| Endpoint | Summary |
| --- | --- |
| `POST /v1/lists/sections/` | Create a list section |
| `PATCH /v1/lists/sections/reorder` | Reorder sections |
| `PATCH /v1/lists/sections/{id}` | Rename a section |
| `DELETE /v1/lists/sections/{id}` | Delete a section |
| `GET /v1/lists/pexels/search` | Search cover photos |
| `GET /v1/lists/` | Get all lists |
| `POST /v1/lists/` | Create a list |
| `PATCH /v1/lists/reorder` | Reorder lists in a section |
| `PATCH /v1/lists/{id}` | Rename a list |
| `DELETE /v1/lists/{id}` | Delete a list |
| `PATCH /v1/lists/{id}/settings` | Update list settings |
| `PATCH /v1/lists/{id}/move` | Move a list to another section |
| `POST /v1/lists/{id}/cover` | Upload a list cover image |
| `DELETE /v1/lists/{id}/cover` | Remove a list cover |
| `POST /v1/lists/{id}/cover/pexels` | Set a list cover from Pexels |
| `PATCH /v1/lists/{id}/cover/visibility` | Hide or show a list cover |
| `POST /v1/lists/{listId}/blocks/` | Add a block to a list |
| `PATCH /v1/lists/{listId}/blocks/{blockId}` | Update a block |
| `DELETE /v1/lists/{listId}/blocks/{blockId}` | Delete a block |
| `PATCH /v1/lists/{listId}/blocks/{blockId}/convert` | Convert a block to another type |
| `PATCH /v1/lists/{listId}/blocks/reorder` | Reorder blocks |
| `PATCH /v1/lists/{listId}/blocks/{blockId}/move` | Move a block to another list |

## activity

The household activity feed: an audit trail of changes with actor attribution (app member or API key). Scope: `activity.read`.

| Endpoint | Summary |
| --- | --- |
| `GET /v1/activity/` | List activity |

## recipes

The household recipe box that meal planning draws from. Scopes: `meals.read` / `meals.write`.

| Endpoint | Summary |
| --- | --- |
| `GET /v1/recipes/` | List recipes |
| `POST /v1/recipes/` | Create a recipe |
| `PATCH /v1/recipes/{id}` | Update a recipe |
| `DELETE /v1/recipes/{id}` | Delete a recipe |

## meals

The weekly meal plan: breakfast, lunch, dinner and snack slots per day. Scopes: `meals.read` / `meals.write`.

| Endpoint | Summary |
| --- | --- |
| `POST /v1/meals/grocery` | Generate a grocery list from the meal plan |
| `GET /v1/meals/` | Get the meal plan for a week |
| `POST /v1/meals/` | Schedule a meal |
| `PATCH /v1/meals/{id}` | Move a scheduled meal |
| `DELETE /v1/meals/{id}` | Remove a meal from the plan |
