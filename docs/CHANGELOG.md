# Repo layout history

## 2026-09-12 (5) - Prem-006 held back from the repo
`SKV365-CA-Prem-006-ExternalTermsOfUse` removed from `CATemplate/Prem/`. It grants on a Terms of
Use object referenced by tenant-specific ID; there is no repo-side template type for one and it
cannot be referenced by display name, so the file could only ever ship a literal token.

Definition and onboarding steps moved to `docs/pending/Prem-006-ExternalTermsOfUse.md`, as
markdown rather than JSON so CIPP cannot pick it up by accident. Number 006 stays reserved.

Prem is now 15 files numbered 001-005 and 007-016.

## 2026-09-12 (4) - all tiers populated, all tiers renumbered
Extracted the full CA set from SKV365 Command Center v2.3: 32 policies across Core (6), Ess (5),
Prem (16), FL (3 shared frontline), FLLight (1), FLEss (1). All carry the `@odata` type markers
and a deterministic `id`; all are report-only; all group and named-location references are
display names.

Folder changes: added `FL/` for the shared frontline policies, renamed `FLLite/` to `FLLight/` so
folder names match the tier segment of each `displayName`. `FLCore/` stays empty by design.

Renumbered every tier to close gaps - Core 006/007 became 005/006, Prem 002-017 became 001-016,
FLLight 004 became 001. Full old-to-new map with new GUIDs in `RENAME-MAP.md`. Renaming changes
the GUID, so renamed policies arrive in CIPP as new templates; only the two Core rows had ever
been synced and need deleting.

Core-004 and Core-005 keep the fixes made earlier today (named location by display name, Device
Registration Service excluded) - those are in the repo but not yet in the Command Center.

## 2026-09-12 (3) - Core-006 DRS exclusion, Core-004 Iceland, named location template
- `Core-006`: added Device Registration Service (`01cb2876-7ebd-4aa4-9cc9-d28bd4d359a9`) to
  `excludeApplications`. Microsoft enforces authentication-flows policies against that resource
  for any policy targeting all resources, so device-code-flow device registration would otherwise
  break.
- `Core-004`: replaced the literal token `{NL-AllowedCountries-Id}` with the named location's
  display name `SKV365-NL-AllowedCountries`. Referencing it by ID is not possible - Graph assigns
  its own object id when the named location is created in a tenant, so our repo id can never match.
- Added `NamedLocations/SKV365-NL-AllowedCountries.json`, a `countryNamedLocation` containing `IS`
  only, `includeUnknownCountriesAndRegions: false`, `countryLookupMethod: clientIpAddress`.
- `Core-003` left unchanged. Blocking Safari on mobile for the Office 365 suite is intended.

Policy bodies otherwise unchanged - verified by field-level diff, 1 change in 004, 1 in 006,
0 in the other four.

## 2026-09-12 (2) - add @odata type markers and id
Renaming the folder to `CATemplate` did not help: under a clean test (old folder deleted from the
repo, all SKV365 rows deleted from CIPP) all six still imported as Intune templates. Folder name
is not the selector.

`j0eyv/ConditionalAccessBaseline` is a **built-in** CIPP repo with 39 templates correctly in CA
Templates. Its files are raw Graph exports carrying `@odata.context`, `@odata.type` and `id`.
The CA Templates list shows CA000's GUID as `809741fe-fb1b-4746-9ff0-83a978a4c891`, which is that
policy's `id` field verbatim - CIPP takes the template GUID from `id`.

Added `@odata.context`, `@odata.type`, `@odata.id` and a deterministic `id` to all six. Policy
bodies unchanged (verified by diff against the previous files).

## 2026-09-12 - ConditionalAccess -> CATemplate
Policies under `ConditionalAccess/CorePolicies/` were imported by CIPP as **Intune** templates,
not CA templates. Confirmed twice: the logbook entry
`Updating Intune template 'SKV365-CA-Core-001-RequireMFA' ... (ExecCommunityRepo)` and the
two policies appearing in CIPP's Intune > Policy Templates list (two synced, two present).

Renamed the top-level folder to `CATemplate` and moved tiers one level below it.
Tier folders: Core, Ess, Prem, FLLite, FLCore, FLEss.

## 2026-09-11 - initial
Six Core-tier policies extracted from the SKV365 Command Center script, converted to UTF-8
without BOM, tenant object IDs replaced by group display names, state forced to report-only.
