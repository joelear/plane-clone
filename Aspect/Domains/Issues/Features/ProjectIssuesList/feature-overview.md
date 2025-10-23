# Project Issues List (feat.project-issues-list)

## Summary
Provides the project-scoped issues experience, where users browse, filter, and navigate issues within a specific project context.

## Domain Context
Within the Issues domain at a project level. Serves as the entry point to discover work in a project before drilling into Issue Detail.

## User Story
As a project member, I want to browse and filter issues in my project so that I can find the right work item to view or update.

## Pages and Routes
- Page: `/[workspaceSlug]/projects/(detail)/[projectId]/issues/(list)`
  - Files: `apps/web/app/(all)/[workspaceSlug]/(projects)/projects/(detail)/[projectId]/issues/(list)/{page.tsx, layout.tsx, header.tsx}`

Key client components/calls
- `ProjectLayoutRoot` (issues list/board root for a project)
- `useProject().getProjectById(projectId)` to derive title and context

## User Interactions
1. Open the project Issues route; the list/board loads in the project frame.
2. Adjust view controls (filters/sorts/grouping as provided by `ProjectLayoutRoot`).
3. Click any row/card to open Issue Detail in the same workspace context.
4. Use navigation to switch between project sections (e.g., workspace views, notifications) via project sidebar.

## Components and State
- UI Root: `ProjectLayoutRoot`
- Scaffolding: `PageHead`, project-level `sidebar.tsx`, `extended-project-sidebar.tsx`
- Hooks/Stores: `useProject`

## Outcomes
- Success: The list/board renders with project-aware controls; page title reflects project name + Issues.
- Failure: If the project cannot be resolved, page renders with minimal chrome or redirects as defined by root components.

## Dependencies
- Projects domain (navigation, sidebar, title, permissions)
- i18n (for page titles and labels)

## Product Value
- Centralizes all project work items with consistent controls, enabling discovery and triage.
- Serves as the primary springboard into Issue Detail and related flows.

## Edge Cases / Review Notes
- Empty projects should show an on-boarding/empty state (verify root implementation).
- Very large projects benefit from server-side pagination/virtualization—confirm behavior in `ProjectLayoutRoot`.
- Ensure route param changes (switching projects) fully reset local list state.