# Project Management — Requirements

## Document Information

| Field | Value |
|---|---|
| Product | Knitwise |
| Feature ID | FEATURE-001 |
| Feature Name | Project Management |
| Requirement Prefix | PM |
| Priority | P0 |
| Planned Release | V1 |
| Document Status | Draft |
| Version | 1.0 |
| Last Updated | 2026-07-29 |
| Product Owner | Product |
| Technical Owner | TBD |

---

# 1. Purpose

This document defines the functional requirements, non-functional requirements, business rules, validation rules, error-handling requirements, edge cases, and acceptance criteria for the Knitwise Project Management feature.

Project Management is the central domain of Knitwise.

A project represents a knitting, crochet, amigurumi, or related craft activity that a user wants to create, track, pause, complete, archive, or delete.

Other Knitwise features may attach their data to a project, including:

- Row Counter
- Multi-Part Tracking
- Pattern Library
- Custom Patterns
- Starter Patterns
- Yarn Inventory
- Hook and Needle Inventory
- Material Recommendations
- Notifications
- Statistics
- Local Persistence
- Cloud Sync

This document is the primary product-level source of truth for Project Management behavior.

Technical implementation details must be documented separately in:

- `implementation-notes.md`

---

# 2. Requirement Conventions

## 2.1 Requirement Types

| Prefix | Meaning |
|---|---|
| PM-FR | Functional Requirement |
| PM-NFR | Non-Functional Requirement |
| PM-BR | Business Rule |
| PM-VR | Validation Rule |
| PM-ER | Error Handling Requirement |
| PM-EC | Edge Case |
| PM-AC | Acceptance Criterion |

## 2.2 Requirement Priority

| Priority | Meaning |
|---|---|
| Must | Required for V1 release |
| Should | Important but may be deferred if release risk requires it |
| Could | Optional improvement |
| Won’t | Explicitly excluded from the current release |

## 2.3 Requirement Status

| Status | Meaning |
|---|---|
| Draft | Requirement is still being refined |
| Proposed | Requirement is ready for review |
| Approved | Requirement is approved for implementation |
| Implemented | Requirement is implemented |
| Verified | Requirement passed testing |
| Deferred | Requirement moved to a later release |
| Rejected | Requirement will not be implemented |

---

# 3. Scope Summary

## 3.1 Included in V1

The V1 Project Management feature includes:

- Creating a project
- Viewing a project list
- Viewing project details
- Editing a project
- Changing project status
- Pausing and resuming a project
- Completing a project
- Reopening a completed project
- Archiving a project
- Restoring an archived project
- Soft deleting a project
- Recovering a recently deleted project
- Searching projects
- Filtering projects
- Sorting projects
- Adding project notes
- Adding a project cover image
- Linking a pattern
- Linking yarn
- Linking hooks or needles
- Accessing counters from a project
- Accessing project parts
- Displaying project progress
- Supporting offline creation
- Supporting offline editing
- Applying free-plan active-project limits
- Preserving existing projects after subscription downgrade

## 3.2 Excluded from V1

The following capabilities are excluded from V1:

- Multi-user project collaboration
- Public project profiles
- Project comments
- Likes or social interactions
- Customer order management
- Product sales
- Cost and profit calculation
- Public project-sharing links
- Project marketplace
- Real-time collaborative editing
- Advanced project templates
- AI-generated project plans
- AI-generated progress summaries
- Desktop web project management
- Public community feeds

---

# 4. Project Entity Definition

A project is a user-owned record representing a knitting, crochet, amigurumi, or related craft activity.

Every project must contain:

- A unique identifier
- An owner identifier
- A project name
- A project status
- A creation timestamp
- A last-updated timestamp

All other project fields are optional unless another business rule explicitly makes them required.

---

# 5. Functional Requirements

## 5.1 Project Creation

### PM-FR-001 — Create Project

**Priority:** Must  
**Status:** Proposed

The system shall allow an authenticated or locally authorized user to create a project.

The project creation flow shall support at minimum:

- Project name
- Initial project status

The system shall automatically assign:

- Project ID
- Owner ID
- Created timestamp
- Updated timestamp

### PM-FR-002 — Quick Project Creation

**Priority:** Must  
**Status:** Proposed

The system shall allow a user to create a project by entering only a project name.

Optional fields shall not block project creation.

The quick-create flow shall allow a user to start working on a project with minimal interaction.

### PM-FR-003 — Detailed Project Creation

**Priority:** Must  
**Status:** Proposed

The system shall provide an optional detailed project creation flow.

The detailed flow may include:

- Project name
- Technique
- Category
- Description
- Cover image
- Start date
- Target completion date
- Pattern
- Yarn
- Hook or needle
- Initial project status

### PM-FR-004 — Generate Unique Project ID

**Priority:** Must  
**Status:** Proposed

Every project shall receive a globally unique identifier.

The identifier shall not depend on:

- Project name
- Project category
- Project creation order
- User-visible sequence numbers

The identifier must remain unchanged throughout the project lifecycle.

### PM-FR-005 — Save Project Locally First

**Priority:** Must  
**Status:** Proposed

The system shall persist the core project record locally before treating project creation as successful.

Remote synchronization shall not be required for the user to begin using the project.

### PM-FR-006 — Draft Project Support

**Priority:** Should  
**Status:** Proposed

The system shall support saving an incomplete project as a draft.

A draft project may contain only:

- Project ID
- Owner ID
- Project name or temporary recovery name
- Status
- Timestamps

Draft behavior shall prevent project data from being lost when the application closes unexpectedly.

### PM-FR-007 — Recover Incomplete Creation

**Priority:** Must  
**Status:** Proposed

When the project creation form contains unsaved changes, the system shall preserve a recoverable local draft where technically possible.

When the user returns, the system may offer:

- Continue editing
- Discard draft
- Save as draft

---

## 5.2 Project List

### PM-FR-008 — Display Project List

**Priority:** Must  
**Status:** Proposed

The system shall display projects owned by the current user.

The default project list shall include:

- Active projects
- Paused projects

Draft, completed, archived, and deleted projects shall not appear in the default list unless explicitly selected.

### PM-FR-009 — Project Card Information

**Priority:** Must  
**Status:** Proposed

Each project card shall display at minimum:

- Project name
- Project status
- Cover image or placeholder
- Last-updated information

When available, the card may also display:

- Technique
- Category
- Progress
- Pattern name
- Active counter summary

### PM-FR-010 — Project List Categories

**Priority:** Must  
**Status:** Proposed

The project list shall support the following views:

- Active
- Paused
- Draft
- Completed
- Archived
- All non-deleted projects

Deleted projects shall not appear in normal project-list views.

### PM-FR-011 — Empty Project State

**Priority:** Must  
**Status:** Proposed

When the user has no projects, the system shall display an empty state containing:

