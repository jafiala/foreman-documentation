# Technical Review — SAT-32536: Surface Python (PyPI) content for the Satellite build (Iteration 2)

**Scope:** Re-review of the 11 changed `.adoc` files for SAT-32536 (Python/PyPI content enablement for Satellite 6.21 / Foreman 5.0), confirming that fixes applied after iteration 1 resolved the 2 significant and 3 minor issues without regressions.
**Doc types detected:** ASSEMBLY (2 masters), CONCEPT (2), PROCEDURE (5), SNIPPET (2)
**Reviewer lens applied:** Both (Developer lens for procedures/snippets; Architect lens for concepts, build conditionals, and assembly coherence)
**Overall technical confidence:** HIGH — Both significant issues from iteration 1 are resolved, the list-continuation minor issue is fixed, and no regressions were introduced. One minor usability gap (no sync verification step) and the standing SME-confirmation items remain; none are build-breaking or accuracy-breaking.

---

## Iteration 1 fix verification

### Significant issue 1 — Storage caveat vs On Demand default — RESOLVED
- **Location:** `proc_synchronizing-python-repositories.adoc`, IMPORTANT admonition (lines 29–35).
- **Verification:** The admonition was rewritten to separate the two cost dimensions cleanly:
  - "requires a long initial metadata synchronization regardless of the download policy that you select" (applies to all policies);
  - "With the *Immediate* download policy, {Project} also downloads and stores every package during synchronization, which requires terabyte-scale storage";
  - "With the default *On Demand* download policy, {Project} downloads only the metadata during synchronization and fetches packages on request, which defers rather than eliminates the package storage cost."
- This is now internally consistent and matches `con_download-policies-overview.adoc` (Immediate = metadata + packages; On Demand = metadata only, packages on request). It is also consistent with the Download Policy step (lines 42–43), which states Python repositories default to On Demand. The contradiction flagged in iteration 1 is gone.

### Significant issue 2 — `/pypi/` download-by-file NOTE implied prefix-only swap — RESOLVED
- **Location:** `...-by-using-web-ui.adoc` (NOTE, lines 26–30) and `...-by-using-cli.adoc` (NOTE, lines 36–40).
- **Verification:** Both NOTEs now explicitly instruct the reader to use the full copied/retrieved *Published At* URL as the download URL "instead of adapting the example path," rather than implying a `/pulp/content/` → `/pypi/` prefix substitution. This matches suggestion (a) from iteration 1 and removes the misleading guidance, while correctly leaving the shared `snip_step-downloading-file-from-server.adoc` untouched (still shows the generic `/pulp/content/…/_My_File_` path for file repositories). The exact per-file HTTP layout under `/pypi/` remains an SME item (below), but the prose no longer steers readers toward a broken URL.

### Minor issue 1 — Inconsistent list-continuation before the snippet include — RESOLVED
- **Verification:** Both procedures now place a `+` continuation before `include::snip_step-downloading-file-from-server.adoc[]` (Web UI line 31→32; CLI line 41→42). The two sibling procedures are now structurally consistent.

### Minor issue 2 — Sync procedure has no verification step — NOT ADDRESSED (still open, minor)
- **Location:** `proc_synchronizing-python-repositories.adoc` still ends at "From the *Select Action* menu, select *Sync Now*" (line 53).
- No verification pointer (e.g., an xref to *Viewing available Python packages* or a note on monitoring the sync task) was added. Carried forward as a minor issue below.

### Minor issue 3 — Download Policy step placement / field order — converted to SME item
- The Download Policy step (lines 42–44) remains between *Mirroring Policy* and *HTTP Proxy Policy*. Field order cannot be verified from source; tracked under SME verification (item 2).

---

## Regression check

- **Build conditionals unchanged and correct.** The two master guards remain `ifdef::katello,orcharhino,satellite[]` (verified via diff); the export snippets and content-flow concept branch use the same three-build model. No new conditionals were introduced by the fixes.
- **No new broken xrefs.** The rewrite of the IMPORTANT admonition and the Download Policy step did not alter the `xref:Download_Policies_Overview_{context}[]` / `xref:Mirroring_Policies_Overview_{context}[]` references, which iteration 1 confirmed resolve for all three builds.
- **Admonition rewrite introduced no new contradictions.** The "default On Demand" statement in the admonition (line 33) agrees with the Download Policy step (line 43) and with `con_download-policies-overview.adoc`.
- **pip `/pypi/…/simple/` index path** in `proc_installing-python-packages-on-a-host-from-project-server.adoc` is unchanged and still correct; the plan independently confirms Pulp Python distributions publish under `/pypi/<base_path>/`.

---

### Critical issues (must fix before publication)
None identified.

### Significant issues (should fix)
None identified. Both iteration-1 significant issues are resolved.

### Minor issues (consider fixing)

**1. Sync procedure still has no verification step**
- **Location:** `proc_synchronizing-python-repositories.adoc`, ends at line 53 (*Sync Now*).
- **Issue:** No way to confirm the sync succeeded or to monitor a potentially very long full-PyPI sync.
- **Impact:** A first-time Satellite user cannot tell whether the sync completed or how to inspect progress/failure.
- **Suggestion:** Add a verification pointer — e.g., an xref to `proc_viewing-available-python-packages.adoc`, or a note on monitoring the sync task from *Monitor* > *Tasks*.

### SME verification needed

1. **Syncable-export exclusion for Python.** `snip_content-types-syncable-export.adoc` lists Python as not exportable in the syncable format (both `satellite` and `ifndef::satellite` branches). The plan still flags this as an open question (plan "Open questions" #1; SAT-33274 / SAT-49247). Confirm with the ISS/export engineering owner before publication.
2. **Download Policy field presence, order, and new-repo default.** Confirm that the *Download Policy* field is present/selectable in the 6.21 New Repository form for a Python repo, that its on-screen position matches the step order (between *Mirroring Policy* and *HTTP Proxy Policy*), and that a newly created Python repository (not only migrated existing repos) defaults to On Demand.
3. **`/pypi/` per-file download URL layout.** The NOTEs now direct readers to the copied *Published At* URL, but the exact HTTP URL structure for downloading an individual package file from a Python repository under `/pypi/<base_path>/` is still unconfirmed from source. Confirm so the download-by-file procedures are reliably followable.
4. **Storage/time factual claim.** The rewritten admonition's prose is now internally consistent, but the underlying claims (Immediate incurs terabyte-scale package storage; On Demand is metadata-only at sync) should still be confirmed factually by an SME for full-PyPI scale.

### Strengths

- **Both significant iteration-1 issues were fixed precisely and minimally**, with no scope creep and no change to the build-safe surfacing work.
- **The admonition rewrite is a genuine improvement**, not just a patch: it now teaches the reader the metadata-vs-package cost distinction that the download-policies concept also makes, improving architectural coherence across the two modules.
- **Correct restraint maintained on the shared snippet** — the download-by-file fix stayed in the procedure NOTEs and left `snip_step-downloading-file-from-server.adoc` untouched for file-repository reuse.
- **The two download procedures are now structurally consistent**, reducing maintenance drift.

Severity counts: critical=0 significant=0 minor=1 sme=4
