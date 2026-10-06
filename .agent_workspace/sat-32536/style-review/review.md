# Documentation Review Report

**Source**: Ticket: SAT-32536
**Date**: 2026-10-06

## Summary

| Metric | Count |
|--------|-------|
| Files reviewed | 11 |
| Errors (must fix) | 0 (in scope) |
| Warnings (should fix) | 0 (in scope) |
| Suggestions (optional) | 3 (in scope) |

No source edits were applied. The content added or changed for SAT-32536 is clean and style-compliant. Every Vale error and warning reported falls on pre-existing lines that this ticket did not change, or is a false positive (literal file names, UI labels, attribute-inflated counts). Per the dispatch instructions, pre-existing issues are noted but not rewritten.

## Files Reviewed

### 1. guides/doc-Managing_Content/master.adoc

**Type**: ASSEMBLY (master)

Change: `ifdef::katello,orcharhino[]` -> `ifdef::katello,orcharhino,satellite[]` to include `assembly_managing-python-content-in-project.adoc` for Satellite. No prose change. No issues. Vale not run (build-level conditional only).

---

### 2. guides/doc-Managing_Hosts/master.adoc

**Type**: ASSEMBLY (master)

Change: `ifdef::katello,orcharhino[]` -> `ifdef::katello,orcharhino,satellite[]` to include `assembly_consuming-python-content-on-hosts.adoc` for Satellite. No prose change. No issues.

---

### 3. guides/common/modules/con_content-flow-in-project.adoc

**Type**: CONCEPT

Change: Added `PyPI,` to the Satellite branch of the content-sources list (bringing it in line with the non-Satellite branch, which already listed PyPI). Grammatically correct, correct list position. No in-scope issues.

Pre-existing (out of scope, not changed by this ticket):
- Line 21: `RedHat.Definitions` suggestion — `SCAP` not expanded on first use.
- Line 26 / 28: `RedHat.SimpleWords` suggestions — `multiple` -> `many`, `provide` -> `give/offer`.

---

### 4. guides/common/modules/con_download-policies-overview.adoc

**Type**: CONCEPT

Change: Added `Python` to the content-type lists in the abstract (both ifdef branches) and to the lazy-synchronization sentence. Oxford commas correct in both edits (`Deb, Yum, Python, and container image content`; `Deb, Yum, and Python repositories`). No in-scope issues.

Pre-existing / false positives (out of scope):
- Line 7: `foreman-documentation.AbstractLength` error (392 chars). **False positive / pre-existing** — Vale counts both `ifdef` branches plus attribute names as one string; the rendered per-build abstract is ~155 characters. The abstract was already long before this ticket; the Python additions only marginally lengthen it. Not an Asciidoctor build breaker.
- Line 19 / 39: `RedHat.TermsErrors` "Use 'on-demand' rather than 'On Demand'." **False positive** — `On Demand` is the product UI label for the download policy (used with bold `*On Demand*` elsewhere). Pre-existing labeled-list terms, not changed.
- Line 24 (pre-existing): subject/verb disagreement — "The *On Demand* policy acts as a _Lazy Synchronization_ feature because **they** save time". Consider "because **it** saves time" in a future update. Out of scope.
- Lines 22, 26, 37, 42, 49: `RedHat.PassiveVoice` suggestions on pre-existing sentences.

---

### 5. guides/common/modules/proc_synchronizing-python-repositories.adoc

**Type**: PROCEDURE

Change: Added (a) an explanatory lead-in after the *Includes* step, (b) an IMPORTANT admonition on the whole-of-PyPI synchronization caveat, and (c) a new Optional *Download Policy* step. Review of the added content:
- IMPORTANT admonition is correctly placed mid-procedure (module does not start with it), uses a valid admonition type, and is concise. Content is accurate and customer-focused.
- Added *Download Policy* step uses imperative form, a valid `xref:`, and correct UI bold. Good.
- Oxford comma and UI formatting correct throughout the additions.

No in-scope issues.

Pre-existing (out of scope — do NOT rewrite per dispatch instructions):
- Lines 11-53: `foreman-documentation.ProcedureStepLimit` error — the procedure exceeds 10 steps. This is a long-standing structure issue; the ticket adds one step, making an already-over-limit procedure one step longer. **[GLOBAL]** Splitting into sub-procedures is out of scope for this ticket; consider it in a future update.
- Line 4: `RedHat.NoGerundsInTitles` / `RedHat.Headings` suggestions — gerund title "Synchronizing Python repositories". This follows the established Foreman/Satellite procedure-title convention; changing it would break cross-references. Out of scope.
- Line 38 (pre-existing): "separated by comma" reads better as "separated by commas". Out of scope.

---

