# Issue Detail (feat.issue-detail)

## Summary
Displays the full details of a single work item (issue or epic) including metadata, description, properties, and side panels. Supports loading by human-readable key (e.g., PRJ-123), responsive sidebar behavior, and context-aware redirection for intake items.

## Domain Context
Part of the Issues domain. Used throughout the workspace when a user opens a specific work item from any list/board/search. It is the canonical detail experience for issues.

## User Story
As a teammate, I want to open an issue by its key so that I can read its details, update fields, and take action without leaving the project context.

## Pages and Routes
- Page: `/[workspaceSlug]/browse/[workItem]` (e.g., `/acme/browse/PRJ-123`)
- Layout/Header: `apps/web/app/(all)/[workspaceSlug]/(projects)/browse/[workItem]/{layout.tsx, header.tsx}`

Key client calls
- `useIssueDetail().fetchIssueWithIdentifier(workspaceSlug, projectIdentifier, sequence_id)`
- `useWorkItemProperties(projectId, workspaceSlug, issueId, EIssueServiceType)`

## User Interactions
1. Navigate to a work item URL (e.g., clicking PRJ-123 in a list, or pasting the URL).
2. The app fetches issue data and related properties; a loader is shown until ready.
3. On smaller screens, the detail sidebar auto-collapses; it expands on wider screens.
4. If the item is marked as intake, the user is redirected to the Intake view for that project.
5. Otherwise, the Issue Detail view renders with tabs/panels for properties and content.
6. On fetch error, an Empty State appears with a CTA to view all issues.

## Components and State
- UI: `IssueDetailRoot`, `EmptyState`, `Loader`
- Layout/Auth: `ProjectAuthWrapper`
- Hooks/Stores: `useIssueDetail`, `useProject`, `useAppTheme` (for `toggleIssueDetailSidebar`), `useWorkItemProperties`
- Types: `EIssueServiceType`
- i18n/theme: `useTranslation`, `useTheme`

## Outcomes
- Success: Issue details load and render; the page `<title>` reflects `PROJECT-SEQ Issue name`.
- Redirect: Intake items route to `/[workspaceSlug]/projects/{projectId}/intake/?currentTab=open&inboxIssueId={issueId}`.
- Failure: Empty State shown with a button to `/[workspaceSlug]/workspace-views/all-issues/`.

## Dependencies
- Projects domain (auth wrapper and project context)
- Views domain (navigation to workspace views)
- i18n and theming packages

## Product Value
- Provides a consistent, canonical surface to view and edit work items.
- Deep links (e.g., from chat or email) resolve directly to a rich detail experience.
- Optimized for responsiveness to improve readability on all screen sizes.

## Edge Cases / Review Notes
- Invalid or unknown keys → ensure helpful Empty State and recovery path.
- Permissions: user may lack access to the project or issue.
- Mobile sidebar toggling logic can conflict with manual user toggles—consider persisting preference.
- Large descriptions or many relations may increase load time; progressive loading is preferred.