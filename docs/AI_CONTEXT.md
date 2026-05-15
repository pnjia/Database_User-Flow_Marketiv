# AI Context & Bootstrap

## Project Overview
Marketiv is a full-stack platform connecting UMKM (SMEs) with Content Creators. 

## Tech Stack
- **Frontend**: Next.js, React, TailwindCSS, Shadcn
- **Backend/BaaS**: Appwrite (Database, Auth, Storage, Functions)
- **Payments**: Midtrans

## Documentation Hierarchy & Source of Truth Rules
To prevent context conflicts, strictly adhere to these Source of Truth (SoT) rules:
1. **Database Schema**: `docs/01-system/database/` is the absolute SoT. NEVER trust schema definitions inside feature folders.
2. **UI/Styling**: `docs/01-system/design-system/` is the absolute SoT for styling. Use these components before inventing new ones.
3. **Core Architecture**: `docs/01-system/core-architecture/` is the SoT for modular Sitemaps, Data Flows, User Flows, and Wireframes. Always start from the `00-index.md` in each subfolder.
4. **Features**: `docs/02-features/<feature-name>/` is the SoT for business logic, UI flows, and edge cases for a specific feature.

## How to Read the Docs Before Coding
Before implementing any feature or fixing a bug, execute the following read order:
1. **Understand the Goal**: Read `docs/02-features/<feature-name>/01-product-spec.md`.
2. **Visualize the UI/Flow**: Read `docs/02-features/<feature-name>/02-ui-ux.md` and cross-reference `docs/01-system/design-system/06-components.md`. Also refer to `docs/01-system/core-architecture/04-user-flows/` and `docs/01-system/core-architecture/03-wireframes/` if needed.
3. **Plan the Logic**: Read `docs/02-features/<feature-name>/03-technical-design.md`. Consult `docs/01-system/core-architecture/02-data-flow/` for app-wide data mapping.
4. **Verify the Data Model**: Search and read the relevant collection files in `docs/01-system/database/collections/`.
5. **Acknowledge Rules**: Briefly review `docs/01-system/guidelines/technical-guidelines.md` for coding standards.

**CRITICAL AI INSTRUCTION**: NEVER duplicate database schemas or system-wide UI rules inside feature files. Reference them via file paths (e.g., "See `docs/01-system/database/collections/users.md`").