- A brief explanation
- A primary create-project action
- An optional starter-project suggestion
- No mandatory Premium prompt

### PM-FR-012 — List Refresh

**Priority:** Must  
**Status:** Proposed

The project list shall refresh when:

- A project is created
- A project is updated
- A project status changes
- A project is archived
- A project is restored
- A project is deleted
- Synchronization introduces remote changes

The user shall not be required to restart the application to see project changes.

---

## 5.3 Project Detail

### PM-FR-013 — Display Project Detail

**Priority:** Must  
**Status:** Proposed

The system shall provide a project-detail view.

The project-detail view shall display available project information, including:

- Project name
- Project status
- Description
- Technique
- Category
- Start date
- Target completion date
- Completion date
- Cover image
- Progress
- Linked pattern
- Linked yarn
- Linked tools
- Counters
- Parts

Missing optional information shall not prevent the project-detail view from opening.

### PM-FR-014 — Access Related Features

**Priority:** Must  
**Status:** Proposed

The user shall be able to access project-related features from the project-detail context.

Related feature entry points may include:

- Open pattern
- Open row counter
- Open multi-part tracking
- Open yarn assignments
- Open hook or needle assignments
- Open notes
- Open progress information

### PM-FR-015 — Project Ownership Check

**Priority:** Must  
**Status:** Proposed

Before displaying project data, the system shall confirm that the current user owns or is authorized to access the project.

Unauthorized project data shall not be displayed.

---

## 5.4 Project Editing

### PM-FR-016 — Edit Project

**Priority:** Must  
**Status:** Proposed

The system shall allow the project owner to edit supported project fields.

Editable fields may include:

- Project name
- Description
- Technique
- Category
- Cover image
- Start date
- Target completion date
- Completion date
- Pattern assignment
- Material assignments
- Tool assignments
- Manual progress

### PM-FR-017 — Save Project Changes

**Priority:** Must  
**Status:** Proposed

Valid project changes shall be persisted locally.

The `updatedAt` value shall change whenever a meaningful project field is modified.

Opening a project without changing data shall not update `updatedAt`.

### PM-FR-018 — Cancel Project Editing

**Priority:** Must  
**Status:** Proposed

The user shall be able to cancel project editing.

When changes have not been automatically saved, the system shall either:

- Discard changes after confirmation, or
- Preserve changes as a local draft

The selected behavior must be consistent across project forms.

### PM-FR-019 — Auto-Save Project Notes

**Priority:** Should  
**Status:** Proposed

Project notes should support automatic local saving.

The user shall receive a visible indication when:

- Changes are being saved
- Changes are saved
- Saving fails

---

## 5.5 Project Status Management

### PM-FR-020 — Supported Project Statuses

**Priority:** Must  
**Status:** Proposed

The system shall support the following project statuses:

```text
draft
active
paused
completed
archived
```

Deleted state shall be represented separately through soft-delete fields and shall not be treated as a normal project status.

### PM-FR-021 — Change Project Status

**Priority:** Must  
**Status:** Proposed

The user shall be able to change project status when the requested transition is permitted by business rules.

The system shall reject:

- Unsupported status values
- Invalid status transitions
- Unauthorized status changes

### PM-FR-022 — Pause Project

**Priority:** Must  
**Status:** Proposed

The user shall be able to change an active project to paused.

Pausing a project shall preserve:

- Counters
- Parts
- Notes
- Pattern assignment
- Material assignments
- Tool assignments
- Progress information

Pausing shall not create a completion date.

### PM-FR-023 — Resume Project

**Priority:** Must  
**Status:** Proposed

The user shall be able to change a paused project to active.

The project shall continue from its existing progress state.

### PM-FR-024 — Complete Project

**Priority:** Must  
**Status:** Proposed

The user shall be able to mark a project as completed.

When completed:

- Status shall become `completed`
- Completion date shall be populated
- The project shall leave the default active-project list
- Project data shall remain accessible
- Project data shall remain editable unless another approved rule restricts it

### PM-FR-025 — Reopen Completed Project

**Priority:** Should  
**Status:** Proposed

The user shall be able to reopen a completed project.

Reopening shall:

- Change the status to `active` or another eligible working status
- Preserve all project data
- Apply active-project entitlement limits
- Re-evaluate completion-date behavior

### PM-FR-026 — Archive Project

**Priority:** Must  
**Status:** Proposed

The user shall be able to archive a project without deleting it.

Archiving shall:

- Change project status to `archived`
- Record an archive timestamp
- Remove the project from default lists
- Preserve project-related data

### PM-FR-027 — Restore Archived Project

**Priority:** Must  
**Status:** Proposed

The user shall be able to restore an archived project.

The restoration flow shall either:

- Restore the previous working status, or
- Ask the user to choose an eligible destination status

Restoring to active status shall respect active-project limits.

---

## 5.6 Project Deletion

### PM-FR-028 — Soft Delete Project

**Priority:** Must  
**Status:** Proposed

Deleting a project shall initially perform a soft delete.

The system shall record:

- Deleted state
- Deleted timestamp
- User responsible for deletion, where applicable

The project shall be excluded from normal queries after deletion.

### PM-FR-029 — Delete Confirmation

**Priority:** Must  
**Status:** Proposed

Before deleting a project, the system shall display a clear confirmation message.

The confirmation shall distinguish between:

- Archive
- Delete

The delete action shall not be presented as equivalent to archiving.

### PM-FR-030 — Project Recovery

**Priority:** Should  
**Status:** Proposed

The system should support project recovery during a defined recovery period.

Recommended V1 recovery period:

```text
30 days
```

The final duration shall be approved before production release.

### PM-FR-031 — Permanent Deletion

**Priority:** Should  
**Status:** Proposed

After the recovery period, the system may permanently remove:

- Project record
- Project-specific relationship records
- Project media
- Project-only drafts
- Project-specific synchronization records

Permanent deletion shall respect:

- Legal requirements
- Data-retention requirements
- Backup-retention rules
- Account-deletion requirements

---

## 5.7 Search

### PM-FR-032 — Search by Project Name

**Priority:** Must  
**Status:** Proposed

The user shall be able to search projects by project name.

Search shall be:

- Case-insensitive
- Whitespace-tolerant
- Compatible with Turkish characters
- Restricted to projects accessible by the current user

### PM-FR-033 — Search Result State

**Priority:** Must  
**Status:** Proposed

When no project matches the search query, the system shall display a no-results state.

The no-results state shall not imply that the user has no projects.

### PM-FR-034 — Clear Search

**Priority:** Must  
**Status:** Proposed

The user shall be able to clear the search query and return to the unfiltered project list.

---

## 5.8 Filtering

### PM-FR-035 — Filter Projects

**Priority:** Should  
**Status:** Proposed

The system shall allow projects to be filtered by one or more supported criteria.

