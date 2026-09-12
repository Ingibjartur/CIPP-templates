# Review - SKV365 CA Core script and the 6 policies

Reviewed 2026-09-11 against the Command Center script dated 2026-08-13.

## Findings that change behaviour in a tenant

**1. Core-006 BlockDeviceCodeFlow will break device registration.**
The policy targets `includeApplications: ["All"]` with an empty `excludeApplications`.
Microsoft enforces authentication-flows policies against the Device Registration Service
for any policy targeting all resources (since early September 2024). Any tenant that uses
device code flow for device registration loses it. Microsoft's documented remedy is to
exclude app ID `01cb2876-7ebd-4aa4-9cc9-d28bd4d359a9` (Device Registration Service).
Add it to `excludeApplications` unless there is a deliberate reason not to.

**2. Core-003 RequireAppProtection blocks Safari on iOS.**
`clientAppTypes` includes `browser` and the grant is `compliantApplication`
(Require app protection policy). Safari cannot satisfy that control; only Edge on iOS can.
Result: iOS users on Safari are blocked from Office 365. That may be intended, but it is a
user-visible block, not a silent hardening. Confirm before enforcement.

**3. `{NL-AllowedCountries-Id}` in Core-004 is not repo-safe.**
The script resolves it from `-AnswerFile`. A statically synced repo has no such step.
If the literal token reaches Graph the policy either fails or excludes nothing.

**4. Exclusion depends entirely on one group existing and being populated.**
`Get-SkvGroupId` throws when the group is absent, so deployment fails closed - good.
It does not check the group is non-empty. An existing but empty
`SKV365-CA-Global-Exclusions` deploys all 6 policies with no break-glass exclusion.
In report-only that is harmless; at enforcement it is the lockout path. Add a
membership check before the enforcement step.

**5. Core-007 role list is 12 unannotated GUIDs.**
They are role template IDs, so they port across tenants correctly, but nobody editing
this in GitHub can review the list. Resolve and document the names:

    $r = Invoke-MgGraphRequest -Method GET -Uri 'v1.0/directoryRoleTemplates' -OutputType PSObject
    $r.value | Where-Object id -in $ids | Select-Object id, displayName

I have not verified each GUID maps to the role intended.

## Script-level observations

- `Deploy-SkvExoItem` is referenced in the `switch ($item.Kind)` block but is not defined
  in this script. Harmless for the Core tier (no `exo` items) but it would throw
  CommandNotFound rather than a clean error if one were added.
- `Expand-SkvPlaceholder` splices structured answers as a JSON fragment, then also runs
  `$Json.Replace($key, [string]$value)` on the same key. For an array value that second
  pass stringifies to `System.Object[]` or a space-joined list. It only bites if the token
  appears unquoted elsewhere, but the second replace should be skipped for structured values.
- `Get-SkvMissingServicePrincipal` only scans GUIDs inside `includeApplications`. None of
  these 6 policies name an app by GUID (`All`, `Office365`), so the check is a no-op for
  this tier and `Application.Read.All` is requested but unused here.
- `authenticationFlows` / `transferMethods` is confirmed available in Graph **v1.0**, so the
  v1.0 URI in Core-006 is correct.
- `$ConfirmPreference = 'None'` plus the single YES gate is a reasonable trade-off, but it
  also suppresses confirmation for `-OverwriteTenantSettings`. Not exercised by this tier.

## Correct as written

- Report-only default with the documented CAE exception is sound.
- Failing closed on a missing group rather than deploying an unexcluded policy is correct.
- Idempotency via `Test-SkvExists` on `displayName` is appropriate for CA policies.
- Graph error body extraction is worth keeping; the default exception text is useless.
- `authenticationStrength` `00000000-0000-0000-0000-000000000004` is the built-in
  phishing-resistant MFA strength, present in every tenant.