### 6. guides/common/modules/proc_viewing-available-python-packages.adoc

**Type**: PROCEDURE

Change: Collapsed a two-step navigation into a single bullet step reflecting the new UI path (*Content* > *Content Types* > *Python Packages*). Correctly uses an unnumbered bullet (`*`) for the single-step procedure, matching RH SSG guidance. Good change.

Pre-existing (out of scope):
- Line 4: gerund title suggestion (project convention — leave).
- Line 10: `RedHat.PassiveVoice` on the prerequisite — acceptable per RH SSG (prerequisites may use passive/completed-state phrasing).

---

### 7. guides/common/modules/snip_content-types-export.adoc

**Type**: SNIPPET

Change: Added `Python content` under an `ifdef::katello,orcharhino,satellite[]` guard, in correct alphabetical position (between Kickstart and Yum). No issues.

---

### 8. guides/common/modules/snip_content-types-syncable-export.adoc

**Type**: SNIPPET

Change: Added `Python content` to the "cannot export in the syncable format" lists in both ifdef branches, with correct Oxford commas. No in-scope issues.

Pre-existing / false positive:
- Line 3: `RedHat.Spelling` warning on "syncable". **False positive** — established product/engineering term (ISS syncable export format); pre-existing wording not changed by this ticket.

---

### 9. guides/common/modules/proc_installing-python-packages-on-a-host-from-project-server.adoc

**Type**: PROCEDURE

Change: Corrected the `pip --index-url` from the generic `/pulp/content/...` path to the correct `/pypi/...` pip index path. This is the key technical fix and is correct. No in-scope issues.

Pre-existing (out of scope):
- Line 7: `RedHat.Using` warning — "on hosts using `pip`" -> "by using `pip`". Clear and trivial, but on a pre-existing line; consider in a future update.
- Line 4: gerund title suggestion (project convention — leave).
- Line 13: `RedHat.RepeatedWords` "'to' is repeated" — **false positive** ("set the URL **to** {ProjectServer} **to** install" — two distinct uses). Pre-existing.

---

### 10. guides/common/modules/proc_downloading-files-to-a-host-from-a-python-repository-by-using-web-ui.adoc

**Type**: PROCEDURE

Change: Added a NOTE explaining that {Project} publishes Python repositories under the `/pypi/` path, so the copied *Published At* URL differs from the generic `/pulp/content/` example. Content is accurate, concise, and correctly placed as an attached NOTE on the "Copy the *Published At* URL" step. No in-scope issues.

Pre-existing / false positives (out of scope):
- Line 4: `foreman-documentation.HeadingWordCount` error (12 words). Pre-existing title; not changed by this ticket. The title uses `{ProjectWebUI}` which expands to "Satellite web UI". Out of scope.
- Line 14: `foreman-documentation.Capitalization` error "katello" -> "Katello". **False positive** — refers to the literal file name `katello-server-ca.crt` in monospace. Pre-existing line.
- Line 4: gerund title suggestion (project convention — leave).

---

### 11. guides/common/modules/proc_downloading-files-to-a-host-from-a-python-repository-by-using-cli.adoc

**Type**: PROCEDURE

Change: Added the same `/pypi/` path NOTE, attached to the "View the repository information" step. Accurate and concise. No in-scope issues.

Pre-existing / false positives (out of scope):
- Line 4: `foreman-documentation.HeadingWordCount` error (13 words). Pre-existing title. Out of scope.
- Line 14: `foreman-documentation.Capitalization` "katello" — **false positive**, literal file name `katello-server-ca.crt`.
- Line 4: `RedHat.Headings` / gerund suggestions on the pre-existing title (project convention — leave).

---

## Required Changes

None in scope. All Vale errors reported land on pre-existing, unchanged lines or are false positives (literal file names such as `katello-server-ca.crt`, the `On Demand` UI label, and the attribute/ifdef-inflated abstract-length count).

## Suggestions

1. **proc_installing-python-packages-on-a-host-from-project-server.adoc:7** — [SUGGESTION] Pre-existing line: "on hosts using `pip`" reads better as "on hosts by using `pip`" (RedHat.Using). Trivial, but outside the ticket's changed content — consider in a future update.
2. **con_download-policies-overview.adoc:24** — [SUGGESTION] Pre-existing subject/verb disagreement: "...feature because **they** save time" should be "...because **it** saves time". Out of scope for this ticket.
3. **proc_synchronizing-python-repositories.adoc** — [SUGGESTION][GLOBAL] This procedure already exceeds the 10-step limit (a long-standing issue). The SAT-32536 Download Policy step adds one more. Consider splitting into sub-procedures in a future update; out of scope here.

---

*Generated with [Claude Code](https://claude.com/claude-code)*