V1 filters may include:

- Status
- Technique
- Category
- Has linked pattern
- Has active counter
- Recently updated

### PM-FR-036 — Display Active Filters

**Priority:** Must  
**Status:** Proposed

The system shall visually indicate when filters are active.

The user shall be able to remove:

- A single filter
- All active filters

### PM-FR-037 — Empty Filter Result

**Priority:** Must  
**Status:** Proposed

When selected filters return no results, the system shall display an appropriate empty-result state.

The system shall provide an action to clear filters.

---

## 5.9 Sorting

### PM-FR-038 — Sort Projects

**Priority:** Should  
**Status:** Proposed

The system shall support project sorting by:

- Recently updated
- Oldest updated
- Newest created
- Oldest created
- Name ascending
- Name descending

Progress-based sorting may be included if progress-data quality is sufficient.

### PM-FR-039 — Default Sorting

**Priority:** Must  
**Status:** Proposed

The default project sort order shall be:

```text
updatedAt descending
```

The most recently updated project shall appear first.

### PM-FR-040 — Persist Sort Preference

**Priority:** Could  
**Status:** Proposed

The system may persist the user’s most recently selected project-sorting preference locally.

---

## 5.10 Project Notes

### PM-FR-041 — Add Project Notes

**Priority:** Must  
**Status:** Proposed

The user shall be able to add free-text notes to a project.

Project notes may contain:

- Measurements
- Pattern adjustments
- Stitch notes
- Yarn substitutions
- Reminders
- General observations

### PM-FR-042 — Edit Project Notes

**Priority:** Must  
**Status:** Proposed

The user shall be able to edit existing project notes.

### PM-FR-043 — Remove Project Notes

**Priority:** Must  
**Status:** Proposed

The user shall be able to clear project notes without deleting the project.

### PM-FR-044 — Notes Privacy

**Priority:** Must  
**Status:** Proposed

Project-note content shall not be sent to analytics systems.

Project-note content shall not be included in debug logs.

---

## 5.11 Project Cover Image

### PM-FR-045 — Add Cover Image

**Priority:** Should  
**Status:** Proposed

The user shall be able to assign a cover image to a project.

Supported sources may include:

- Photo library
- Camera
- Existing project image

### PM-FR-046 — Replace Cover Image

**Priority:** Must  
**Status:** Proposed

The user shall be able to replace a project cover image.

Replacing the image shall not remove or modify other project data.

### PM-FR-047 — Remove Cover Image

**Priority:** Must  
**Status:** Proposed

The user shall be able to remove the project cover image.

When no cover image is assigned, the system shall display a consistent placeholder.

### PM-FR-048 — Optimize Cover Image

**Priority:** Must  
**Status:** Proposed

The system shall resize or compress project cover images before remote upload.

The optimization process shall balance:

- Visual quality
- Storage usage
- Upload speed
- Mobile performance

Exact image specifications shall be documented in `implementation-notes.md`.

---

## 5.12 Pattern Relationship

### PM-FR-049 — Link Pattern

**Priority:** Must  
**Status:** Proposed

The user shall be able to link an accessible pattern to a project.

The linked pattern may originate from:

- Pattern Library
- Custom Patterns
- Starter Patterns

### PM-FR-050 — Replace Linked Pattern

**Priority:** Must  
**Status:** Proposed

The user shall be able to replace the pattern linked to a project.

Replacing the pattern shall not automatically delete:

- Counters
- Notes
- Parts
- Materials
- Tools

The user may be warned when related data may no longer match the replacement pattern.

### PM-FR-051 — Unlink Pattern

**Priority:** Must  
**Status:** Proposed

The user shall be able to unlink a pattern from a project.

Unlinking shall not delete the pattern record itself.

### PM-FR-052 — Missing Pattern Handling

**Priority:** Must  
**Status:** Proposed

If a linked pattern becomes unavailable or is deleted:

- The project shall remain accessible
- The project shall not be deleted
- The user shall be informed that the pattern is unavailable
- The user may remove or replace the broken relationship

---

## 5.13 Yarn Relationship

### PM-FR-053 — Link Yarn

**Priority:** Must  
**Status:** Proposed

The user shall be able to link one or more yarn records to a project.

### PM-FR-054 — Yarn Allocation

**Priority:** Should  
**Status:** Proposed

The user may specify a yarn quantity allocated to a project.

Supported units may include:

- Grams
- Meters
- Skeins

Unit behavior shall follow Yarn Inventory requirements.

### PM-FR-055 — Unlink Yarn

**Priority:** Must  
**Status:** Proposed

The user shall be able to remove a yarn relationship from a project.

Removing the relationship shall not delete the yarn-inventory record.

### PM-FR-056 — Deleted Yarn Handling

**Priority:** Must  
**Status:** Proposed

If a linked yarn record becomes unavailable:

- The project shall remain accessible
- The interface shall not crash
- The unavailable relationship shall be represented safely
- The user may replace or remove the relationship

---

## 5.14 Hook and Needle Relationship

### PM-FR-057 — Link Hook or Needle

**Priority:** Must  
**Status:** Proposed

The user shall be able to link one or more hook or needle records to a project.

### PM-FR-058 — Manual Tool Entry

**Priority:** Should  
**Status:** Proposed

The system may allow the user to enter a temporary tool value without first creating an inventory record.

Examples:

- 3.5 mm crochet hook
- 4 mm circular needle
- 80 cm cable

### PM-FR-059 — Unlink Tool

**Priority:** Must  
**Status:** Proposed

The user shall be able to unlink a tool from a project without deleting the related tool-inventory record.

---

## 5.15 Row Counter Relationship

### PM-FR-060 — Open Project Counters

**Priority:** Must  
**Status:** Proposed

The user shall be able to access row counters associated with a project.

### PM-FR-061 — Create Counter from Project

**Priority:** Must  
**Status:** Proposed

The user shall be able to create a row counter from within the project context.

The new counter shall automatically reference the project ID.

### PM-FR-062 — Multiple Counters

**Priority:** Should  
**Status:** Proposed

The system shall support multiple counters under a single project.

Examples:

- Main body counter
- Sleeve counter
- Pattern-repeat counter
- Increase counter
- Decrease counter

### PM-FR-063 — Missing Counter Handling

**Priority:** Must  
**Status:** Proposed

A missing or corrupted counter shall not prevent the project-detail screen from loading.

---

## 5.16 Multi-Part Relationship

### PM-FR-064 — Open Project Parts

**Priority:** Must  
**Status:** Proposed

The user shall be able to access parts associated with a project.

### PM-FR-065 — Create Part from Project

**Priority:** Must  
**Status:** Proposed

The user shall be able to add a part from within the project context.

The new part shall automatically reference the project ID.

### PM-FR-066 — Display Part Summary

