# Repo layout history

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
