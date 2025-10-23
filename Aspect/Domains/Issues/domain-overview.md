# Issues (dom.issues)

## Summary
The Issues domain is focused on task management, enabling users to create, track, and organize issues or work items. It exists to facilitate team collaboration and productivity by providing a rich editor, sub-properties, and referencing capabilities.

## Users
- **End users/team members:** Create and update tasks, add details, comments and track progress.
- **Project managers:** Organize and prioritize issues to ensure timely delivery.

## Core Capabilities
- Task creation with rich text and file attachment support
- Manage sub-properties and relationships between issues
- Issue prioritization and status tracking

## Key Pages & Entry Points
- `/issues` (list and detail views)
- `/api/issues/` (RESTful endpoints for issue management)

## Representative Code
- `/apps/api/src/issues/` (API handlers and business logic)
- `/apps/web/src/components/IssueEditor/`

## Related Entities
`Issue`, `SubIssue`, `Comment`, `Attachment`

## Dependencies
- Dependent on the Cycles domain for sprint-like progress tracking
- Integrates with Analytics domain for reporting

## Product & Business Value
This domain drives user engagement and collaborative productivity, a core part of team workflows and project completion.

## Review Notes
Code scattered between API and UI components; consider unifying service logic. Needs Review on API route layout.