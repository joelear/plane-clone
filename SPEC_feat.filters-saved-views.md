# Feature Specification — Filters & Saved Views (feat.filters-saved-views)

Domain/Page: dom.issues, page.issue-list; page.project-views  
Purpose: Let users filter issues and persist/share those filters as named views.
Scope: In — issue filtering UI, saving/updating/deleting views, applying views, sharing visibility; Out — cross-workspace global views, backend schema changes.

Actors & Permissions
- Roles: workspace member (viewer, contributor, manager), guest (read-only), project admin.
- Auth prerequisites: authenticated session; access to workspace and project.

Preconditions & Triggers
- Preconditions: user can access /[workspaceSlug]/projects/(detail)/[projectId]/issues and /views.
- Triggers: user opens list page; interacts with filter bar; clicks Save View; selects a saved view from sidebar.

User Flows
Primary Flow — Create and apply a saved view
1) User adjusts filters (assignees, labels, status, priority, cycle, module, dates, text query).
2) System queries issues list with current filter params; list updates.
3) User clicks Save View; enters name; visibility defaults to Project; clicks Save.
4) System creates view; adds to Project Views list; applies the view; URL reflects viewId.

Alternate Flows
A1) Update existing saved view — User modifies filters while a view is active; clicks Update View; system saves new params.
A2) Save as new — With a view active, user chooses Save As…; enters new name; system creates a new view without changing original.
A3) Delete view — From views list, user selects Delete; confirm; system removes and falls back to default All Issues.
A4) Share visibility — Owner changes visibility (Private -> Project); system updates view and revalidates access for others.
A5) Apply via URL — User opens URL with ?viewId=<id>; system loads saved filter params and applies automatically.

State Model
| State | Event | Next State | Notes |
|---|---|---|---|
| Idle | OpenList | Listing | No filters applied or defaults loaded |
| Listing | ChangeFilter | Listing | Debounced query refresh |
| Listing | SaveView | Saving | POST/PUT request |
| Saving | Success | ViewingSaved | viewId set; filters frozen to saved params |
| Saving | Fail(err) | Error | Inline error, remain on Listing |
| ViewingSaved | ChangeFilter | DirtySaved | Show “Update view” affordance |
| DirtySaved | UpdateView | Saving | PUT request |
| Any | ApplyView(viewId) | ViewingSaved | Load params; fetch issues |

Data Contracts
Request/Local state (filters)
- query (string, optional, <= 256)
- assignee_ids (string[], optional)
- label_ids (string[], optional)
- status_ids (string[], optional)
- priority (enum: urgent/high/medium/low/none, optional)
- cycle_ids (string[], optional)
- module_ids (string[], optional)
- start_date_from, start_date_to (ISO date, optional)
- due_date_from, due_date_to (ISO date, optional)
- sort_by (enum, default: updated_at)
- sort_order (asc|desc, default: desc)
- group_by (status|assignee|priority|none, default: status)
- layout (list|kanban|calendar|gantt, default: list)

Saved View entity (derived from project-view.store.ts usage)
- id (string)
- name (string, 1..60, required)
- project_id (string, required)
- visibility (private|project, default: project)
- filters (object; same as above)
- owner_id (string)
- created_at / updated_at (ISO)

Business Rules
- R1: Name unique per project for same owner when private; for project-visible views, unique per project.
- R2: Only owner or project admin can update/delete Private views; Project-visible views editable by owner or project admins.
- R3: Applying a view overrides current filters and layout.
- R4: Debounce filter changes by 300–500ms to avoid excessive queries.
- R5: URL must be the source of truth for applied viewId and major filter params to allow sharing/deep-linking.

UI/UX Notes
- Filter bar chips with multi-select dropdowns for Assignees, Labels, Status, Priority, Cycle, Module; date pickers; text search.
- Save View button shown when filters differ from default or current saved view (Dirty state). Tooltip: “Save current filters as a view”.
- Update View button when in DirtySaved state; Save As… in overflow menu.
- Views sidebar/list shows name, visibility icon, owner. Active view highlighted.
- Empty state: “No issues match these filters.” with Clear filters action.
- Keyboard: Tab-navigable chips and inputs; Esc closes dropdowns; Enter applies text search.