**Priority:** Must  
**Status:** Proposed

The project-detail view shall display a summary of part completion when project parts exist.

Example:

```text
4 of 8 parts completed
```

---

## 5.17 Project Progress

### PM-FR-067 — Display Progress

**Priority:** Must  
**Status:** Proposed

The system shall display project progress when a valid progress source exists.

### PM-FR-068 — Progress Source Priority

**Priority:** Must  
**Status:** Proposed

When multiple progress sources exist, the system shall apply the following priority:

1. Multi-part completion
2. Target-based row-counter progress
3. Manual progress
4. No progress displayed

The application shall not silently average unrelated progress sources.

### PM-FR-069 — Explain Progress Source

**Priority:** Should  
**Status:** Proposed

The interface should make the selected progress source understandable.

Examples:

```text
6 of 10 parts completed
```

```text
Row 42 of 100
```

```text
Manual progress: 55%
```

### PM-FR-070 — No Progress State

**Priority:** Must  
**Status:** Proposed

When no valid progress source exists, the system shall not display a fabricated percentage.

The interface may display:

```text
Progress not set
```

---

## 5.18 Offline Operation

### PM-FR-071 — Offline Project Creation

**Priority:** Must  
**Status:** Proposed

The user shall be able to create a project without an active internet connection.

The project shall be stored locally.

### PM-FR-072 — Offline Project Editing

**Priority:** Must  
**Status:** Proposed

The user shall be able to edit locally available projects without an active internet connection.

### PM-FR-073 — Pending Sync State

**Priority:** Must  
**Status:** Proposed

Changes waiting for remote synchronization shall be marked with a pending-sync state.

The pending state may be shown to the user when relevant.

### PM-FR-074 — Retry Sync

**Priority:** Must  
**Status:** Proposed

The system shall retry eligible project-synchronization operations when connectivity returns.

Retry behavior shall prevent uncontrolled duplicate operations.

### PM-FR-075 — Preserve Local Changes

**Priority:** Must  
**Status:** Proposed

A remote-synchronization failure shall not silently remove valid local project changes.

---

## 5.19 Premium and Entitlement

### PM-FR-076 — Free Active-Project Limit

**Priority:** Must  
**Status:** Proposed

The free plan shall support a configurable maximum number of active projects.

Recommended initial V1 decision:

```text
Free active-project limit: 3
```

The final value shall be consistent with `premium-strategy.md`.

The value should be remotely configurable where technically possible.

### PM-FR-077 — Active-Project Count

**Priority:** Must  
**Status:** Proposed

For entitlement purposes, active-project count shall include projects with the following statuses:

- `active`
- `paused`

Recommended V1 draft rule:

```text
Draft projects do not count until activated.
```

Completed and archived projects shall not count toward the active-project limit.

### PM-FR-078 — Limit-Reached Experience

**Priority:** Must  
**Status:** Proposed

When the free active-project limit is reached, the user shall be offered:

- Archive an existing project
- Complete an existing project
- Upgrade to Premium
- Cancel project creation

The system shall not delete existing projects.

### PM-FR-079 — Downgrade Behavior

**Priority:** Must  
**Status:** Proposed

When a Premium user downgrades and has more projects than the free-plan limit:

- Existing projects shall remain accessible
- Existing projects shall not be deleted
- Existing projects shall not be hidden
- New project creation or activation may be restricted
- The user shall receive a clear explanation

### PM-FR-080 — Entitlement Failure

**Priority:** Must  
**Status:** Proposed

When entitlement cannot be verified because of a temporary error, the system shall avoid destructive or irreversible restrictions.

A cached entitlement may be used according to Premium feature rules.

---

# 6. Non-Functional Requirements

## 6.1 Performance

### PM-NFR-001 — Project List Load Time

The project list should become usable within two seconds under typical local-data conditions.

Typical conditions include:

- Up to 100 projects
- Up to 20 active projects
- Locally cached thumbnails
- Standard supported mobile hardware

### PM-NFR-002 — Project Detail Load Time

The project-detail view should display core project information within one second when data is locally available.

Related modules may load progressively.

### PM-NFR-003 — Save Responsiveness

A local project save should complete quickly enough that the application does not appear frozen.

Long-running remote operations shall not block local project use.

### PM-NFR-004 — Image Performance

Project thumbnails shall be loaded lazily.

Full-resolution images shall not be loaded into project-list cards.

---

## 6.2 Reliability

### PM-NFR-005 — No Silent Data Loss

The system shall not silently discard valid project changes.

When saving fails, the user shall receive a visible warning or recoverable state.

### PM-NFR-006 — Atomic Core Save

Core project creation shall be atomic.

The system shall avoid states where a project appears created but lacks mandatory identity fields.

### PM-NFR-007 — Partial Relationship Failure

Failure to save an optional relationship shall not automatically delete the core project.

Examples:

- Pattern link fails
- Yarn link fails
- Cover-image upload fails
- Tool link fails

The user shall be informed of the partial failure.

### PM-NFR-008 — Crash Recovery

Recoverable project-form data should survive:

- Application backgrounding
- Unexpected application termination
- Device interruption
- Temporary operating-system resource pressure

---

## 6.3 Scalability

### PM-NFR-009 — Project Volume

The local project model shall support at least 1,000 project records per user without requiring a schema redesign.

The interface may use:

- Pagination
- Lazy loading
- Local indexing
- Incremental data retrieval

### PM-NFR-010 — Relationship Volume

The project data model shall support multiple:

- Yarn relationships
- Tool relationships
- Counters
- Parts
- Photos

Related data shall not all be embedded directly into one unbounded project record.

---

## 6.4 Security

### PM-NFR-011 — User Isolation

Project data shall be scoped to the project owner.

A user shall not be able to access another user’s project by changing:

- Project ID
- API request
- Local route
- Query parameter
- Deep link

### PM-NFR-012 — Private Media

Project media shall be stored in private storage.

Access shall require valid authorization or time-limited access.

### PM-NFR-013 — Secure Deletion State

Soft-deleted project records shall not be returned by standard user-facing project queries.

---

## 6.5 Privacy

### PM-NFR-014 — Analytics Data Minimization

Analytics shall not include:

- Project names
- Project notes
- User-entered descriptions
- Raw image paths
- Private pattern text
- Yarn notes

### PM-NFR-015 — User Data Export

Project data shall be included in account-level data export where required.

### PM-NFR-016 — Account Deletion

Project records shall participate in the user account-deletion process.

---

## 6.6 Accessibility

### PM-NFR-017 — Screen-Reader Support

Project cards, forms, status controls, and actions shall expose meaningful screen-reader labels.

### PM-NFR-018 — Status Accessibility

Project status shall not be represented by color alone.

Status shall include text or another accessible indicator.

### PM-NFR-019 — Touch Targets

