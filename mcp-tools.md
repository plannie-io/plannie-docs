# MCP tool reference

Generated from a live `tools/list` call against `https://api.plannie.io/mcp`. 16 tools.

### `list_events`

List family calendar events in a date range (ISO 8601). Recurring events are expanded into instances. Use get_members first to map member names to ids.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `from` | `string` | **yes** | Range start, ISO 8601 datetime |
| `to` | `string` | **yes** | Range end, ISO 8601 datetime |
| `memberId` | `string` | no | Only events assigned to this member id |

### `create_event`

Create a family calendar event. Times are ISO 8601 with offset. memberIds come from get_members — call it first to resolve names like "Vera" to ids.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `title` | `string` | **yes** |  |
| `startsAt` | `string` | **yes** | ISO 8601 datetime with offset |
| `endsAt` | `string` | **yes** | ISO 8601 datetime with offset |
| `allDay` | `boolean` | no |  |
| `location` | `string` | no |  |
| `notes` | `string` | no |  |
| `recurrenceRule` | `string` | no | RRULE string, e.g. FREQ=WEEKLY;BYDAY=TH |
| `memberIds` | `array` | no | Member ids from get_members |

### `update_event`

Update an event. For recurring events set editScope: "this" (one instance, requires instanceStartsAt), "this_and_future", or "all".

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `eventId` | `string` | **yes** |  |
| `title` | `string` | no |  |
| `startsAt` | `string` | no |  |
| `endsAt` | `string` | no |  |
| `allDay` | `boolean` | no |  |
| `location` | `string` | no |  |
| `notes` | `string` | no |  |
| `memberIds` | `array` | no |  |
| `editScope` | `string` | no |  |
| `instanceStartsAt` | `string` | no | Required when editScope is "this" |

### `delete_event`

Delete an event. For recurring events set scope: "this" (requires instanceStartsAt), "this_and_future", or "all" (default).

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `eventId` | `string` | **yes** |  |
| `scope` | `string` | no |  |
| `instanceStartsAt` | `string` | no |  |

### `list_tasks`

List family tasks, optionally filtered by member, due date, or completion.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `memberId` | `string` | no | Member id from get_members |
| `dueDate` | `string` | no | YYYY-MM-DD |
| `completed` | `boolean` | no |  |

### `create_task`

Add a one-off to-do for a family member (not a recurring habit/chore — there is no MCP tool for those yet). memberId comes from get_members.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `label` | `string` | **yes** |  |
| `memberId` | `string` | **yes** | Member id from get_members |
| `dueDate` | `string` | no | YYYY-MM-DD |

### `complete_task`

Mark a task as completed now.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `taskId` | `string` | **yes** |  |

### `get_lists`

Get all family lists (shopping, packing, …) with their items.

| Name | Type | Required | Description |
| --- | --- | --- | --- |

### `add_list_item`

Add a task to a list. Get listId from get_lists. Always appended at the end of the list.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `listId` | `string` | **yes** |  |
| `label` | `string` | **yes** |  |

### `check_list_item`

Check off (complete) a task on a list.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `listId` | `string` | **yes** |  |
| `itemId` | `string` | **yes** |  |

### `get_members`

Get the family members (id, name, role, color). Call this first to resolve names like "Vera" into the member ids other tools need.

| Name | Type | Required | Description |
| --- | --- | --- | --- |

### `get_activity`

Recent household activity (what agents and family members did), newest first.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `limit` | `integer` | no |  |
| `cursor` | `string` | no | nextCursor from the previous page |

### `get_meal_plan`

Get the scheduled meals for the 7-day week starting at weekStart, including each meal's recipe (title, emoji, ingredients). Use list_recipes to see the full recipe library, or set_meal to schedule one.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `weekStart` | `string` | **yes** | YYYY-MM-DD — the first of the 7 days returned |

### `list_recipes`

List the household recipe library (id, name, emoji, ingredients). Use this to find a recipeId for set_meal, or to check whether a recipe already exists before creating a new one.

| Name | Type | Required | Description |
| --- | --- | --- | --- |

### `set_meal`

Schedule a recipe into a meal-plan cell (date + mealType). Provide an existing recipeId (from list_recipes/get_meal_plan) or a recipeName — if no recipe with that name exists yet (case-insensitive match), a bare recipe is created automatically, so this also works for a quick note like "leftovers". Breakfast/lunch/dinner cells hold up to two meals side by side: pass slot 1 for a second meal in the same cell (slot 0 is primary and the default); snack cells only ever use slot 0.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `date` | `string` | **yes** | YYYY-MM-DD |
| `mealType` | `string` | **yes** |  |
| `recipeId` | `string` | no | Existing recipe id. Omit to resolve by recipeName instead. |
| `recipeName` | `string` | no | Recipe name to find (case-insensitive) or auto-create if not found. Ignored when recipeId is given. |
| `slot` | `number` | no | Cell position: 0 (primary, default) or 1 (second meal in the same cell). Snack cells only use 0. |

### `remove_meal`

Unschedule a meal by id (from get_meal_plan).

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `mealId` | `string` | **yes** | ScheduledMeal id from get_meal_plan |
