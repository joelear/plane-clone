# Issues (dom.issues)

## Summary
Enables users to create, manage, and track tasks and project issues with rich text editing and organizational features.
Supports collaboration and clear progress tracking for team projects.

## Users
- Team members and project contributors: create and update tasks.
- Project managers: assign, prioritize, and monitor issue progress.

## Core Capabilities
- Creating and editing issues with rich-text and file uploads.
- Adding sub-properties and referencing related issues.
- Organizing issues within projects and modules.
- Tracking issue status and progress.

## Key Pages & Entry Points
- `/issues`, `/issue/[id]`
- API routes `/api/issues/*`

## Representative Code
- Frontend issue components (likely under `/apps/web` or relevant front-end folder).
- Backend API handlers for issues.

## Related Entities
`Issue`, `SubProperty`, `Project`, `Module`

## Dependencies
- Depends on User domain for permissions and assignments.
- May interact with Cycles and Modules domains.

## Product & Business Value
Critical for organizing and tracking project work; drives team productivity and clarity.

## Review Notes
Naming and domain boundaries might overlap with Modules or Cycles features — Needs Review.