Interactive controls shall comply with supported-platform minimum touch-target guidance.

### PM-NFR-020 — Dynamic Text

Project screens shall support dynamic text without:

- Hiding critical actions
- Overlapping controls
- Truncating required information
- Preventing form completion

---

## 6.7 Localization

### PM-NFR-021 — Localizable Text

All user-facing Project Management text shall be localizable.

Hard-coded user-interface text shall not be used.

### PM-NFR-022 — Date Localization

Project dates shall be formatted according to the user’s locale.

Stored date values shall use a consistent system representation.

### PM-NFR-023 — Turkish Character Support

Search, sorting, validation, and text display shall correctly support Turkish characters.

Examples:

```text
ç, ğ, ı, İ, ö, ş, ü
```

---

## 6.8 Maintainability

### PM-NFR-024 — Stable Core Model

The core project entity shall remain independent of optional feature implementation details.

The project entity shall not require all counter, yarn, pattern, and part data to be embedded directly.

### PM-NFR-025 — Migration Support

Project data-model changes shall include a migration strategy.

Existing local projects shall not become inaccessible after application updates.

### PM-NFR-026 — Testability

Business logic shall be structured so it can be unit tested independently from the user interface.

---

# 7. Business Rules

### PM-BR-001 — Project Ownership

Every project must belong to exactly one user.

V1 does not support shared ownership.

### PM-BR-002 — Required Project Name

A project must have a non-empty project name before it can become:

- Active
- Paused
- Completed

A draft may temporarily use a system-generated placeholder only for recovery purposes.

### PM-BR-003 — Project Status Values

Project status must be one of:

```text
draft
active
paused
completed
archived
```

### PM-BR-004 — Deleted-State Separation

Deletion must not be represented only by setting the project status to `archived`.

Archived and deleted are separate concepts.

### PM-BR-005 — Default Project Status

The default status for quick project creation shall be:

```text
active
```

When draft creation is explicitly selected, the status shall be:

```text
draft
```

### PM-BR-006 — Active-Project Definition

For entitlement purposes, a project is considered active when its status is:

```text
active
paused
```

### PM-BR-007 — Completed-Project Limit

Completed projects shall not count toward the free active-project limit.

### PM-BR-008 — Archived-Project Limit

Archived projects shall not count toward the free active-project limit.

### PM-BR-009 — Draft Limit

Recommended V1 rule:

Draft projects shall not count toward the active-project limit.

The application may enforce a separate anti-abuse draft limit if necessary.

### PM-BR-010 — Completion Date

When a project becomes completed and no completion date is supplied, the system shall use the current user-local date.

### PM-BR-011 — Reopen Completion Date

When a completed project is reopened:

- The active completion date shall be cleared
- Historical completion information may remain in audit history

### PM-BR-012 — Archive Timestamp

When a project becomes archived, `archivedAt` shall be populated.

When the project is restored, the active `archivedAt` value shall be cleared.

### PM-BR-013 — Soft-Delete Timestamp

When a project is soft deleted, `deletedAt` shall be populated.

Restoring the project shall clear `deletedAt`, subject to recovery rules.

### PM-BR-014 — Pattern Optionality

A project does not require a linked pattern.

### PM-BR-015 — Yarn Optionality

A project does not require a linked yarn record.

### PM-BR-016 — Tool Optionality

A project does not require a linked hook or needle record.

### PM-BR-017 — Counter Optionality

A project does not require a row counter.

### PM-BR-018 — Parts Optionality

A project does not require multi-part tracking.

### PM-BR-019 — Pattern Cardinality

Recommended V1 rule:

A project may have only one primary pattern relationship.

Support for multiple primary patterns may be evaluated in a later release.

### PM-BR-020 — Multiple Yarn Records

A project may have multiple yarn relationships.

### PM-BR-021 — Multiple Tool Records

A project may have multiple hook or needle relationships.

### PM-BR-022 — Multiple Counters

A project may have multiple row counters.

### PM-BR-023 — Archive Preservation

Archiving a project must preserve:

- Core project data
- Notes
- Counters
- Parts
- Pattern relationship
- Materials
- Tools
- Progress
- Media

### PM-BR-024 — Delete Relationship Behavior

Soft deleting a project shall make its project-specific relationships unavailable to normal user flows.

Permanent relationship deletion may occur only during permanent project deletion.

### PM-BR-025 — Completed-Project Editing

Completed projects shall remain editable in V1.

The interface shall clearly indicate that the project is completed.

### PM-BR-026 — Project Restore and Limit

An archived or completed project cannot be restored to active status when the active-project limit is exceeded unless:

- The user has Premium entitlement, or
- Another active project is completed or archived

### PM-BR-027 — Offline Ownership

Projects created offline must receive an owner reference derived from the authenticated or locally authorized user session.

### PM-BR-028 — Local Save Precedence

The application shall treat successful local persistence as the first save milestone.

Remote synchronization state shall be tracked separately.

### PM-BR-029 — No Automatic Relationship Deletion

Changing or removing a project pattern shall not automatically delete:

- Row counters
- Project parts
- Notes
- Yarn relationships
- Tool relationships

### PM-BR-030 — No Automatic Inventory Deletion

Removing yarn or tool relationships from a project shall not delete the corresponding inventory records.

---

# 8. Validation Rules

### PM-VR-001 — Project Name Required

After trimming whitespace, a non-draft project name shall not be empty.

### PM-VR-002 — Project Name Length

Recommended project-name limits:

```text
Minimum: 1 character
Maximum: 120 characters
```

The final limits must be identical across:

- Mobile form
- Local database
- Remote database
- API validation

### PM-VR-003 — Project Description Length

Recommended project-description limit:

```text
Maximum: 2,000 characters
```

### PM-VR-004 — Project Notes Length

Recommended project-note limit:

```text
Maximum: 10,000 characters
```

Support for richer or longer notes may be introduced in a later release.

### PM-VR-005 — Trim Text

The system shall trim unnecessary leading and trailing whitespace from:

- Project name
- Custom technique value
- Custom category value

Project notes may preserve intentional formatting.

### PM-VR-006 — Start Date

Start date may be:

- In the past
- The current date
- In the future for planned projects

### PM-VR-007 — Target Date

Target completion date shall not be earlier than the project start date when both dates are present.

### PM-VR-008 — Completion Date

Completion date shall not be earlier than the project start date when both dates are present.

### PM-VR-009 — Manual Progress

Manual progress must be between:

```text
0 and 100
```

### PM-VR-010 — Counter Progress

A counter-based progress target must be greater than zero.

### PM-VR-011 — Part Quantity

Required part quantity must be a positive integer.

Completed part quantity must not be negative.

### PM-VR-012 — Image Type

Only supported image formats shall be accepted.

Recommended formats:

- JPEG
- PNG
- HEIC when platform conversion is supported
- WebP when platform compatibility is confirmed

