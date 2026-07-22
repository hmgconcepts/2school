# Database installation

Run **`complete-schema.sql`** once in the Supabase SQL Editor. It is the single,
self-contained, dependency-ordered, idempotent v12 schema for a **fresh** deployment —
and it also **repairs** older School Connect databases (missing tables, columns, unique
keys, policies) without touching your data.

Files:
- `complete-schema.sql` — **run this** (v12 clean; ends with PostgREST cache reload).
- `complete-schema-v12-clean.sql` — identical named copy of the v12 file.
- `complete-schema-v11-LEGACY-MERGED.sql` — historical reference only; do NOT run on new projects.
- `*.csv` — import templates & sample question banks.

## v12.1 patch — 42703 "column student_id does not exist" (2026-07-22)
If you saw `ERROR: 42703: column "student_id" does not exist` while running
`complete-schema.sql` on an EXISTING database, your database was built by an
older schema generation whose tables (e.g. `support_plans`, `certificates`,
`lms_submissions`, `idcards`, `results.teacher_id`, `poll_votes.candidate_id`,
`profiles.role/status`, …) pre-date the hardening added in v12. RLS policies
validate their column references at creation time, so the run aborted.
v12.1 extends the drift-hardening block: every column referenced by any
policy / view / function / constraint / index is now force-added
(`ADD COLUMN IF NOT EXISTS`) before first use. Just re-run the updated
`complete-schema.sql` — it is idempotent and purely additive; nothing is
dropped from your database.
