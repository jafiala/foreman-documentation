# Documentation Requirements

**Source**: Enable synchronization and management of PyPI-type repositories
**Date**: 2026-10-06
**Release/Sprint**: 6.21.0

## Summary

- Total requirements analyzed: 6
- New modules needed: 0
- Existing modules to update: 19
- Breaking changes requiring docs: 0

## Requirements by priority

### High

#### REQ-001: Python-type content fully enabled and first-class in Satellite
- **Source**: [SAT-32536](https://redhat.atlassian.net/browse/SAT-32536) | [SAT-36514](https://redhat.atlassian.net/browse/SAT-36514) | [SAT-33274](https://redhat.atlassian.net/browse/SAT-33274)
- **Summary**: In Satellite 6.21, Python (PyPI) content becomes a first-class, default-enabled content type in Satellite Server and Capsule, matching Yum, Deb, and container content. The pulp-python plugin is now packaged into Satellite/Capsule and enabled by the installer, and the Python type no longer requires publications (improving sync performance, with metadata generated on pull). The documentation work is to remove the conditional build guards that previously excluded Satellite from the already-written upstream Python-content modules.
- **User impact**: Satellite and Capsule users can now synchronize, upload, mirror, and distribute Python (PyPI) packages to hosts as a default-supported content type, and they need documentation covering how to do so, including the include/exclude filter behavior and the expectation that a full mirror of all of PyPI is a long first sync.
- **Documentation action**:
  - [ ] Update `guides/doc-Managing_Content/master.adoc` (ASSEMBLY) — Line ~108: change the guard around include of `common/assembly_managing-python-content-in-project.adoc` from `ifdef::katello,orcharhino[]` to include satellite so the assembly builds for Satellite
  - [ ] Update `guides/doc-Managing_Hosts/master.adoc` (ASSEMBLY) — Line ~91: change the guard around include of `common/assembly_consuming-python-content-on-hosts.adoc` to include satellite
  - [ ] Update `guides/common/modules/con_content-flow-in-project.adoc` (CONCEPT) — Lines 15-20: the satellite variant of the external content sources list omits PyPI; add PyPI to the satellite branch
  - [ ] Update `guides/common/modules/snip_content-types-syncable-export.adoc` (SNIPPET) — Verify/update the syncable export/import support statement for Python on Satellite (SAT-33274 open question)
  - [ ] Update `guides/common/modules/proc_synchronizing-python-repositories.adoc` (PROCEDURE) — Confirm module renders for Satellite attributes; add a note that syncing all of PyPI is supported but a long first sync, and clarify include/exclude field behavior
  - [ ] Review `guides/common/modules/con_managing-python-content-in-project.adoc` (CONCEPT) — Confirm no platform-specific guards block Satellite output once the assembly guard is opened
- **Acceptance criteria**:
  - [ ] The Managing content guide for Satellite 6.21 renders the 'Managing Python content' assembly (sync, upload via Web UI/CLI, viewing available packages)
  - [ ] The Managing hosts guide for Satellite 6.21 renders the 'Consuming Python content on hosts' assembly (installing packages, downloading files via Web UI/CLI)
  - [ ] A Satellite user can follow the documented procedure to synchronize a Python (PyPI) repository, including using the Includes, Excludes, and Keep latest packages fields
  - [ ] The Satellite content-flow concept lists PyPI among supported external content sources
  - [ ] Documentation states that synchronizing the entire PyPI index is supported and warns it is a long initial synchronization
  - [ ] Documentation accurately states whether Python content supports syncable-format export/import on Satellite
  - [ ] No conditional build guard excludes Satellite from Python-type content modules in the content and hosts guides
  - [ ] A Satellite host can be configured to consume Python content from Satellite following the documented procedure
- **References**:
  - [SAT-33274 (doc catch-all AC)](https://redhat.atlassian.net/browse/SAT-33274): remove if-defs; cover sync, consume, include/exclude oddities, import/export
  - [SAT-32536 comments](https://redhat.atlassian.net/browse/SAT-32536): all-of-PyPI first sync is slow but default; Python publications dropped
  - `guides/doc-Managing_Content/master.adoc:108-110`: ifdef guard excluding Satellite from Python content assembly
  - `guides/doc-Managing_Hosts/master.adoc:91-93`: ifdef guard excluding Satellite from consuming Python content assembly
  - `guides/common/modules/con_content-flow-in-project.adoc:15-20`: satellite branch omits PyPI from external content sources list

#### REQ-002: Synchronize and consume PyPI content on Satellite
- **Source**: [SAT-33274](https://redhat.atlassian.net/browse/SAT-33274) | [SAT-36511](https://redhat.atlassian.net/browse/SAT-36511)
- **Summary**: Satellite 6.21 adds supported synchronization and management of PyPI (Python-type) repositories, a capability that already exists upstream in Foreman/Katello and orcharhino. Comprehensive module and assembly content already exists in `guides/common` but is gated out of the Satellite build by `ifdef::katello,orcharhino[]` directives in the Managing_Content and Managing_Hosts masters. The documentation work is primarily to surface this existing content for Satellite and add missing whole-of-PyPI sync caveats and include/exclude guidance.
- **User impact**: Satellite users can now synchronize Python packages from upstream sources such as pypi.org, upload local packages, publish them through content views and Capsules, and install them on hosts using pip pointed at Satellite. Without the doc changes, this supported feature would be invisible in Satellite documentation.
- **Documentation action**:
  - [ ] Update `guides/doc-Managing_Content/master.adoc` (ASSEMBLY) — Line ~108: change the ifdef guard to also include satellite
  - [ ] Update `guides/doc-Managing_Hosts/master.adoc` (ASSEMBLY) — Line ~91: change the ifdef guard to also include satellite
  - [ ] Update `guides/common/modules/proc_synchronizing-python-repositories.adoc` (PROCEDURE) — Add a caveat about syncing the whole of PyPI (terabytes of disk, slow full syncs, recommend Includes) and clarify include/exclude semantics
  - [ ] Verify `guides/common/modules/con_managing-python-content-in-project.adoc` (CONCEPT) — Confirm renders cleanly in satellite build; attributes resolve
  - [ ] Verify `guides/common/modules/con_consuming-python-content-on-hosts.adoc` (CONCEPT) — Confirm renders cleanly; cross-reference macros resolve
  - [ ] Verify `guides/common/assembly_managing-python-content-in-project.adoc` (ASSEMBLY) — Confirm all referenced attributes/xrefs build for satellite
  - [ ] Verify `guides/common/assembly_consuming-python-content-on-hosts.adoc` (ASSEMBLY) — Confirm the pip examples build for satellite
  - [ ] Verify `guides/common/modules/proc_installing-python-packages-on-a-host-from-project-server.adoc` (PROCEDURE) — Confirm `{foreman-example-com}` resolves for satellite build
- **Acceptance criteria**:
  - [ ] The Managing Content guide for Satellite renders the 'Managing Python content' assembly
  - [ ] The Managing Hosts guide for Satellite renders the 'Consuming Python content on hosts' assembly
  - [ ] A Satellite user can create a python-type repository, set an upstream URL (for example https://pypi.org), and run Sync Now
  - [ ] The sync procedure documents how Includes and Excludes behave, including that an empty Includes field syncs all available packages minus any Excludes
  - [ ] The documentation warns that syncing the whole of PyPI requires terabyte-scale disk space and can take a long time, and advises limiting scope with Includes
  - [ ] A Satellite user can configure a host to install Python packages using pip --index-url pointing at the Satellite Pulp content URL, including --trusted-host guidance
  - [ ] The satellite, katello, and orcharhino builds all compile without asciidoctor warnings after the ifdefs are updated
- **References**:
  - `guides/doc-Managing_Content/master.adoc:108`: ifdef gating the Python content assembly out of Satellite
  - `guides/doc-Managing_Hosts/master.adoc:91`: ifdef gating the consuming-python-content assembly out of Satellite
  - `guides/common/attributes.adoc:54-56`: build-to-attribute mapping confirming the ifdef excludes Satellite
  - [Upstream Foreman Python content docs](https://docs.theforeman.org/nightly/Managing_Content/index-katello.html#Managing_Python_Type_Content_content-management): source of the content being surfaced for Satellite

### Medium

#### REQ-003: Download policy support for Python repositories
- **Source**: [SAT-36510](https://redhat.atlassian.net/browse/SAT-36510) | [PR #11842](https://github.com/Katello/katello/pull/11842) | [PR #11858](https://github.com/Katello/katello/pull/11858)
- **Summary**: Python-type repositories in {Project} can now be assigned a download policy (Immediate or On Demand), matching the behavior already available for Yum, Deb, Docker, and file content. Existing Python repositories default to On Demand. This lets users defer downloading Python package content until it is requested, saving storage and synchronization time.
- **User impact**: Users creating or editing a Python repository can now choose a download policy in the web UI, via Hammer CLI, and via the API, and can update it later like other repository types. Existing Python repositories are set to On Demand by the upgrade migration.
- **Documentation action**:
  - [ ] Update `con_download-policies-overview.adoc` (CONCEPT) — The ifdef blocks list which content types support download policies; add Python to the supported content types and reconsider the lazy-synchronization restriction wording
  - [ ] Update `proc_synchronizing-python-repositories.adoc` (PROCEDURE) — Add an optional 'Download Policy' selection step; note the default is On Demand for Python repositories
  - [ ] Update `proc_updating-the-download-policy-for-a-repository-by-using-web-ui.adoc` (PROCEDURE) — Verify content-type agnostic; if it enumerates supported types, add Python
  - [ ] Update `proc_updating-the-download-policy-for-a-repository-by-using-cli.adoc` (PROCEDURE) — Verify Python is covered; ensure CLI example applies to Python repositories
  - [ ] Update `con_managing-python-content-in-project.adoc` (CONCEPT) — Optional: mention download policy support and add an xref to the download policies overview
- **Acceptance criteria**:
  - [ ] The download policies overview concept lists Python among the content types that support download policies for both Satellite and non-Satellite builds
  - [ ] A user following the Python repository creation procedure can locate and set the Download Policy field (Immediate or On Demand) in the web UI
  - [ ] The documentation states that Python repositories default to the On Demand download policy
  - [ ] A user can update the download policy of an existing Python repository by using the web UI and the Hammer CLI
  - [ ] No documentation implies that lazy/On Demand synchronization is unavailable for Python repositories
- **References**:
  - [SAT-36510 description](https://redhat.atlassian.net/browse/SAT-36510): confirms feature and 6.21.0 fix version
  - `con_download-policies-overview.adoc`: existing concept module that currently excludes Python from supported types; primary update target
  - [katello app/models/katello/root_repository.rb](https://github.com/Katello/katello/pull/11842): adds Python to download-policy-supporting content types
  - [katello migration set_default_download_policy_for_python_repos](https://github.com/Katello/katello/pull/11842): sets existing Python repos to on_demand

#### REQ-004: Inter-Server Sync (ISS) import/export support for PyPI
- **Source**: [SAT-49247](https://redhat.atlassian.net/browse/SAT-49247)
- **Summary**: Python (PyPI-type) repositories can now be included in Inter-Server Sync (ISS) export and import operations using the default (importable) format, including incremental exports. Previously only Ansible collections, Deb, Docker, file, kickstart, and Yum content could be exported/imported; Python content was excluded. The change is a small gating change (adding the Python type to EXPORTABLE_TYPES) because the Pulp importable exporter/importer pipeline is already content-type-agnostic.
- **User impact**: Users running multi-server or air-gapped deployments can now move synchronized Python repositories from an upstream Server to downstream Servers using the same hammer content-export / content-import workflow they use for other content types, including incremental exports. Python content remains NOT supported in the syncable export format.
- **Documentation action**:
  - [ ] Update `guides/common/modules/snip_content-types-export.adoc` (SNIPPET) — Add 'Python content' to the list of content types exportable in the default (importable) format, gated with the same build conditional used for Python content management. This snippet is transcluded into all export procedures
  - [ ] Update `guides/common/modules/snip_content-types-syncable-export.adoc` (SNIPPET) — Add Python to the 'You cannot export ... in the syncable format' exclusion lists (both satellite and ifndef::satellite branches)
- **Acceptance criteria**:
  - [ ] The list of content types that can be exported in the default format includes Python content, visible in every export procedure that transcludes the snippet
  - [ ] The syncable-format snippet explicitly states that Python content cannot be exported in the syncable format
  - [ ] A user following the existing export procedures (and their incremental variants) can export a Python repository in the default format without needing a Python-specific procedure
  - [ ] A user following the existing import procedures can import an exported Python repository into a downstream Server
  - [ ] The Python content entries respect the same build conditional as the rest of the Python content-management documentation
- **References**:
  - [SAT-49247 Acceptance Criteria](https://redhat.atlassian.net/browse/SAT-49247): export (default format), incremental export, and import of Python repos
  - [SAT-49247 refinement comment](https://redhat.atlassian.net/browse/SAT-49247): importable format is content-type-agnostic; syncable format (yum/file only) is out of scope
  - `snip_content-types-export.adoc`: existing snippet listing default-format exportable content types; primary edit target
  - `snip_content-types-syncable-export.adoc`: existing snippet listing syncable-format exclusions; secondary edit target
  - `guides/doc-Managing_Content/master.adoc:108-109`: Python content assembly gated ifdef::katello,orcharhino[] — informs the build conditional

### Low

#### REQ-005: Corrected Python repository distribution (Published At) path
- **Source**: [SAT-43766](https://redhat.atlassian.net/browse/SAT-43766)
- **Summary**: Python repositories in Satellite are distributed by Pulp under the `/pypi/<base_path>/` path (analogous to how Ansible collections use `/galaxy/`), not the generic `/pulp/content/<base_path>/` path. A bug previously caused the repository details page to display the wrong 'Published At' URL; it now correctly shows `${BASE_ADDR}/pypi/${DIST_BASE_PATH}/`. QA confirmed the fix in 6.21.0.
- **User impact**: Users who copy the 'Published At' URL from a Python repository (for pip install or curl downloads) now get the correct /pypi/ path. Documentation that hardcodes /pulp/content/ for Python pip index URLs is now inconsistent with the product and would produce broken pip install commands.
- **Documentation action**:
  - [ ] Update `guides/common/modules/proc_installing-python-packages-on-a-host-from-project-server.adoc` (PROCEDURE) — Line 18 hardcodes the pip --index-url with /pulp/content/; change the segment to /pypi/ to match the corrected Published At distribution path
  - [ ] Update `guides/common/modules/proc_downloading-files-to-a-host-from-a-python-repository-by-using-web-ui.adoc` (PROCEDURE) — Procedure copies the Published At URL (self-correcting), but verify the shared curl snippet does not mislead Python users; consider a note that Python repos publish under /pypi/
  - [ ] Update `guides/common/modules/proc_downloading-files-to-a-host-from-a-python-repository-by-using-cli.adoc` (PROCEDURE) — Same consideration as the web UI variant; verify consistency
- **Acceptance criteria**:
  - [ ] The pip --index-url example in the installing-Python-packages procedure uses the /pypi/ distribution path, not /pulp/content/
  - [ ] Any documented 'Published At' example URL for Python repositories reflects the /pypi/<base_path>/ path
  - [ ] Python content download/install procedures are internally consistent with the corrected /pypi/ distribution path
- **References**:
  - [SAT-43766 Expected behavior](https://redhat.atlassian.net/browse/SAT-43766): Published at path should be ${BASE_ADDR}/pypi/${DIST_BASE_PATH}/
  - `proc_installing-python-packages-on-a-host-from-project-server.adoc:18`: hardcoded /pulp/content/.../simple/ pip index URL
  - `snip_step-downloading-file-from-server.adoc:13,22`: shared curl snippet showing generic /pulp/content/ path; used by both file and Python download procedures, so cannot be blindly changed

#### REQ-006: Dedicated Python Packages content type page in the web UI
- **Source**: [SAT-33275](https://redhat.atlassian.net/browse/SAT-33275)
- **Summary**: In the {ProjectWebUI}, Python Packages becomes a dedicated content type page reached directly at Content > Content Types > Python Packages (URL /python), instead of being a selection within Content > Content Types > Other Content Types. The entry is removed from Other Content Types, and the Content Counts 'Python packages' link on a repository's page now opens this dedicated page with the repository pre-selected.
- **User impact**: Users navigate to Python Packages as a first-class content type in the sidebar rather than filtering within Other Content Types, so any documented navigation path that routes through Other Content Types is now outdated.
- **Documentation action**:
  - [ ] Update `guides/common/modules/proc_viewing-available-python-packages.adoc` (PROCEDURE) — Replace the navigation steps through 'Other Content Types' and the Type menu with a single step: navigate to Content > Content Types > Python Packages. This is the only module in the repo that references the old navigation for Python
- **Acceptance criteria**:
  - [ ] Following the viewing procedure, a user reaches the Python packages list by navigating to Content > Content Types > Python Packages without selecting a Type filter
  - [ ] The procedure no longer instructs users to go through Content > Content Types > Other Content Types or to select Python Packages from a Type menu
  - [ ] Navigation terminology matches the 6.21.0 web UI where Python Packages is a dedicated content type page
- **References**:
  - [SAT-33275 description](https://redhat.atlassian.net/browse/SAT-33275): primary source of the navigation and Content Counts change
  - `proc_viewing-available-python-packages.adoc`: existing module with the stale navigation path (steps 1-2)
  - [KCS 4461511](https://access.redhat.com/solutions/4461511): customer-facing KCS linked from parent feature; context for PyPI content

## Documentation scope

### New documentation needed

| Requirement | Scope | References |
|-------------|-------|------------|
| _(none)_ | All requirements update existing modules | — |

### Existing documentation to update

| Requirement | What changed | References |
|-------------|-------------|------------|
| REQ-001 | Remove ifdef guards so Python assemblies build for Satellite; add PyPI to content-flow source list; sync caveats | SAT-33274, SAT-32536 |
| REQ-002 | Surface sync/consume assemblies for Satellite; add whole-of-PyPI sync caveats and include/exclude semantics | SAT-33274, SAT-36511 |
| REQ-003 | Add Python to download-policy-supported content types; add Download Policy step to Python sync procedure (default On Demand) | SAT-36510, PR #11842, PR #11858 |
| REQ-004 | Add Python to default-format export list; add Python to syncable-format exclusion list | SAT-49247 |
| REQ-005 | Change pip --index-url from /pulp/content/ to /pypi/ in install procedure; verify download procedures | SAT-43766 |
| REQ-006 | Update viewing-packages navigation to dedicated Content Types > Python Packages page | SAT-33275 |

## Breaking changes

_None._

## Notes

- **REQ-001/REQ-002 (overlap):** Both center on the same core task — removing `ifdef::katello,orcharhino[]` guards in the two master.adoc files so already-written Python-content modules build for Satellite. The planner should consolidate these to avoid duplicate edits. This is largely a "surface existing content" task, not greenfield authoring.
- **REQ-004 build conditional:** Verify the correct build conditional for the new snippet entries. Python content management currently ships only to katello and orcharhino, but SAT-49247 and parent SAT-35510 target 6.21.0 — confirm whether PyPI/Python content is enabled for the Satellite build in 6.21 and set the snippet guard accordingly.
- **REQ-003/REQ-005 shared snippet caution:** `snip_step-downloading-file-from-server.adoc` is reused by file repositories as well as Python; do not globally change its /pulp/content/ example without affecting file repositories.
- **GitHub access:** PR diffs for katello #11842 and #11858 could not be fetched via the git-pr-reader API (401 bad credentials); content was retrieved via public GitHub page WebFetch and cross-checked against JIRA. JIRA access was unaffected.

## Related tickets

- **Parent feature:** [SAT-35510](https://redhat.atlassian.net/browse/SAT-35510) — Enable synchronization and management of PyPI-type repositories (Feature)
- **Children:** [SAT-33270](https://redhat.atlassian.net/browse/SAT-33270) (QE sync), [SAT-33271](https://redhat.atlassian.net/browse/SAT-33271) (QE download/install), [SAT-33273](https://redhat.atlassian.net/browse/SAT-33273) (QE upload), [SAT-33275](https://redhat.atlassian.net/browse/SAT-33275) (Python package page), [SAT-36514](https://redhat.atlassian.net/browse/SAT-36514) (enable by default), [SAT-49247](https://redhat.atlassian.net/browse/SAT-49247) (ISS support), [SAT-33274](https://redhat.atlassian.net/browse/SAT-33274) (DOC catch-all), [SAT-36510](https://redhat.atlassian.net/browse/SAT-36510) (download policy), [SAT-43766](https://redhat.atlassian.net/browse/SAT-43766) (distribution path bug), [SAT-36511](https://redhat.atlassian.net/browse/SAT-36511) (doc sync/consume), [SAT-36512](https://redhat.atlassian.net/browse/SAT-36512) / [SAT-36513](https://redhat.atlassian.net/browse/SAT-36513) (QE automation), [SAT-37930](https://redhat.atlassian.net/browse/SAT-37930) (refinement)
- **Web link:** [KCS 4461511](https://access.redhat.com/solutions/4461511) — How to synchronize and manage PyPi-type repositories in Red Hat Satellite 6

## Sources consulted

### JIRA tickets
- [SAT-32536](https://redhat.atlassian.net/browse/SAT-32536) — Enable synchronization and management of PyPI-type repositories (Epic)
- [SAT-35510](https://redhat.atlassian.net/browse/SAT-35510) — Feature / parent
- [SAT-33274](https://redhat.atlassian.net/browse/SAT-33274) — [DOC] Add Python-type repositories to Satellite docs
- [SAT-33275](https://redhat.atlassian.net/browse/SAT-33275) — Create Python package type page
- [SAT-36510](https://redhat.atlassian.net/browse/SAT-36510) — Specify a download policy
- [SAT-36511](https://redhat.atlassian.net/browse/SAT-36511) — Document how to sync and consume PyPI content
- [SAT-36514](https://redhat.atlassian.net/browse/SAT-36514) — Enable the Python content type by default
- [SAT-43766](https://redhat.atlassian.net/browse/SAT-43766) — Python repo distribution path displayed incorrectly
- [SAT-49247](https://redhat.atlassian.net/browse/SAT-49247) — Add ISS Support for PyPI

### Pull requests / Merge requests
- [katello#11842](https://github.com/Katello/katello/pull/11842) — Download policy support for Python repositories (fetched via WebFetch; API 401)
- [katello#11858](https://github.com/Katello/katello/pull/11858) — Download policy reverting fix (fetched via WebFetch)

### Code files
- `guides/doc-Managing_Content/master.adoc` (ifdef guard, line ~108)
- `guides/doc-Managing_Hosts/master.adoc` (ifdef guard, line ~91)
- `guides/common/assembly_managing-python-content-in-project.adoc`
- `guides/common/assembly_consuming-python-content-on-hosts.adoc`
- `guides/common/modules/con_managing-python-content-in-project.adoc`
- `guides/common/modules/con_consuming-python-content-on-hosts.adoc`
- `guides/common/modules/con_content-flow-in-project.adoc`
- `guides/common/modules/con_download-policies-overview.adoc`
- `guides/common/modules/proc_synchronizing-python-repositories.adoc`
- `guides/common/modules/proc_installing-python-packages-on-a-host-from-project-server.adoc`
- `guides/common/modules/proc_viewing-available-python-packages.adoc`
- `guides/common/modules/proc_downloading-files-to-a-host-from-a-python-repository-by-using-cli.adoc`
- `guides/common/modules/proc_downloading-files-to-a-host-from-a-python-repository-by-using-web-ui.adoc`
- `guides/common/modules/proc_updating-the-download-policy-for-a-repository-by-using-web-ui.adoc`
- `guides/common/modules/proc_updating-the-download-policy-for-a-repository-by-using-cli.adoc`
- `guides/common/modules/snip_content-types-export.adoc`
- `guides/common/modules/snip_content-types-syncable-export.adoc`
- `guides/common/modules/snip_step-downloading-file-from-server.adoc`
- `guides/common/attributes.adoc`

### Existing documentation
- `guides/common/assembly_managing-python-content-in-project.adoc` and its 9 included modules (sync, upload, consume, install, view) — already written, gated out of Satellite build

### External references
- [Pulp Python — Sync from Remote Repositories](https://pulpproject.org/pulp_python/docs/user/guides/sync/)
- [Pulp Python — Set up your own PyPI](https://pulpproject.org/pulp_python/docs/user/guides/pypi/)
- [How to self-host a Python package index using Pulp (Red Hat Developer)](https://developers.redhat.com/articles/2022/01/17/how-self-host-python-package-index-using-pulp)
- [How to change download policy of repositories in Red Hat Satellite 6 (KCS 3481621)](https://access.redhat.com/solutions/3481621)
- [Inter-Satellite Sync: Network and Export Sync (Red Hat blog)](https://www.redhat.com/en/blog/inter-satellite-sync-network-and-export-sync)

### Web search findings
- [Pulp Python — Sync from Remote Repositories](https://pulpproject.org/pulp_python/docs/user/guides/sync/): include/exclude and sync-policy behavior
- [Story #985: Sync all packages from PyPI (Pulp)](https://pulp.plan.io/issues/985): whole-of-PyPI mirroring background
- [Unable to sync full PyPI repository (Pulp Community)](https://discourse.pulpproject.org/t/unable-to-sync-full-pypi-repository/586): full PyPI syncs can be extremely slow
- [Pulp Python — Set up your own PyPI](https://pulpproject.org/pulp_python/docs/user/guides/pypi/): Pulp Python distributions return /pypi/<base_path>/
- [RFE Bugzilla 1746508](https://bugzilla.redhat.com/show_bug.cgi?id=1746508): origin RFE referenced by the epic
