# Documentation Plan

**Project**: Enable synchronization and management of PyPI-type repositories (Satellite 6.21)
**Date**: 2026-10-06
**Ticket**: [SAT-32536](https://redhat.atlassian.net/browse/SAT-32536) — Epic (parent Feature: [SAT-35510](https://redhat.atlassian.net/browse/SAT-35510))
**Output format**: AsciiDoc (adoc)
**Release/Sprint**: 6.21.0

## What is the support status of the feature(s) being used to complete the user's JTBD (Job To Be Done)?

General Availability. In Satellite 6.21.0, Python (PyPI) becomes a first-class, default-enabled content type (the pulp-python plugin is packaged into Satellite/Capsule and enabled by the installer), matching Yum, Deb, and container content. The feature already ships as GA upstream in Foreman/Katello and orcharhino; this work surfaces it for the Satellite build.

## Why is this content important?

Python (PyPI) content is now a default-supported, first-class content type in Satellite and Capsule, but the comprehensive module and assembly content that already exists in `guides/common` is gated out of the Satellite build by `ifdef::katello,orcharhino[]` directives. Without these documentation changes, a GA feature would be invisible to Satellite users: they would have no documented path to synchronize PyPI repositories, upload or publish Python packages, set a download policy, export/import Python content between servers, or install packages on hosts with `pip`. Surfacing this content lets Satellite administrators and developers accomplish real jobs they can already perform in the product, and prevents broken guidance (for example, outdated `/pulp/content/` pip index URLs and stale web UI navigation).

## Who is the target persona(s)?

* **SysAdmin**: Primary persona for the content-management job. Synchronizes PyPI repositories, uploads local packages, sets download policies, publishes Python content through content views and Capsules, and moves content between servers with Inter-Server Sync. Owns the Satellite Server configuration.
* **Developer**: Primary persona for the content-consumption job. Consumes Python packages on hosts by pointing `pip` at the Satellite/Capsule content URL and downloads package files. Values accurate index URLs and working examples rather than owning the platform.

These are two distinct jobs for two distinct personas. The SysAdmin "manage Python content on the Server" job and the Developer "consume Python content on a host" job have different situations, motivations, and outcomes, and are therefore kept in separate assemblies (as they already are in the repository) with cross-references between them.

## What is the main JTBD? What user goal is being accomplished? What pain point is being avoided?

Two persona-differentiated job statements:

**JTBD 1 — SysAdmin (manage Python content):**
When I run Satellite 6.21 and my organization depends on Python (PyPI) packages, I want to synchronize, upload, govern, and distribute Python content from a single trusted source, so that I can provide developers with curated, air-gap-capable Python packages while avoiding uncontrolled direct access to public PyPI and the storage blowout of mirroring the entire index.

**JTBD 2 — Developer (consume Python content):**
When I need Python packages to build or run an application on a managed host, I want to install them with `pip` from my organization's Satellite/Capsule, so that I can reliably obtain the exact packages I need without reaching the public internet and without hitting broken index URLs.

## How does the JTBD(s) relate to the overall real-world workflow for the user?

These jobs sit inside the established Satellite content lifecycle, identical to how Yum, Deb, and container content already flow. The SysAdmin's job is an extension of the existing "manage content" workflow: define a Python repository, set an upstream URL and sync scope (Includes/Excludes), choose a download policy, synchronize, optionally upload local packages, publish through content views and lifecycle environments, mirror to Capsules, and move content between servers with Inter-Server Sync. The Developer's job is the downstream end of the same lifecycle: a host registered to Satellite points `pip` at the published Python repository and installs packages, or downloads files directly. The documentation must make Python a visible, equal member of this existing lifecycle rather than a special case — which is why the work is almost entirely "surface and reconcile existing content" rather than greenfield authoring.

## What high-level steps does the user need to take to accomplish the goal?

**SysAdmin (manage Python content):**
1. Create a Python-type repository and set the upstream URL (for example, `https://pypi.org`).
2. Scope the sync with the Includes and Excludes fields (empty Includes = all available packages minus Excludes); heed the whole-of-PyPI caveat (terabyte-scale disk, very long first sync).
3. Optionally set a Download Policy (Immediate or On Demand; Python defaults to On Demand).
4. Run Sync Now; optionally upload local packages via web UI or Hammer CLI.
5. View available packages on the dedicated Content > Content Types > Python Packages page.
6. Optionally export/import Python repositories between servers using the default (importable) format, including incremental exports.

Prerequisites: Satellite 6.21 with the pulp-python plugin enabled (default), an organization, and appropriate content/repository permissions.

**Developer (consume Python content):**
1. Ensure a Python repository is published and available to the host.
2. Run `pip install --index-url https://<satellite>/pypi/.../simple/ <package>` (add `--trusted-host` if the certificate is not trusted).
3. Optionally download package files directly from the repository via web UI or CLI.

Prerequisites: a host registered to Satellite/Capsule with access to the published Python repository; `pip` installed.

## Is there a demo available or can one be created?

No demo is referenced in the source tickets. The documented procedures are self-demonstrating; a short internal walkthrough (sync from pypi.org with a scoped Includes list, then `pip install` from the host) could be created if required for enablement.

## Are there special considerations for disconnected environments?

Yes. Inter-Server Sync (REQ-004, SAT-49247) is the primary disconnected/air-gapped path: Python repositories can be exported in the default (importable) format (including incremental exports) on a connected upstream Server and imported into downstream Servers. Note that Python content is NOT supported in the syncable export format — the documentation must state this explicitly. Additionally, the whole-of-PyPI sync caveat (terabyte-scale storage) is especially relevant for disconnected deployments that cannot lazily fetch on demand, reinforcing the advice to scope syncs with Includes.

## Who can provide information and answer questions?

The requirements analysis did not capture named PM/SME/UX contacts. Draw contacts from the parent tickets before publishing:
* Technical SME / feature owner: assignee and reporter of parent Feature [SAT-35510](https://redhat.atlassian.net/browse/SAT-35510) and Epic [SAT-32536](https://redhat.atlassian.net/browse/SAT-32536).
* Documentation SME: assignee of the DOC catch-all [SAT-33274](https://redhat.atlassian.net/browse/SAT-33274).
* Engineering contacts: authors of katello [PR #11842](https://github.com/Katello/katello/pull/11842) and [PR #11858](https://github.com/Katello/katello/pull/11858) (download policy).
* Open question owner for syncable-export support (SAT-33274 / SAT-49247 refinement): confirm with the ISS/export engineering owner on SAT-49247.

## Release Note needed?

Yes.

Draft release note: "Satellite 6.21 adds support for synchronizing and managing Python (PyPI) content. Python is now a first-class, default-enabled content type on Satellite Server and Capsule. You can synchronize Python repositories from upstream sources such as pypi.org, upload local packages, assign a download policy (Immediate or On Demand; Python repositories default to On Demand), publish Python content through content views and Capsules, export and import Python repositories between servers using Inter-Server Sync (default/importable format, including incremental exports), and install packages on hosts by pointing pip at Satellite. Synchronizing the entire PyPI index is supported but requires terabyte-scale storage and a long initial synchronization; limit the sync scope with the Includes field."

## Links to existing content

* [Upstream Foreman Python content docs](https://docs.theforeman.org/nightly/Managing_Content/index-katello.html#Managing_Python_Type_Content_content-management) — source of the content being surfaced for Satellite
* [KCS 4461511](https://access.redhat.com/solutions/4461511) — How to synchronize and manage PyPi-type repositories in Red Hat Satellite 6
* [KCS 3481621](https://access.redhat.com/solutions/3481621) — How to change download policy of repositories in Red Hat Satellite 6
* [Pulp Python — Sync from Remote Repositories](https://pulpproject.org/pulp_python/docs/user/guides/sync/) — Includes/Excludes and sync-policy behavior
* [Pulp Python — Set up your own PyPI](https://pulpproject.org/pulp_python/docs/user/guides/pypi/) — confirms distributions return `/pypi/<base_path>/`
* [How to self-host a Python package index using Pulp (Red Hat Developer)](https://developers.redhat.com/articles/2022/01/17/how-self-host-python-package-index-using-pulp)

---

## Doc impact assessment

| Requirement | Impact grade | Rationale |
|-------------|-------------|-----------|
| REQ-001 Python first-class / remove build guards | High | New user-facing workflow surfaced for Satellite; makes an entire content type visible |
| REQ-002 Synchronize and consume PyPI | High | Core new workflow for Satellite users (sync + consume) |
| REQ-003 Download policy for Python repos | Medium | New configuration option on an existing feature |
| REQ-004 ISS import/export for PyPI | Medium | Enhancement to existing export/import workflow; key for disconnected use |
| REQ-005 Corrected `/pypi/` distribution path | Low | Bug fix that corrects a documented command; prevents broken `pip install` |
| REQ-006 Dedicated Python Packages UI page | Low | Minor navigation change in existing procedure |

No None-impact items in scope. QE tickets (SAT-33270/71/73, SAT-36512/13) are correctly excluded — test-only, no user-facing doc change.

## Relationship analysis

| Pair | Relationship | Note |
|------|-------------|------|
| REQ-001 ↔ REQ-002 | Overlapping | Both remove the same `ifdef::katello,orcharhino[]` guards in the two master.adoc files and add the same whole-of-PyPI/include-exclude caveats. Consolidate into one guard-removal + one sync-procedure edit. |
| REQ-001/002 ↔ REQ-003 | Sequential + Complementary | Guards must be opened first, or the Download Policy step added to the Python sync procedure never renders for Satellite. |
| REQ-001/002 ↔ REQ-004 | Complementary | ISS export/import is a separate aspect of the same feature; depends on the Python build conditional being settled. |
| REQ-003 ↔ REQ-005 | Independent (shared-file caution) | `snip_step-downloading-file-from-server.adoc` is shared with file repositories; do not globally edit it. |
| REQ-005 ↔ REQ-006 | Parallel / Sibling | Independent corrections to the Developer consumption and viewing procedures. |

## Theme clustering

**Cluster A — Surface Python content for the Satellite build**
- Summary: Remove the `ifdef::katello,orcharhino[]` guards so the already-written Python assemblies build for Satellite, and reconcile the content-flow source list.
- Issues: REQ-001, REQ-002 (SAT-32536, SAT-36514, SAT-33274, SAT-36511)
- Overlap risk: **High** — consolidate into shared edits; do not duplicate guard changes.
- Recommended ownership: `doc-Managing_Content/master.adoc` and `doc-Managing_Hosts/master.adoc`, plus the two Python assemblies.

**Cluster B — Python feature-parity enhancements**
- Summary: Bring Python to parity with other content types for download policy and Inter-Server Sync.
- Issues: REQ-003 (SAT-36510, PR #11842/#11858), REQ-004 (SAT-49247)
- Overlap risk: **Low–Medium** — both edit shared content-type lists/snippets; keep build conditionals consistent.
- Recommended ownership: `con_download-policies-overview.adoc`, `proc_synchronizing-python-repositories.adoc`, `snip_content-types-export.adoc`, `snip_content-types-syncable-export.adoc`.

**Cluster C — Python consumption path and navigation corrections**
- Summary: Correct the `/pypi/` distribution path in pip examples and update the viewing-packages navigation to the dedicated UI page.
- Issues: REQ-005 (SAT-43766), REQ-006 (SAT-33275)
- Overlap risk: **Low** — independent single-module edits.
- Recommended ownership: `proc_installing-python-packages-on-a-host-from-project-server.adoc`, `proc_viewing-available-python-packages.adoc`, and the two download-files procedures.

## Gap analysis (existing vs needed)

| Category | Finding |
|----------|---------|
| Coverage | All required content already exists in `guides/common`. No greenfield modules are needed. The gap is build-visibility: 9 modules across 2 assemblies are gated out of the Satellite build. |
| Currency | `proc_installing-python-packages-on-a-host-from-project-server.adoc:18` hardcodes an outdated `/pulp/content/` pip index URL (should be `/pypi/`). `proc_viewing-available-python-packages.adoc` steps 1–2 use stale web UI navigation (Other Content Types + Type menu). |
| Completeness | `proc_synchronizing-python-repositories.adoc` lacks the whole-of-PyPI caveat, Includes/Excludes semantics, and the optional Download Policy step. `con_download-policies-overview.adoc` omits Python from supported content types (satellite branch says "RPM packages" only; non-satellite branch says "Deb, Yum, and container image content"). The lazy-synchronization restriction wording (lines 25–30) excludes Python and needs reconsideration. |
| Structure | Modules are already correctly typed (CONCEPT/PROCEDURE/REFERENCE/SNIPPET/ASSEMBLY) and follow modular-docs conventions. No retyping needed. |
| User stories | The SysAdmin "manage Python content" and Developer "consume Python content on hosts" journeys are complete as modules but invisible for Satellite. Content-flow discovery (`con_content-flow-in-project.adoc`) omits PyPI from the satellite external-sources branch, breaking the Discover-phase entry point. Export/import (ISS) user story is missing Python from the shared content-type snippets. |

### Content journey phase coverage

| Phase | Module(s) | Status |
|-------|-----------|--------|
| Discover | `con_content-flow-in-project.adoc` (external content sources) | Gap: PyPI missing from satellite branch — add it |
| Discover / Learn | `con_managing-python-content-in-project.adoc`, `con_consuming-python-content-on-hosts.adoc` | Exist; just need to render for Satellite |
| Evaluate | `proc_synchronizing-python-repositories.adoc`, download-policy procedures | Exist; add caveats + Download Policy step |
| Adopt | upload / view / install / download procedures, ISS export/import snippets | Exist; surface + correct paths and navigation |

No unexplained phase gaps remain after the planned edits; the distribution is appropriately Adopt-heavy because this is a mature feature being surfaced, not introduced.

## Assembly structure

No new assemblies or parent topics. Both main jobs already have Parent Topic assemblies in the repository; per topic-proliferation control, the work updates existing structure only.

**Assembly 1 — `assembly_managing-python-content-in-project.adoc`** (Category: Administer; Main Job: Manage Python content; Persona: SysAdmin)
- `con_managing-python-content-in-project.adoc` (CONCEPT, parent-topic intro)
- `proc_synchronizing-python-repositories.adoc` (PROCEDURE)
- `proc_uploading-content-to-a-python-repository-by-using-web-ui.adoc` (PROCEDURE)
- `proc_uploading-content-to-a-python-repository-by-using-cli.adoc` (PROCEDURE)
- `proc_viewing-available-python-packages.adoc` (PROCEDURE)
- Surfaced by removing the `ifdef::katello,orcharhino[]` guard at `doc-Managing_Content/master.adoc:108`.

**Assembly 2 — `assembly_consuming-python-content-on-hosts.adoc`** (Category: Operate/Develop; Main Job: Consume Python content on hosts; Persona: Developer)
- `con_consuming-python-content-on-hosts.adoc` (CONCEPT, parent-topic intro)
- `proc_installing-python-packages-on-a-host-from-project-server.adoc` (PROCEDURE)
- `proc_downloading-files-to-a-host-from-a-python-repository-by-using-web-ui.adoc` (PROCEDURE)
- `proc_downloading-files-to-a-host-from-a-python-repository-by-using-cli.adoc` (PROCEDURE)
- Surfaced by removing the `ifdef::katello,orcharhino[]` guard at `doc-Managing_Hosts/master.adoc:91`.

**Cross-references**: the SysAdmin managing assembly should link to the Developer consuming assembly (what sync enables downstream), and the consuming assembly should link back to the managing assembly as a prerequisite (a published Python repository must exist). Keep the personas separate — do not merge the managing and consuming user stories into one module.

**Shared/supporting modules** (render into multiple procedures/guides via snippets or shared concepts):
- `con_content-flow-in-project.adoc` (Discover concept, both personas)
- `con_download-policies-overview.adoc` (Reference/concept, SysAdmin)
- `snip_content-types-export.adoc`, `snip_content-types-syncable-export.adoc` (transcluded into every export/import procedure)

## Build conditional note (REQ-004 resolution)

REQ-001/REQ-002 confirm Python content is enabled for the Satellite build in 6.21 (first-class, default-enabled). Therefore the new Python entries in `snip_content-types-export.adoc` and `snip_content-types-syncable-export.adoc` must render for all three builds (satellite, katello, orcharhino). Use the same build conditional that the rest of the Python content-management documentation uses after the guards are opened — i.e., gate consistently to all three builds, or leave ungated if the surrounding list items are ungated. Confirm the final conditional against `guides/common/attributes.adoc:54-56`. Also confirm the open question (SAT-33274/SAT-49247) that Python is NOT supported in the syncable export format before finalizing `snip_content-types-syncable-export.adoc`.

## Module specifications (Updated Docs)

Each specification lists: file, type, persona/journey phase, the change, prerequisites/dependencies, and source.

### Cluster A — Surface Python content for Satellite

1. **`guides/doc-Managing_Content/master.adoc`** (ASSEMBLY master) — SysAdmin / Evaluate
   - Change: at line ~108, change the guard around `include::common/assembly_managing-python-content-in-project.adoc` from `ifdef::katello,orcharhino[]` to include satellite (`ifdef::katello,orcharhino,satellite[]`), or remove the guard if Python is now universal.
   - Dependency: foundational — nothing else in Cluster A renders for Satellite until this is done.
   - Source: REQ-001, REQ-002; SAT-33274, SAT-36511.

2. **`guides/doc-Managing_Hosts/master.adoc`** (ASSEMBLY master) — Developer / Adopt
   - Change: at line ~91, apply the same guard change around `include::common/assembly_consuming-python-content-on-hosts.adoc`.
   - Dependency: foundational for the Developer consumption assembly.
   - Source: REQ-001, REQ-002.

3. **`guides/common/modules/con_content-flow-in-project.adoc`** (CONCEPT) — both personas / Discover
   - Change: in the `ifdef::satellite[]` branch of the "External content sources" list, add PyPI (the branch currently reads "custom Yum repositories," while the `ifndef::satellite[]` branch reads "custom Deb and Yum repositories, PyPI,"). Add PyPI to the satellite branch so Satellite's content-flow concept lists it.
   - Source: REQ-001; `con_content-flow-in-project.adoc:15-20`.

4. **`guides/common/modules/con_managing-python-content-in-project.adoc`** (CONCEPT) — SysAdmin / Discover
   - Change: verify it renders cleanly once the assembly guard is opened; confirm no platform-specific guards block Satellite output and all attributes resolve. Optional: add an xref to the download policies overview (see REQ-003).
   - Source: REQ-001, REQ-002, REQ-003.

5. **`guides/common/modules/con_consuming-python-content-on-hosts.adoc`** (CONCEPT) — Developer / Discover
   - Change: verify it renders cleanly for satellite; confirm cross-reference macros resolve.
   - Source: REQ-002.

6. **`guides/common/assembly_managing-python-content-in-project.adoc`** / **`guides/common/assembly_consuming-python-content-on-hosts.adoc`** (ASSEMBLY) — verify all referenced attributes/xrefs build for satellite without asciidoctor warnings (the maps/Satellite build fails on any warning).
   - Source: REQ-002.

### Cluster B — Feature-parity enhancements

7. **`guides/common/modules/proc_synchronizing-python-repositories.adoc`** (PROCEDURE) — SysAdmin / Evaluate
   - Change: confirm it renders for Satellite attributes; add a note that syncing all of PyPI is supported but is a long initial synchronization requiring terabyte-scale disk, and recommend limiting scope with Includes; clarify Includes/Excludes semantics (empty Includes syncs all available packages minus any Excludes); add an optional Download Policy selection step noting the default is On Demand for Python repositories.
   - Dependency: Cluster A guard removal (so it renders for Satellite); consolidates the REQ-001/REQ-002 caveat work with REQ-003 Download Policy step.
   - Source: REQ-001, REQ-002, REQ-003; SAT-36510, PR #11842.

8. **`guides/common/modules/con_download-policies-overview.adoc`** (CONCEPT) — SysAdmin / Reference
   - Change: add Python to the supported content types in both the `ifdef::satellite[]` branch (currently "RPM packages") and the `ifndef::satellite[]` branch (currently "Deb, Yum, and container image content"); reconsider the lazy-synchronization restriction wording at lines 25–30 so it no longer implies On Demand is unavailable for Python.
   - Source: REQ-003; SAT-36510, PR #11842, PR #11858.

9. **`guides/common/modules/proc_updating-the-download-policy-for-a-repository-by-using-web-ui.adoc`** and **`...-by-using-cli.adoc`** (PROCEDURE) — SysAdmin / Adopt
   - Change: verify content-type agnostic; if either enumerates supported types, add Python; ensure the CLI example applies to Python repositories.
   - Source: REQ-003.

10. **`guides/common/modules/snip_content-types-export.adoc`** (SNIPPET) — SysAdmin / Adopt (disconnected)
    - Change: add "Python content" to the list of content types exportable in the default (importable) format, using a build conditional consistent with Python content management (all three builds in 6.21). Transcluded into all export procedures.
    - Source: REQ-004; SAT-49247.

11. **`guides/common/modules/snip_content-types-syncable-export.adoc`** (SNIPPET) — SysAdmin / Adopt (disconnected)
    - Change: add Python to the "You cannot export ... in the syncable format" exclusion lists (both satellite and `ifndef::satellite` branches). Confirm the syncable-format exclusion with engineering (SAT-33274/SAT-49247 open question) before finalizing.
    - Source: REQ-001 (SAT-33274 open question), REQ-004; SAT-49247.

### Cluster C — Consumption path and navigation corrections

12. **`guides/common/modules/proc_installing-python-packages-on-a-host-from-project-server.adoc`** (PROCEDURE) — Developer / Adopt
    - Change: at line 18 the pip `--index-url` hardcodes `/pulp/content/.../simple/`; change the path segment to `/pypi/` to match the corrected Published At distribution path. Verify `{foreman-example-com}` resolves for the satellite build. Keep the `--trusted-host` guidance.
    - Source: REQ-005, REQ-002; SAT-43766. Line ref: `:18`.

13. **`guides/common/modules/proc_downloading-files-to-a-host-from-a-python-repository-by-using-web-ui.adoc`** and **`...-by-using-cli.adoc`** (PROCEDURE) — Developer / Adopt
    - Change: these copy the Published At URL (self-correcting), but verify the shared curl snippet `snip_step-downloading-file-from-server.adoc` does not mislead Python users. CAUTION: that snippet is shared with file repositories and shows a generic `/pulp/content/` path — do NOT globally change it. Consider adding a note that Python repositories publish under `/pypi/`.
    - Source: REQ-005; SAT-43766. Note: `snip_step-downloading-file-from-server.adoc:13,22` is shared.

14. **`guides/common/modules/proc_viewing-available-python-packages.adoc`** (PROCEDURE) — SysAdmin / Adopt
    - Change: replace steps 1–2 (navigate to Content > Content Types > Other Content Types, then select Python Packages from the Type menu) with a single step: navigate to Content > Content Types > Python Packages. This is the only module in the repo referencing the old navigation for Python.
    - Source: REQ-006; SAT-33275.

## Implementation order (dependency-based)

1. **Open the build guards** — `doc-Managing_Content/master.adoc:108` and `doc-Managing_Hosts/master.adoc:91`. Foundational; nothing else renders for Satellite without this. (Cluster A, REQ-001/002)
2. **Add PyPI to the content-flow satellite branch** — `con_content-flow-in-project.adoc`. Restores the Discover-phase entry point. (REQ-001)
3. **Verify the surfaced concepts/assemblies build warning-free** for satellite — the two Python assemblies and their CONCEPT intros. (REQ-001/002)
4. **Enhance the sync procedure** — whole-of-PyPI caveat, Includes/Excludes semantics, and the Download Policy step, in one pass. (REQ-002 + REQ-003)
5. **Update the download-policies overview and update-policy procedures** — add Python; fix lazy-sync wording. (REQ-003)
6. **Update the ISS export snippets** — default-format inclusion and syncable-format exclusion; settle the build conditional. (REQ-004)
7. **Correct the pip `/pypi/` index path** in the install procedure and verify the download procedures. (REQ-005)
8. **Update the viewing-packages navigation** to the dedicated Python Packages page. (REQ-006)
9. **Final build verification** — compile satellite, katello, and orcharhino builds; confirm zero asciidoctor warnings.

## New Docs

* _(none)_ — All six requirements are satisfied by updating existing modules and assemblies. No greenfield modules are required; the core task is surfacing already-written `guides/common` content for the Satellite build and reconciling shared lists, paths, and navigation.

## Updated Docs

* `guides/doc-Managing_Content/master.adoc` (Assembly)
    Line ~108: change the Python content assembly guard from `ifdef::katello,orcharhino[]` to include satellite so the Managing Python content assembly builds for Satellite.
* `guides/doc-Managing_Hosts/master.adoc` (Assembly)
    Line ~91: change the consuming-Python-content assembly guard to include satellite.
* `guides/common/modules/con_content-flow-in-project.adoc` (Concept)
    Add PyPI to the `ifdef::satellite[]` external content sources list (satellite branch currently omits it).
* `guides/common/modules/con_managing-python-content-in-project.adoc` (Concept)
    Confirm clean satellite render; optionally add an xref to the download policies overview.
* `guides/common/modules/con_consuming-python-content-on-hosts.adoc` (Concept)
    Confirm clean satellite render and that cross-reference macros resolve.
* `guides/common/assembly_managing-python-content-in-project.adoc` (Assembly)
    Verify all referenced attributes/xrefs build for satellite without warnings.
* `guides/common/assembly_consuming-python-content-on-hosts.adoc` (Assembly)
    Verify the pip examples and xrefs build for satellite without warnings.
* `guides/common/modules/proc_synchronizing-python-repositories.adoc` (Procedure)
    Add whole-of-PyPI sync caveat (terabyte-scale disk, long first sync, recommend Includes), clarify Includes/Excludes semantics, and add an optional Download Policy step (default On Demand for Python).
* `guides/common/modules/con_download-policies-overview.adoc` (Concept)
    Add Python to supported content types in both satellite and non-satellite branches; reconsider the lazy-synchronization restriction wording so it does not exclude Python.
* `guides/common/modules/proc_updating-the-download-policy-for-a-repository-by-using-web-ui.adoc` (Procedure)
    Verify content-type agnostic; add Python if types are enumerated.
* `guides/common/modules/proc_updating-the-download-policy-for-a-repository-by-using-cli.adoc` (Procedure)
    Verify Python is covered; ensure the CLI example applies to Python repositories.
* `guides/common/modules/snip_content-types-export.adoc` (Snippet)
    Add "Python content" to the default (importable) export format list, gated consistently with Python content management.
* `guides/common/modules/snip_content-types-syncable-export.adoc` (Snippet)
    Add Python to the syncable-format exclusion lists (both branches); confirm exclusion with engineering.
* `guides/common/modules/proc_installing-python-packages-on-a-host-from-project-server.adoc` (Procedure)
    Line 18: change the pip `--index-url` path from `/pulp/content/` to `/pypi/`; verify `{foreman-example-com}` resolves for satellite.
* `guides/common/modules/proc_downloading-files-to-a-host-from-a-python-repository-by-using-web-ui.adoc` (Procedure)
    Verify the shared curl snippet does not mislead Python users; consider a `/pypi/` note. Do not globally edit the shared snippet.
* `guides/common/modules/proc_downloading-files-to-a-host-from-a-python-repository-by-using-cli.adoc` (Procedure)
    Same `/pypi/` consistency check as the web UI variant.
* `guides/common/modules/proc_viewing-available-python-packages.adoc` (Procedure)
    Replace the Other Content Types + Type menu navigation (steps 1–2) with a single step: Content > Content Types > Python Packages.

## Content sources (JIRA and PR/MR analysis)

### JIRA tickets
* [SAT-32536](https://redhat.atlassian.net/browse/SAT-32536) — Epic: Enable synchronization and management of PyPI-type repositories
* [SAT-35510](https://redhat.atlassian.net/browse/SAT-35510) — Parent Feature
* [SAT-33274](https://redhat.atlassian.net/browse/SAT-33274) — DOC catch-all: remove if-defs; cover sync, consume, include/exclude, import/export
* [SAT-36511](https://redhat.atlassian.net/browse/SAT-36511) — Document how to sync and consume PyPI content
* [SAT-36514](https://redhat.atlassian.net/browse/SAT-36514) — Enable the Python content type by default
* [SAT-36510](https://redhat.atlassian.net/browse/SAT-36510) — Specify a download policy (6.21.0 fix version)
* [SAT-49247](https://redhat.atlassian.net/browse/SAT-49247) — Add ISS support for PyPI (default/importable format, incremental; syncable out of scope)
* [SAT-43766](https://redhat.atlassian.net/browse/SAT-43766) — Python repo distribution path displayed incorrectly (`/pypi/` fix; QA-confirmed 6.21.0)
* [SAT-33275](https://redhat.atlassian.net/browse/SAT-33275) — Create dedicated Python package type page

### Pull requests
* [katello#11842](https://github.com/Katello/katello/pull/11842) — Download policy support for Python repositories; adds Python to download-policy-supporting content types and migrates existing Python repos to on_demand. (Fetched via public GitHub WebFetch; git-pr-reader API returned 401.)
* [katello#11858](https://github.com/Katello/katello/pull/11858) — Download policy reverting fix.

### Key code files (build guards and shared lists)
* `guides/doc-Managing_Content/master.adoc:108` — `ifdef::katello,orcharhino[]` gating the Python content assembly (confirmed)
* `guides/doc-Managing_Hosts/master.adoc:91` — `ifdef::katello,orcharhino[]` gating the consuming-Python assembly (confirmed)
* `guides/common/modules/con_content-flow-in-project.adoc:15-20` — satellite branch omits PyPI (confirmed)
* `guides/common/modules/con_download-policies-overview.adoc:7-30` — satellite/non-satellite content-type and lazy-sync wording (confirmed)
* `guides/common/modules/snip_content-types-export.adoc` / `snip_content-types-syncable-export.adoc` — shared export lists (confirmed)
* `guides/common/modules/proc_installing-python-packages-on-a-host-from-project-server.adoc:18` — hardcoded `/pulp/content/` pip index URL (confirmed)
* `guides/common/modules/proc_viewing-available-python-packages.adoc:13-14` — stale Other Content Types navigation (confirmed)
* `guides/common/attributes.adoc:54-56` — build-to-attribute mapping (verify for REQ-004 conditional)

### External references
* [KCS 4461511](https://access.redhat.com/solutions/4461511), [KCS 3481621](https://access.redhat.com/solutions/3481621)
* [Upstream Foreman Python content docs](https://docs.theforeman.org/nightly/Managing_Content/index-katello.html#Managing_Python_Type_Content_content-management)
* [Pulp Python sync guide](https://pulpproject.org/pulp_python/docs/user/guides/sync/) and [PyPI distribution guide](https://pulpproject.org/pulp_python/docs/user/guides/pypi/)

## Build and verification notes

* Satellite/maps builds fail on any asciidoctor warning — after opening the guards, verify satellite, katello, and orcharhino builds all compile cleanly (REQ-002 acceptance criterion).
* Do not use `[tabs]` constructs — the downstream (ccutil) Satellite build does not render them.
* Keep the shared `snip_step-downloading-file-from-server.adoc` intact; it is reused by file repositories.

## Open questions for SME confirmation

1. Syncable export format: confirm Python is NOT supported in the syncable export format (SAT-33274/SAT-49247 refinement) before finalizing `snip_content-types-syncable-export.adoc`.
2. REQ-004 build conditional: confirm the exact conditional for the new snippet entries against `attributes.adoc:54-56` (expected: all three builds in 6.21).
3. PM/SME/UX contact names were not captured in the requirements analysis — resolve from parent SAT-35510 / SAT-32536 / SAT-33274 before posting to JIRA.
