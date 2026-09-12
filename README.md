# SKV365 CIPP templates

Source of truth for Wise's Skýjavakt Conditional Access baseline. CIPP syncs from this repo.

## Layout

    CATemplate/
      Core/      SKV365-CA-Core-*.json
      Ess/
      Prem/
      FLLite/
      FLCore/
      FLEss/
    docs/        internal notes - not read by CIPP

**The top-level folder name is load-bearing.** CIPP routes an imported template to a store
based on it. `ConditionalAccess/` was not recognised and the policies were filed as **Intune**
templates instead (confirmed in the CIPP logbook and in Intune > Policy Templates).
`CATemplate` matches CIPP's own naming for this type - the UI switch is "Create CA Templates",
and CyberDrain's CIPP-Templates repo uses the same `<Type>Template` pattern
(`ReportBuilderTemplate`, `StandardsTemplateV2`).

Tier folders sit one level below and act as the category. CIPP already proved it recurses:
it displayed the old files with category `ConditionalAccess / CorePolicies`.

## File contract

- UTF-8, **no BOM**, LF line endings. Never save as UTF-16.
- Filename must equal `displayName`.
- `"state": "enabledForReportingButNotEnforced"` on every policy. Enforcement is a separate,
  deliberate per-tenant step. Never commit a policy in `enabled` state.
- No tenant-specific object IDs. Groups are referenced by **display name** (`SKV365-*`).
- Role references use Entra **role template IDs**, identical in every tenant - safe to hardcode.
- No `id`, `@odata.*`, `createdDateTime`, `modifiedDateTime`.

## Per-tenant prerequisites (create before deploying)

| Object | Type | Used by |
|---|---|---|
| `SKV365-CA-Global-Exclusions` | Security group | all Core policies |
| `SKV365-CA-Global-NoIntuneLicence` | Security group | Core-003 |
| `SKV365-CA-Global-TravelException` | Security group | Core-004 |
| Allowed-countries named location | Named location | Core-004 |

`SKV365-CA-Global-Exclusions` must contain the break-glass accounts **before** enforcement.
A policy deployed against an empty exclusion group has no break-glass path.

## Known open items

- `Core-004` carries the literal token `{NL-AllowedCountries-Id}`. The Command Center fills it
  from an answer file; a statically synced repo has no equivalent step. Unresolved.
- Whether CIPP resolves `SKV365-CA-Global-Exclusions` to an object ID at deploy time is
  unverified. If it does not, policies deploy with no break-glass exclusion.
- Core tier numbering skips 005.
