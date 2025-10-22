# Selected Domain

- Issues (dom.issues) — Create, browse, triage, and work on issues across projects.
  Why this domain: Largest surface of routes and UI under /app/(all)/[workspaceSlug]/(projects)/projects/(detail)/[projectId]/issues/*, multiple list/detail screens, and dense client state in core/store/issue/* (kanban, calendar, gantt, filters, details). Also appears across analytics, drafts, intake, and views.

## Pages

- Issue List (page.issue-list) — /[workspaceSlug]/(projects)/projects/(detail)/[projectId]/issues/(list) — Browse and filter issues in list/board views within a project.
- Issue Details (page.issue-details) — /[workspaceSlug]/(projects)/projects/(detail)/[projectId]/issues/(detail)/[issueId] — Deep view of a single issue with properties, comments, and activity.
- Project Intake (page.project-intake) — /[workspaceSlug]/(projects)/projects/(detail)/[projectId]/intake — Capture and triage incoming requests into issues.
- Project Drafts (page.project-drafts) — /[workspaceSlug]/(projects)/drafts — Manage draft issues before publishing to a project.
- Active Cycles (page.active-cycles) — /[workspaceSlug]/(projects)/active-cycles — See issues in current sprints/iterations.
- Analytics (page.analytics) — /[workspaceSlug]/(projects)/analytics/[tabId] — Analyze issue throughput, velocity, and trends.
- Project Views (page.project-views) — /[workspaceSlug]/(projects)/projects/(detail)/[projectId]/views — Saved filters and custom views over issues.
- Workspace Browse (page.workspace-browse) — /[workspaceSlug]/(projects)/browse/[workItem] — Explore issues and related work items across workspace.

## Features

- Create Issue (feat.create-issue) — Add a new issue with title, description, and attributes. (Pages: Issue List, Project Intake)
- Edit Properties (feat.edit-properties) — Modify status, priority, assignees, labels, estimates. (Pages: Issue Details, Issue List)
- Board View (feat.board-view) — Visualize issues in Kanban columns; drag-and-drop between states. (Pages: Issue List)
- Calendar View (feat.calendar-view) — View issues by due/start dates on a calendar. (Pages: Issue List)
- Gantt View (feat.gantt-view) — Timeline view for scheduled issues and dependencies. (Pages: Issue List)
- Filters & Saved Views (feat.filters-saved-views) — Apply filters and save reusable views. (Pages: Issue List, Project Views)
- Bulk Actions (feat.bulk-actions) — Select multiple issues to update or move in one action. (Pages: Issue List)
- Comments & Activity (feat.comments-activity) — Discuss and review changes on an issue. (Pages: Issue Details)
- Attachments (feat.attachments) — Upload and preview files on issues. (Pages: Issue Details)
- Sub-issues (feat.sub-issues) — Manage parent/child hierarchy for complex tasks. (Pages: Issue Details)
- Cycle Assignment (feat.cycle-assignment) — Add issues to cycles/iterations and track progress. (Pages: Issue List, Active Cycles)
- Estimation (feat.estimation) — Set and visualize estimates/points on issues. (Pages: Issue Details, Issue List)
- Draft to Issue (feat.draft-to-issue) — Convert drafts into active issues. (Pages: Project Drafts)
- Intake Triage (feat.intake-triage) — Review incoming requests and turn them into issues. (Pages: Project Intake)
- Analytics Dashboards (feat.analytics-dashboards) — Charts for issue counts, throughput, and velocity. (Pages: Analytics)
- Notifications (feat.notifications) — In-app alerts for issue changes and mentions. (Pages: Various project pages)

## Representative Evidence

- Routes: apps/web/app/(all)/[workspaceSlug]/(projects)/projects/(detail)/[projectId]/issues/(list|detail)/*, intake/, drafts/, views/, active-cycles/, analytics/.
- Client state: apps/web/core/store/issue/* (issue.store.ts, issue_kanban_view.store.ts, issue_calendar_view.store.ts, issue_gantt_view.store.ts, root.store.ts), plus related stores for cycles/modules/views.
- UI: Multiple page.tsx and layout.tsx files for list/detail, headers, and mobile headers under issues; extended sidebars and headers in project shells.
- Entities: Issue, Cycle, Module, View, Label, Member stores tie directly to UI flows.