Error Handling
| Code/Key | User Message | Cause | Recovery |
|---|---|---|---|
| VIEW_NAME_REQUIRED | “Name is required.” | Empty name on save | Keep dialog open; focus name input |
| VIEW_NAME_TAKEN | “A view with this name already exists.” | Uniqueness rule | Suggest suffix; allow overwrite if owner opts |
| VIEW_SAVE_FAILED | “Couldn’t save view. Try again.” | Network/server error | Retry; keep state Dirty |
| VIEW_LOAD_FAILED | “Couldn’t load selected view.” | 404/403 on viewId | Fallback to All Issues; clear viewId from URL |
| PERMISSION_DENIED | “You don’t have permission to modify this view.” | Role check failed | Disable Update/Delete; allow Save As private |

Analytics & Telemetry
- Issue List Viewed — on list load; props: project_id, workspace_slug, layout, group_by
- Filter Changed — on each committed change; props: filter_keys_changed, counts
- Saved View Created — on success; props: view_id, visibility, layout
- Saved View Updated — on success; props: view_id
- Saved View Applied — when applied; props: view_id, source (sidebar|url|save-success)
- Saved View Deleted — on success; props: view_id

Performance & Loading
- Debounce filter queries 300–500ms; cancel in-flight requests on param change.
- Lazy-load views list on first expansion or page enter; cache in store for session.
- Show skeletons for list and shimmer for board/calendar/gantt while fetching.

Security & Privacy
- Saved view may embed assignee IDs and label IDs; no PII beyond IDs. Respect project membership when loading Project-visible views.
- Do not expose private view metadata to non-owners; 404 for unauthorized access.

Test Scenarios (Gherkin)
Happy Path — Create and apply project view
Given an authenticated member on the Issue List with filters cleared
When the user selects Assignee = self and Label = “frontend” and clicks Save View and enters name “My FE” and saves
Then the system creates a view and applies it and the URL contains viewId and the list shows only matching issues
And the event “Saved View Created” is fired with visibility=project

Alternate — Update existing view
Given an active saved view “My FE”
And the user adds Priority = High
When the user clicks Update View
Then the view is updated and remains active and issues refresh with new results
And “Saved View Updated” is fired

Alternate — Save As new from existing view
Given an active saved view “My FE”
When the user adds Status = In progress and chooses Save As… with name “My FE WIP”
Then a new view is created and applied and both views are visible in the list

Edge — Name uniqueness conflict
Given another project-visible view named “My FE” exists
When a user attempts to save a project-visible view named “My FE”
Then the dialog shows “A view with this name already exists.” and Save is disabled

Edge — Apply via URL with invalid viewId
Given the user navigates to ?viewId=deadbeef
Then an inline banner “Couldn’t load selected view.” appears and the app falls back to All Issues and removes viewId from URL

Failure — Network error on save
Given the user has Dirty filters
When the user clicks Save View and the network fails
Then a toast “Couldn’t save view. Try again.” appears and the dialog stays open with inputs intact

Failure — Permission denied on update
Given a Project-visible view owned by another user is active and the current user is a contributor
When the user clicks Update View
Then show “You don’t have permission to modify this view.” and disable Update; offer Save As private

Sample Test Data
| Case | Inputs | Expected |
|---|---|---|
| HP-1 | assignee=[me], labels=[frontend], name=“My FE” | created view, URL ?viewId=*, list filtered |
| ALT-1 | active view “My FE”, +priority=high | PUT success, list updates |
| ALT-2 | active view “My FE”, Save As “My FE WIP” | new view created, applied |
| EDGE-1 | name duplicate | validation error |
| EDGE-2 | viewId invalid | banner + fallback |
| FAIL-1 | save 500 | toast + remain dirty |
| FAIL-2 | update 403 | permission message + Save As option |

Representative Evidence (Context Only)
- docker-compose.yml references apps/web and apps/admin, implying Next.js web app with views management.
- Plane upstream features list in README includes “Views” with saved filters; this spec aligns with that capability.

Open Questions & Risks
- Needs Review: Exact routes and URL param names for views (apps/web routing not visible in this fork).
- Needs Review: Visibility and ownership rules—confirm with backend permissions matrix.
- Needs Review: Actual filter keys supported by current API and their validation constraints.
