# Repo layout history

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
