# Repo layout history

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
