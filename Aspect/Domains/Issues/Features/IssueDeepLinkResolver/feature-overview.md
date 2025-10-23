# Issue Deep Link Resolver (feat.issue-deep-link)

## Summary
Resolves id-based issue links to the canonical key-based URL and forwards users accordingly. Improves shareability and consistency of links across the app.

## Domain Context
Lives within the Issues domain as a helper flow for detail navigation. Triggered when a user lands on an id-based path (often from emails, integrations, or legacy bookmarks).

## User Story
As a user following a link from a notification, I want to land on the standard issue detail URL so that the page shows the correct header, breadcrumbs, and related actions.

## Pages and Routes
- Entry: `/[workspaceSlug]/projects/(detail)/[projectId]/issues/(detail)/[issueId]`
  - File: `apps/web/app/(all)/[workspaceSlug]/(projects)/projects/(detail)/[projectId]/issues/(detail)/[issueId]/page.tsx`
- Destination: `/[workspaceSlug]/browse/[project_identifier]-[sequence_id]`

Key client calls
- `IssueService.getIssueMetaFromURL(workspaceSlug, projectId, issueId)` → `{ project_identifier, sequence_id }`
- On success: `router.push(\`/${workspaceSlug}/browse/${project_identifier}-${sequence_id}\`)`

## User Interactions
1. User opens an id-based URL for an issue.
2. The app fetches minimal metadata for that id.
3. If found, the user is redirected to the canonical key-based route.
4. If not found, an Empty State is displayed with a recovery CTA to Workspace All Issues.

## Components and State
- Services: `IssueService.getIssueMetaFromURL`
- Router: `useAppRouter`
- UI: `LogoSpinner`, `EmptyState`
- i18n/theme: `useTranslation`, `useTheme`

## Outcomes
- Success: Seamless redirect to `/[workspaceSlug]/browse/[project_identifier]-[sequence_id]`.
- Failure: Error state with CTA to `/[workspaceSlug]/workspace-views/all-issues/`.

## Dependencies
- API endpoint for issue metadata by id
- Views domain (Workspace All Issues)
- Routing and i18n packages

## Product Value
- Ensures all issue links normalize to a single, user-friendly format.
- Reduces broken or confusing links from external systems.

## Edge Cases / Review Notes
- Missing or deleted issues should provide human-friendly guidance.
- Permission errors should avoid leaking identifiers; show generic error copy.
- Consider caching successful resolutions to reduce repeated network calls.