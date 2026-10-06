# Documentation Modules: SAT-32536

**Ticket:** SAT-32536 — Enable synchronization and management of PyPI-type repositories (Satellite 6.21)
**Generated:** 2026-10-06
**Placement mode:** UPDATE-IN-PLACE
**Build framework:** Foreman/Satellite documentation (Asciidoctor + ccutil; `guides/` with per-build conditionals)
**Content root:** guides/ (shared modules in guides/common/modules/, assemblies in guides/common/)
**Task type:** UPDATE only — no new modules. All content already existed in `guides/common`; the work surfaces it for the Satellite build and reconciles shared lists, paths, and navigation.

## Files Written

| Path | Type | Description |
|------|------|-------------|
| /home/jafiala/Documents/foreman-documentation/guides/doc-Managing_Content/master.adoc | ASSEMBLY (master/nav) | Opened the build guard around the Managing Python content assembly from `ifdef::katello,orcharhino[]` to `ifdef::katello,orcharhino,satellite[]` so it renders for Satellite (REQ-001/002) |
| /home/jafiala/Documents/foreman-documentation/guides/doc-Managing_Hosts/master.adoc | ASSEMBLY (master/nav) | Opened the same guard around the Consuming Python content on hosts assembly to include satellite (REQ-001/002) |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/con_content-flow-in-project.adoc | CONCEPT | Added PyPI to the `ifdef::satellite[]` external content sources list (satellite branch previously omitted it) (REQ-001) |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/con_download-policies-overview.adoc | CONCEPT | Added Python to supported content types in both the satellite and non-satellite abstract branches, and added Python to the lazy-synchronization allowed-repository wording in both branches (REQ-003) |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/proc_synchronizing-python-repositories.adoc | PROCEDURE | Clarified Includes/Excludes semantics (empty Includes = all available minus Excludes), added an IMPORTANT whole-of-PyPI storage/time caveat, and added an optional Download Policy step noting Python defaults to On Demand with an xref to the download policies overview (REQ-002/003) |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/snip_content-types-export.adoc | SNIPPET | Added "Python content" to the default (importable) export format list, gated `ifdef::katello,orcharhino,satellite[]` to match the Python content-management guards (REQ-004) |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/snip_content-types-syncable-export.adoc | SNIPPET | Added Python to the "cannot export in the syncable format" exclusion lists in both the satellite and non-satellite branches (REQ-004; pending SME confirmation — see open question) |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/proc_installing-python-packages-on-a-host-from-project-server.adoc | PROCEDURE | Corrected the pip `--index-url` distribution path from `/pulp/content/` to `/pypi/` (REQ-005) |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/proc_viewing-available-python-packages.adoc | PROCEDURE | Replaced the two-step Other Content Types + Type menu navigation with a single step: Content > Content Types > Python Packages (REQ-006) |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/proc_downloading-files-to-a-host-from-a-python-repository-by-using-web-ui.adoc | PROCEDURE | Added a NOTE clarifying that Python repositories publish under `/pypi/` (the shared curl snippet shows a generic `/pulp/content/` path); shared snippet left unchanged (REQ-005) |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/proc_downloading-files-to-a-host-from-a-python-repository-by-using-cli.adoc | PROCEDURE | Added the same `/pypi/` clarification NOTE; shared snippet left unchanged (REQ-005) |

## Files verified, no change required

| Path | Reason |
|------|--------|
| /home/jafiala/Documents/foreman-documentation/guides/common/assembly_managing-python-content-in-project.adoc | Builds warning-free for satellite; no edits needed |
| /home/jafiala/Documents/foreman-documentation/guides/common/assembly_consuming-python-content-on-hosts.adoc | Builds warning-free for satellite; no edits needed |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/con_managing-python-content-in-project.adoc | Renders cleanly; already has an Additional resources xref to the consuming assembly. The new Download Policy xref lives in the sync procedure, so an extra concept xref was unnecessary |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/con_consuming-python-content-on-hosts.adoc | Renders cleanly; cross-reference macros resolve |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/proc_updating-the-download-policy-for-a-repository-by-using-web-ui.adoc | Content-type agnostic; no content types enumerated, applies to Python as-is |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/proc_updating-the-download-policy-for-a-repository-by-using-cli.adoc | Content-type agnostic; CLI example applies to Python repositories |
| /home/jafiala/Documents/foreman-documentation/guides/common/modules/snip_step-downloading-file-from-server.adoc | Intentionally NOT edited — shared with file repositories; the `/pypi/` clarification was placed in the two Python download procedures instead |

## Build verification

All affected builds compile with zero Asciidoctor errors/warnings (`-a attribute-missing=warn`, satellite build fails on any warning):

- doc-Managing_Content: satellite, katello, orcharhino, foreman-el — 0 warnings
- doc-Managing_Hosts: satellite, orcharhino — 0 warnings

Rendered-content spot checks (satellite build): Managing Python content assembly, the whole-of-PyPI caveat, the "Python repositories default to On Demand" step, PyPI in the content-flow concept, the Consuming Python assembly, and the corrected `/pypi/` index URL all render.

## Vale notes (all pre-existing or false positives — none introduced)

- `con_download-policies-overview.adoc` AbstractLength: 373 chars before edit, 392 after. Already over the 300-char limit before this work. False positive — Vale counts both `ifdef::satellite[]` and `ifndef::satellite[]` abstract branches concatenated; only one branch (~160 chars, compliant) renders in any single build.
- `con_download-policies-overview.adoc` "On Demand" TermsErrors (lines 19, 39): pre-existing; "On Demand" is the literal Satellite web UI label in the labeled-list terms I did not touch.
- `proc_synchronizing-python-repositories.adoc` ProcedureStepLimit: 22 hits before edit, 23 after. Pre-existing non-compliance in this established module; the plan explicitly required adding the Download Policy step. Refactoring into substeps is out of scope and would risk regressions in a shared module.
- Download-files procedures HeadingWordCount (12-13 words) and `katello-server-ca.crt` capitalization: pre-existing; I changed neither the headings nor the filename line.

## Open questions carried forward from the plan

1. Syncable export format: confirm with the ISS/export engineering owner (SAT-33274 / SAT-49247) that Python is NOT supported in the syncable export format before finalizing `snip_content-types-syncable-export.adoc`. The edit currently adds Python to the exclusion list per the plan's stated scope.
2. REQ-004 build conditional: the new `snip_content-types-export.adoc` entry is gated `ifdef::katello,orcharhino,satellite[]` to match the Python assembly guards. Confirm this matches intended behavior for foreman-el/foreman-deb export guides (Python management is currently not surfaced for those builds).
