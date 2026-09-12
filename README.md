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

**The folder name does not select the template type.** CIPP classifies a repo template by its
JSON content. `CATemplate/` is our own organising choice; the tier folder below it is the category
CIPP displays. Both were verified not to affect where a template lands.

## Why the @odata fields are here - do not strip them

Each policy file starts with:

    "@odata.context": "https://graph.microsoft.com/beta/$metadata#identity/conditionalAccess/policies/$entity",
    "@odata.type":    "#microsoft.graph.conditionalAccessPolicy",
    "@odata.id":      "identity/conditionalAccess/policies('<id>')",
    "id":             "<id>",

These look like leftover Graph response metadata. They are not optional.

**CIPP classifies a repo template by its JSON content, not by its folder.** A file without these
markers is filed as an *Intune* template and never reaches the Conditional Access store. This was
confirmed the hard way: six policies with the `@odata` fields stripped landed in Intune Policy
Templates under two different folder layouts, while `j0eyv/ConditionalAccessBaseline` - a built-in
CIPP repo whose files carry them - has 39 templates correctly in CA Templates.

`id` is separately load-bearing: CIPP uses it as the template **GUID**, shown in the CA Templates
list. A template with no GUID cannot be retrieved or edited (CIPP issue #4456).

Our `id` values are deterministic - UUIDv5 over the displayName under namespace
`uuid5(NAMESPACE_URL, "https://wise.is/skv365/ca")` - so the Command Center can regenerate the
same value for the same policy, and a resync updates the existing template instead of making a
second one.

Fields deliberately **not** included: `createdDateTime`, `modifiedDateTime`, `@odata.editLink`,
`#microsoft.graph.restore`. Those point at a source tenant's object and carry no meaning here.

## File contract

- UTF-8, **no BOM**, LF line endings. Never save as UTF-16.
- Filename must equal `displayName`.
- `"state": "enabledForReportingButNotEnforced"` on every policy. Enforcement is a separate,
  deliberate per-tenant step. Never commit a policy in `enabled` state.
- No tenant-specific object IDs. Groups are referenced by **display name** (`SKV365-*`).
- Role references use Entra **role template IDs**, identical in every tenant - safe to hardcode.
- Every policy carries `@odata.context`, `@odata.type`, `@odata.id` and `id`. See the section
  above - CIPP needs them. Never strip them.
- No `createdDateTime`, `modifiedDateTime`, `@odata.editLink` or `#microsoft.graph.restore`.

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