### PM-VR-013 — Image Size

Images exceeding the configured source-size limit shall be rejected or compressed.

The exact threshold shall be defined in `implementation-notes.md`.

### PM-VR-014 — Relationship Ownership

A project may only link to records accessible by the same user.

This rule applies to:

- Pattern
- Yarn
- Hook
- Needle
- Counter
- Part

### PM-VR-015 — Archived-Project Editing

When archived-project editing is disabled by the interface, the project must first be restored.

Direct attempts to bypass this rule shall be rejected by domain validation.

---

# 9. Error-Handling Requirements

### PM-ER-001 — Project Save Failure

When project creation or editing fails, the system shall:

- Preserve user-entered values where possible
- Display an understandable error
- Offer retry when appropriate
- Avoid creating duplicate projects

### PM-ER-002 — Local Storage Failure

When local persistence fails, the system shall not indicate that the project was saved successfully.

The user shall be informed that the project could not be stored.

### PM-ER-003 — Remote Sync Failure

When remote synchronization fails:

- The local project shall remain usable
- The project shall be marked as pending or failed synchronization
- Retry shall be available automatically or manually

### PM-ER-004 — Cover-Image Upload Failure

When cover-image upload fails:

- The core project shall remain saved
- The local image may remain pending upload
- The user shall receive a non-destructive warning

### PM-ER-005 — Missing Project

When a requested project cannot be found, the system shall display a safe not-found state.

The interface shall not expose technical implementation details.

### PM-ER-006 — Unauthorized Project

When the current user cannot access a project:

- Project data shall not be shown
- The system shall display a generic access or not-found state
- Sensitive ownership information shall not be revealed

### PM-ER-007 — Broken Pattern Relationship

When a linked pattern is unavailable, the system shall allow the project to open and display a missing-pattern state.

### PM-ER-008 — Broken Inventory Relationship

When linked material or tool data is unavailable, the system shall not crash.

The user may remove or replace the unavailable relationship.

### PM-ER-009 — Entitlement Check Failure

When entitlement verification fails temporarily, the system shall use the safest non-destructive behavior.

Existing projects shall not become inaccessible solely because of a temporary entitlement-service failure.

### PM-ER-010 — Duplicate Submission

Repeated activation of the create action shall not create unintended duplicate projects.

---

# 10. Edge Cases

### PM-EC-001 — Whitespace-Only Project Name

Input:

```text
"     "
```

Expected behavior:

- Input is trimmed
- Validation fails
- A non-draft project is not created

### PM-EC-002 — Duplicate Project Names

The user creates two projects with the same name.

Expected behavior:

- Both projects are allowed
- Each project receives a unique project ID
- A duplicate-name warning is optional

### PM-EC-003 — Very Long Project Name

The user enters a project name longer than the supported maximum.

Expected behavior:

- Input is prevented or validation is displayed
- The name is not silently truncated during persistence

### PM-EC-004 — Application Closes During Creation

The application closes after the user enters project data but before final confirmation.

Expected behavior:

- A recoverable draft is preserved where possible
- No malformed project record is exposed

### PM-EC-005 — Application Closes During Save

The application terminates during local persistence.

Expected behavior:

- The local database remains consistent
- Partial mandatory records are not exposed
- The user can retry or recover

### PM-EC-006 — Project Deleted on Another Device

A project exists locally but was deleted remotely.

Expected behavior:

- The synchronization-conflict strategy is applied
- Local unsynchronized changes are not silently discarded
- The user may be informed that the remote project was deleted

### PM-EC-007 — Project Edited on Two Devices

The same project is edited on two devices while offline.

Expected behavior:

- A conflict is detected during synchronization
- One version is not silently overwritten without applying conflict rules

### PM-EC-008 — Active Limit Reached While Offline

The user creates projects offline while entitlement and remote project count are unavailable.

Expected behavior:

- Cached entitlement rules are applied
- The system avoids deleting data
- Project-limit reconciliation occurs during synchronization

### PM-EC-009 — Subscription Expires with Extra Projects

A Premium user has ten active projects and returns to the free plan.

Expected behavior:

- All ten projects remain visible
- All ten projects remain accessible
- New project creation or activation is restricted
- The user is informed how to return below the free limit

### PM-EC-010 — Archived Project Restored Above Limit

The user attempts to restore an archived project as active while the free limit is full.

Expected behavior:

- Restoration to active is blocked
- The user may archive or complete another active project
- A Premium upgrade may be offered
- Archived project data remains safe

### PM-EC-011 — Completed Project Reopened Above Limit

The user attempts to reopen a completed project while the active-project limit is full.

Expected behavior:

- Reopening to active is blocked
- Existing project data remains unchanged

### PM-EC-012 — Missing Cover-Image File

The project references a local image file that no longer exists.

Expected behavior:

- A placeholder is displayed
- The project remains usable
- The user may select a replacement image

### PM-EC-013 — Image Upload Completes After Project Deletion

The project is deleted while its cover image is uploading.

Expected behavior:

- The uploaded orphan file is removed or scheduled for cleanup
- The deleted project is not automatically restored

### PM-EC-014 — Linked Pattern Deleted

The linked custom pattern is deleted.

Expected behavior:

- The project remains available
- The pattern section displays an unavailable state
- The user can remove or replace the relationship

### PM-EC-015 — Linked Yarn Deleted

A linked yarn-inventory record is deleted.

Expected behavior:

- The project remains available
- Missing yarn is represented safely
- Historical relationship data may be retained where required

### PM-EC-016 — Progress Exceeds 100 Percent

A counter or part count implies more than 100 percent completion.

Expected behavior:

- Displayed percentage is safely capped or marked as inconsistent
- Raw values are preserved for correction
- Invalid data is not silently rewritten without an approved rule

### PM-EC-017 — Progress Target Is Zero

A target counter has a target value of zero.

Expected behavior:

- Percentage is not calculated
- Progress is treated as unavailable
- A validation warning is displayed

### PM-EC-018 — No Related Data

The project has no:

- Pattern
- Yarn
- Tool
- Counter
- Part

Expected behavior:

- The project remains valid
- Empty module states are displayed
- The detail screen remains usable

### PM-EC-019 — Archived Project Opened from Old Notification

The user opens a notification pointing to an archived project.

Expected behavior:

- The archived project detail may open
- Archived state is clearly displayed
- Invalid working actions are disabled or require project restoration

### PM-EC-020 — Deleted Project Opened from Deep Link

The user opens a stale deep link pointing to a deleted project.

Expected behavior:

- Normal project content is not displayed
- A safe not-found or recovery state is displayed

---

# 11. Acceptance Criteria

## 11.1 Project Creation

### PM-AC-001 — Quick Creation Success

