# SKV365 CIPP templates

Source of truth for Wise's Skýjavakt Conditional Access baseline. CIPP syncs from this repo.

## Layout

    CATemplate/
      Core/       6 policies   office users, baseline
      Ess/        5 policies   Essential
      Prem/      15 policies   Premium (006 reserved, created by hand - see docs/pending/)
      FL/         3 policies   shared across ALL frontline segments
      FLLight/    1 policy     Frontline Core (F1) specific
      FLEss/      1 policy     Frontline Essential (F3) specific
      FLCore/     empty        FLW Core (Business Basic) has no CA policies of its own
    NamedLocations/   country named locations referenced by the policies
    docs/             internal notes - not read by CIPP

31 policies in the repo, extracted from SKV365 Command Center v2.3, plus one held back in
`docs/pending/`.

Folder name always matches the tier segment of the `displayName`. `FLCore` is deliberately
empty: those users are covered by the three `FL-*` shared policies and by
`SKV365-CA-FLLight-001-BlockDesktopAccess`, which targets `SKV365-CA-FLCore-Users` as well as
`SKV365-CA-FLLight-Users` despite its name.

Numbering is contiguous per tier. See `docs/RENAME-MAP.md` for the 2026-09-12 renumber.

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
| `SKV365-NL-AllowedCountries` | Named location | Core-004 |

`SKV365-CA-Global-Exclusions` must contain the break-glass accounts **before** enforcement.
A policy deployed against an empty exclusion group has no break-glass path.

## Known open items

- **Whether CIPP resolves display names to object IDs at deploy time is unverified.** This is the
  one that matters. Two things depend on it: `SKV365-CA-Global-*` groups in every policy, and
  `SKV365-NL-AllowedCountries` in Core-004. If CIPP does not substitute, policies deploy with no
  break-glass exclusion and Core-004 excludes nothing. One deploy test settles both.
  The named location's `id` in this repo is our own deterministic value and will NOT match the
  object Graph creates in a tenant, so referencing it by ID is not an option.
- `Prem-006-ExternalTermsOfUse` is **deliberately not in this repo**. It grants on a Terms of Use
  object, which is per-tenant and cannot be referenced by name. Definition and manual creation
  steps are in `docs/pending/Prem-006-ExternalTermsOfUse.md`, kept as markdown so CIPP cannot
  pick it up. The number 006 is reserved - it is not a gap to close.
- `Core-003` blocks Safari on iOS and Android for the Office 365 suite. Deliberate - app
  protection can only be satisfied by Edge on mobile. It does not cover Microsoft resources
  outside the Office 365 suite (Azure portal, Entra admin, Power BI) or third-party SSO apps.
- The Command Center still emits the **old** JSON shape (no `@odata` markers, no `id`) and the
  **old** policy numbering. Regenerating from it today undoes both. See CONFIRMATIONS.md 2.1.
- Exclusion group existence is checked at deploy, membership is not. Confirm
  `SKV365-CA-Global-Exclusions` contains the break-glass accounts before enforcement.
- Core tier numbering skips 005.
