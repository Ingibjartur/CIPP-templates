# SKV365 Conditional Access templates

Source of truth for Wise's Skýjavakt Conditional Access baseline, consumed by CIPP.

## Contract

- All JSON is **UTF-8, no BOM, LF line endings**. Do not save as UTF-16.
- Every policy carries `"state": "enabledForReportingButNotEnforced"`. Enforcement is a
  separate, deliberate per-tenant step. Do not commit a policy in `enabled` state.
- No tenant-specific object IDs. Groups are referenced by **display name**
  (`SKV365-*`); the deploying system resolves the name to an object ID in the target tenant.
- Role references use **Entra role template IDs**, which are identical in every tenant.
  These are safe to hardcode.
- Files contain no `id`, `@odata.*`, `createdDateTime` or `modifiedDateTime`.

## Layout

    Config/ConditionalAccess/<policy displayName>.json

One policy per file. Filename matches `displayName`.

## Prerequisite objects (per tenant, created before deploy)

| Object | Type | Used by |
|---|---|---|
| `SKV365-CA-Global-Exclusions` | Security group | all 6 |
| `SKV365-CA-Global-NoIntuneLicence` | Security group | Core-003 |
| `SKV365-CA-Global-TravelException` | Security group | Core-004 |
| Allowed-countries named location | Named location | Core-004 |

`SKV365-CA-Global-Exclusions` must contain the break-glass accounts **before** any policy
is enforced. A policy deployed against an empty exclusion group has no break-glass path.

## Open parameterisation problem

`SKV365-CA-Core-004-BlockNonAllowedCountries.json` carries the literal token
`{NL-AllowedCountries-Id}` in `conditions.locations.excludeLocations`. The Command Center
script substitutes this from an answer file. A repo that CIPP reads statically has no
equivalent step unless CIPP itself supports name-based named-location resolution or a
variable map. Resolve before this policy is synced. Do not let the literal token reach a tenant.

## Tier

Core (6 policies). Numbering skips 005 - carried over from the Command Center, confirm
whether a policy was retired or lives in another tier.