**Given** the user is allowed to create an active project  
**And** the active-project limit has not been reached  
**When** the user enters a valid project name  
**And** confirms project creation  
**Then** the system creates exactly one project  
**And** assigns a unique project ID  
**And** saves the project locally  
**And** opens the project-detail view.

### PM-AC-002 — Empty Name Rejected

**Given** the user is creating a non-draft project  
**When** the project name is empty or contains only whitespace  
**Then** the system does not create an active project  
**And** displays a validation message.

### PM-AC-003 — Optional Fields Do Not Block Creation

**Given** the user has entered a valid project name  
**When** technique, category, cover image, pattern, and material fields are empty  
**Then** the project can still be created successfully.

### PM-AC-004 — Duplicate-Tap Protection

**Given** a project-create request is already processing  
**When** the user activates the create action repeatedly  
**Then** only one project record is created.

### PM-AC-005 — Offline Creation

**Given** the device has no internet connection  
**When** the user creates a valid project  
**Then** the project is stored locally  
**And** appears in the project list  
**And** is marked for later synchronization.

---

## 11.2 Project List

### PM-AC-006 — Default List

**Given** the user has active, paused, completed, and archived projects  
**When** the project list opens  
**Then** active and paused projects are shown by default  
**And** completed and archived projects are excluded.

### PM-AC-007 — Project Card Minimum Content

**Given** a project is shown in the project list  
**Then** its card displays:

- Project name
- Project status
- Cover image or placeholder
- Last-updated information

### PM-AC-008 — Empty State

**Given** the user has no projects  
**When** the project list opens  
**Then** an empty state is displayed  
**And** a create-project action is available.

---

## 11.3 Project Editing

### PM-AC-009 — Edit Success

**Given** the user owns the project  
**When** the user changes a valid editable field  
**And** saves the project  
**Then** the updated value is stored  
**And** `updatedAt` is refreshed.

### PM-AC-010 — Invalid Date Rejected

**Given** the project has a start date  
**When** the user enters a target completion date earlier than the start date  
**Then** the system rejects the value  
**And** displays a validation message.

### PM-AC-011 — Remote Failure Preserves Local Change

**Given** the local save succeeds  
**And** remote synchronization fails  
**Then** the local change remains available  
**And** the project is marked for synchronization retry.

---

## 11.4 Project Status

### PM-AC-012 — Pause Active Project

**Given** a project has status `active`  
**When** the user pauses the project  
**Then** its status becomes `paused`  
**And** progress data remains unchanged  
**And** no completion date is created.

### PM-AC-013 — Resume Paused Project

**Given** a project has status `paused`  
**When** the user resumes the project  
**Then** its status becomes `active`  
**And** existing progress remains available.

### PM-AC-014 — Complete Project

**Given** a project is not deleted  
**When** the user confirms project completion  
**Then** project status becomes `completed`  
**And** a valid completion date is stored  
**And** the project is removed from the default active-project list.

### PM-AC-015 — Archive Project

**Given** the user owns the project  
**When** the user confirms archive  
**Then** project status becomes `archived`  
**And** `archivedAt` is populated  
**And** the project no longer appears in the default list.

### PM-AC-016 — Restore Archived Project

**Given** a project has status `archived`  
**And** entitlement rules allow the destination status  
**When** the user restores the project  
**Then** the project is moved to the selected eligible status  
**And** project data remains intact.

---

## 11.5 Project Deletion

### PM-AC-017 — Delete Confirmation

**Given** the user selects delete  
**Then** the system clearly explains that delete differs from archive  
**And** requires explicit confirmation.

### PM-AC-018 — Soft Delete

**Given** the user confirms project deletion  
**When** deletion succeeds  
**Then** `deletedAt` is populated  
**And** the project disappears from normal lists  
**And** the project remains recoverable during the configured recovery period.

### PM-AC-019 — Deleted Project Access

**Given** a project is soft deleted  
**When** the user attempts to open it through a stale route  
**Then** normal project content is not displayed.

---

## 11.6 Search and Filtering

### PM-AC-020 — Case-Insensitive Search

**Given** a project named `Mavi Kazak` exists  
**When** the user searches for `mavi kazak`  
**Then** the project is returned.

### PM-AC-021 — Turkish Search Support

**Given** a project name contains Turkish characters  
**When** the user enters a matching query  
**Then** the project is returned correctly.

### PM-AC-022 — No Search Results

**Given** the search query matches no project  
**Then** the system displays a no-results state  
**And** provides a clear-search action.

### PM-AC-023 — Filter Results

**Given** projects with different statuses exist  
**When** the user filters by `completed`  
**Then** only completed projects are displayed.

### PM-AC-024 — Clear Filters

**Given** one or more filters are active  
**When** the user selects clear all  
**Then** all project filters are removed  
**And** the default project list is restored.

---

## 11.7 Relationships

### PM-AC-025 — Link Pattern

**Given** the user owns or can access both the project and pattern  
**When** the user selects the pattern  
**Then** the relationship is stored  
**And** the pattern appears in the project detail.

### PM-AC-026 — Unlink Pattern

**Given** a project has a linked pattern  
**When** the user removes the relationship  
**Then** the pattern is removed from the project  
**And** the pattern record itself remains available.

### PM-AC-027 — Link Yarn

**Given** the user owns a yarn-inventory record  
**When** the user links it to a project  
**Then** the relationship is stored  
**And** the yarn appears under project materials.

### PM-AC-028 — Unlink Yarn

**Given** yarn is linked to a project  
**When** the user removes the relationship  
**Then** the relationship is removed  
**And** the yarn-inventory record is preserved.

### PM-AC-029 — Create Counter from Project

**Given** the user is viewing a project  
**When** the user creates a row counter  
**Then** the counter is created with the correct project ID.

### PM-AC-030 — Create Part from Project

**Given** the user is viewing a project  
**When** the user creates a project part  
**Then** the part is associated with the correct project ID.

---

## 11.8 Progress

### PM-AC-031 — Multi-Part Progress Priority

**Given** a project has valid multi-part progress  
**And** also has a manual progress value  
**When** progress is displayed  
**Then** multi-part progress is used.

### PM-AC-032 — Counter Progress Priority

**Given** no valid multi-part progress exists  
**And** a valid target-based counter exists  
**When** progress is displayed  
**Then** counter progress is used.

### PM-AC-033 — Manual Progress Fallback

**Given** no valid multi-part or target-based counter progress exists  
**And** valid manual progress exists  
**When** progress is displayed  
**Then** manual progress is used.

### PM-AC-034 — No Fabricated Progress

**Given** no valid progress source exists  
**When** the project is displayed  
**Then** the system does not show a fabricated percentage.

---

## 11.9 Premium Limits

### PM-AC-035 — Free Limit Reached

**Given** a free user has reached the active-project limit  
**When** the user attempts to create another active project  
**Then** active-project creation is blocked  
**And** existing projects remain accessible  
**And** the user is offered archive, complete, or upgrade options.

