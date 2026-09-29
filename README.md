# Contribution #1: Offer Tracker — Empty State for Filtered Results

**Student:** Nyla Crespo

**Project:** [Offer Tracker](https://github.com/shanker-codepath/offer-tracker)

**Chosen issue:** [#3 — Add an empty state for zero filter/search results](https://github.com/shanker-codepath/offer-tracker/issues/3)

**Fork:** [ncrespo5/offer-tracker](https://github.com/ncrespo5/offer-tracker)

**Status:** Phase III implementation complete; student code review and course submission pending.

## Project Selection Update

This contribution now focuses on Offer Tracker issue #3 instead of the previously listed DrumBeat crash-cymbal icon issue. The earlier README is preserved in this repository's Git history.

Another contributor has already opened [PR #28](https://github.com/shanker-codepath/offer-tracker/pull/28) for the same issue. This implementation is being completed separately for class; that PR belongs to another contributor and is not this submission. No maintainer approval or issue assignment is claimed.

## Why I Chose This Issue

This issue fits my interest in UI/UX design and frontend development. An empty table gives users no explanation or next step. A clear message and a way to reset filters make the application list easier to use.

## Understanding the Issue

### Problem Description

When a search or status filter produces no matching applications, `ApplicationTable` maps over an empty array and renders no table rows. Users see only the table headings.

### Expected Behavior

When filters are active and no results match, show “No applications match your filters” and a “Clear filters” link. Clearing filters should restore the list and reset the visible form controls.

### Affected Components

- `src/app/applications/page.tsx`: reads query parameters and passes filter state to the table.
- `src/components/applications/ApplicationTable.tsx`: displays the empty-state message and link.
- `src/components/applications/FilterBar.tsx`: keeps search, status, and sort controls aligned with navigation.

## Reproduction Process

### Environment Setup

Work started from upstream commit `ecbffd517ae79aaf607196b1019438dc568ff5f3`. Dependencies were installed with `npm ci`. Validation used Node.js 24.19.0, Prisma/SQLite, Vitest, and React Testing Library.

For the local preview, `.env.example` was copied to `.env`, an empty `prisma/dev.db` file was created before migration, and `DATABASE_URL=file:./dev.db` was explicitly supplied to `npm run dev` so the seed script could read it. The app seeded 18 sample applications.

### Steps to Reproduce

1. Open `/applications`.
2. Search for `no-such-company` and apply the filter.
3. Before the fix, the table contains its headings but no result message or recovery link.
4. After the fix, the empty-state message and “Clear filters” link appear.
5. Click “Clear filters”; the URL returns to `/applications`, the application rows return, and all filter controls reset.

### Reproduction Evidence

Seven regression cases were added before the production changes. Five failed against the original implementation: four empty-state scenarios and the visible-control reset scenario. The other two cases verified unaffected behavior.

## Solution Approach

**Understand:** Users need an explanation and a recovery action when their filters return no applications.

**Match:** Follow the existing table markup, Tailwind classes, Next.js links, and component-test patterns.

**Plan and implementation:**

1. Pass a `hasFilters` boolean from the applications page to the table.
2. Render an empty-state row spanning all six columns when the result array is empty and filters are active.
3. Link to `/applications` to remove the query parameters.
4. Key the uncontrolled search, status, and sort controls by their incoming values so navigation updates their displayed values.
5. Add regression tests and verify the real browser flow.

**Scope decisions:** Keep populated rows unchanged. Do not show a misleading “clear filters” action for an unfiltered empty database. Whitespace searches count as active filters because the existing data query treats them as search input. No database schema or dependency changes were made.

## Testing Strategy

### Automated Tests

New coverage in `tests/component/ApplicationsPage.test.tsx`:

- [x] Search with zero matches shows the message and reset link.
- [x] Status filter with zero matches shows the message and reset link.
- [x] Combined search and status filters show the empty state.
- [x] Whitespace-only search can be cleared.
- [x] Empty results without search/status filters do not show misleading filter messaging.
- [x] Matching applications retain their detail links and normal rows.
- [x] Search, status, and sort controls reset when query parameters are cleared.

### Validation Results — September 29, 2026

| Check | Result |
| --- | --- |
| `npm run lint` | Passed |
| `npm run typecheck` | Passed |
| Full Vitest suite | 21 tests passed across 5 files, using the local database workaround below |
| `git diff --check` | Passed |
| Browser: no-match search + Offer status + company sort | Message and reset link displayed |
| Browser: click Clear filters | All 18 sample rows returned; search blank, status All statuses, sort Date added (newest) |

### Local Test Setup Limitation

The unchanged upstream test setup deletes `prisma/test.db` before running migrations. On this machine, Prisma reported a schema-engine error when the database file did not exist. For validation only, `tests/globalSetup.ts` temporarily imported `writeFileSync` from `node:fs` and called `writeFileSync(testDbPath, "")` immediately before `execSync("npx prisma migrate deploy", ...)`. The full suite then passed. The original setup file was restored afterward and is not part of the contribution commit. An unmodified `npm test` still encounters this local setup limitation on this machine; the 21-test pass is explicitly qualified by that workaround.

## Implementation Notes

### September 29, 2026 Progress

Implemented the filtered empty state, query-reset navigation, and visible-control reset behavior. Added seven component/page regression cases, ran the project checks, and verified the recovery flow in a browser.

### Challenges Faced

- Uncontrolled inputs only use `defaultValue` on mount. Keys tied to the incoming filter values allow them to remount when navigation clears the query.
- The local Prisma test setup failed before test execution. Creating the empty database file before migration allowed validation without expanding the issue fix into test-infrastructure changes.
- Another contributor is working on the same issue. This log identifies that overlap and keeps their PR separate from this class implementation.

### Code Changes

- **Development branch:** [codex/issue-3-empty-state](https://github.com/ncrespo5/offer-tracker/tree/codex/issue-3-empty-state)
- **Implementation and regression tests:** [Commit 2bc0aca](https://github.com/ncrespo5/offer-tracker/commit/2bc0aca6eabdfd5e7db9336d8f9bf1bdcfd385e4)
- **Files changed:** the three components/page listed above and `tests/component/ApplicationsPage.test.tsx`.

## Pull Request

**PR link:** Not opened for this implementation yet.

**Planned description:** Add an explanatory empty state when search/status filters return no applications, provide a clear-filters link, and ensure the visible controls reset after navigation. Includes regression coverage.

**Maintainer feedback:** None received on this implementation.

**Next step:** Review and understand the changes before preparing the Phase IV upstream PR; acknowledge the existing duplicate work when submitting.

## Learnings & Reflections

Technical concepts demonstrated by this work include conditional table rendering, query-driven navigation, the difference between input defaults and current values, and regression tests that fail before a fix and pass afterward.

**Student reflection pending:** Review the code and add a personal note about the most useful lesson, the hardest part, and what to do differently next time. AI assisted with implementation, testing, and documentation; personal understanding should be confirmed before submitting.

## Phase III Submission Checklist

- [x] Working implementation available on the fork's development branch.
- [x] Implementation notes, code link, and testing evidence recorded.
- [x] Local test setup limitation documented.
- [ ] Review and understand the implementation and add personal reflections.
- [ ] Attach a screenshot of a Slack participation post made within the 7 days before submission.
- [ ] Submit this Contribution README in the course portal and indicate **“Phase III Complete.”**

## Resources Used

- [Offer Tracker issue #3](https://github.com/shanker-codepath/offer-tracker/issues/3)
- [Project contribution guidelines](https://github.com/shanker-codepath/offer-tracker/blob/main/CONTRIBUTING.md)
- Existing project component tests and the installed Next.js Link documentation.