### PM-AC-036 — Completed Projects Excluded from Limit

**Given** a free user has completed projects  
**When** active-project count is calculated  
**Then** completed projects are excluded.

### PM-AC-037 — Archived Projects Excluded from Limit

**Given** a free user has archived projects  
**When** active-project count is calculated  
**Then** archived projects are excluded.

### PM-AC-038 — Downgrade Preserves Projects

**Given** a Premium user has more active projects than the free limit  
**When** Premium entitlement expires  
**Then** existing projects remain visible and accessible  
**And** no project is deleted  
**And** new project creation or activation may be restricted.

---

## 11.10 Reliability and Security

### PM-AC-039 — Image Failure Does Not Delete Project

**Given** project creation succeeds  
**And** cover-image upload fails  
**Then** the project remains created  
**And** the user is informed of the image failure.

### PM-AC-040 — Missing Relationship Does Not Break Detail

**Given** a project references a missing optional relationship  
**When** the project-detail screen opens  
**Then** core project information is displayed  
**And** the missing relationship is represented safely.

### PM-AC-041 — Unauthorized Access Blocked

**Given** the current user does not own or have access to a project  
**When** the user attempts to access it  
**Then** project data is not displayed.

### PM-AC-042 — Local Changes Survive Restart

**Given** valid project changes are saved locally  
**When** the application restarts  
**Then** the saved changes remain available.

---

# 12. Requirement Traceability Summary

| User Need | Main Requirements |
|---|---|
| Create projects quickly | PM-FR-001 to PM-FR-007 |
| View and discover projects | PM-FR-008 to PM-FR-015 |
| Edit projects | PM-FR-016 to PM-FR-019 |
| Manage project lifecycle | PM-FR-020 to PM-FR-027 |
| Delete projects safely | PM-FR-028 to PM-FR-031 |
| Search, filter, and sort projects | PM-FR-032 to PM-FR-040 |
| Add notes and cover images | PM-FR-041 to PM-FR-048 |
| Link patterns and materials | PM-FR-049 to PM-FR-059 |
| Use counters and project parts | PM-FR-060 to PM-FR-066 |
| Track project progress | PM-FR-067 to PM-FR-070 |
| Work offline | PM-FR-071 to PM-FR-075 |
| Apply Premium limits | PM-FR-076 to PM-FR-080 |

---

# 13. Open Product Decisions

| ID | Decision | Recommendation | Status |
|---|---|---|---|
| PM-OD-001 | Free active-project limit | 3 projects | Open |
| PM-OD-002 | Do drafts count toward the free limit? | No | Open |
| PM-OD-003 | Soft-delete recovery duration | 30 days | Open |
| PM-OD-004 | Can archived projects be edited directly? | Restore first | Open |
| PM-OD-005 | Can one project have multiple primary patterns? | No in V1 | Open |
| PM-OD-006 | Is manual progress included in V1? | Yes | Open |
| PM-OD-007 | Is a project cover image included in V1? | Yes, optional | Open |
| PM-OD-008 | Are progress photos included in V1? | Defer to V1.x | Open |
| PM-OD-009 | Maximum project-note length | 10,000 characters | Open |
| PM-OD-010 | Is project recovery visible to users? | Yes | Open |

---

# 14. Dependencies

## 14.1 Product Dependencies

Project Management depends on or integrates with:

- `feature-002-row-counter`
- `feature-003-multi-part-tracking`
- `feature-004-pattern-library`
- `feature-005-custom-patterns`
- `feature-006-starter-patterns`
- `feature-007-yarn-inventory`
- `feature-008-hook-needle-inventory`
- `feature-014-premium`
- `feature-015-onboarding-authentication`
- `feature-017-local-persistence`
- `feature-018-cloud-sync`
- `feature-019-notifications`
- `feature-020-statistics`

## 14.2 Technical Dependencies

Required technical capabilities include:

- Authentication and user identity
- Local database or persistence layer
- User-scoped data access
- Private image storage
- Navigation and deep-link handling
- Analytics infrastructure
- Feature-entitlement service
- Error reporting
- Localization
- Data migration infrastructure

## 14.3 Design Dependencies

Required design components include:

- Project card
- Project list
- Project-detail screen
- Quick-create form
- Detailed create and edit form
- Status selector
- Empty state
- No-results state
- Error state
- Archive confirmation
- Delete confirmation
- Recovery screen
- Active-project-limit screen
- Premium-upgrade entry point
- Sync-status indicator

---

# 15. Definition of Ready

Project Management requirements are ready for implementation when:

- All Must requirements are approved
- Blocking open product decisions are resolved
- Project-status transitions are approved
- Active-project entitlement rules are approved
- Deletion and recovery rules are approved
- Project-to-pattern cardinality is approved
- The project data model supports all required relationships
- User stories and flows are documented in `flows.md`
- Security and privacy requirements are documented in `security.md`
- Analytics events are documented in `analytics.md`
- Test coverage is documented in `testing.md`
- Technical design is documented in `implementation-notes.md`
- Required UX screens and states are defined
- Product Owner approves the V1 scope

---

# 16. Definition of Done

Project Management is complete when:

- All Must functional requirements are implemented
- All Must acceptance criteria pass
- Required business rules are enforced
- Validation rules are implemented consistently
- Local persistence works
- Offline creation works
- Offline editing works
- Project ownership and access control are verified
- Premium limits work without data loss
- Project lifecycle transitions are tested
- Soft deletion is implemented
- Recovery behavior is implemented or explicitly deferred
- Missing optional relationships do not break project access
- Accessibility checks pass
- Localization checks pass
- Analytics validation passes
- Data migration tests pass
- No release-blocking defects remain
- Product Owner accepts the feature

---

# 17. References

- `overview.md`
- `flows.md`
- `data-model.md`
- `analytics.md`
- `security.md`
- `implementation-notes.md`
- `testing.md`
- `../../PROJECT_PRINCIPLES.md`
- `../../DECISIONS.md`
- `../../02-prd/overview.md`
- `../../02-prd/mvp-roadmap.md`
- `../../02-prd/feature-priorities.md`
- `../../02-prd/premium-strategy.md`
- `../../02-prd/release-plan.md`
- `../feature-002-row-counter/`
- `../feature-003-multi-part-tracking/`
- `../feature-004-pattern-library/`
- `../feature-005-custom-patterns/`
- `../feature-006-starter-patterns/`
- `../feature-007-yarn-inventory/`
- `../feature-008-hook-needle-inventory/`
- `../feature-014-premium/`
- `../feature-015-onboarding-authentication/`
- `../feature-017-local-persistence/`
- `../feature-018-cloud-sync/`
- `../feature-019-notifications/`
- `../feature-020-statistics/`
