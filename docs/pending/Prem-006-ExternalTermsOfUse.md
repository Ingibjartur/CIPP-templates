# SKV365-CA-Prem-006-ExternalTermsOfUse - not in the repo, created manually

**Status:** deliberately excluded from `CATemplate/`. Do not add it back as a `.json` file.

## Why

The policy grants on a Terms of Use object, referenced by its object ID:

    "termsOfUse": ["{TERMS-OF-USE-ID}"]

A Terms of Use is a per-tenant object with a tenant-specific ID. Unlike groups and named
locations there is no repo-side template type for one, and unlike those two it cannot be
referenced by display name either. There is nothing the repo can carry that resolves correctly
in a customer tenant, so the file would only ever ship a literal token.

The number **006 stays reserved** for this policy. It is not a gap to be closed - the policy
exists, it is just created by hand.

## Onboarding steps (for the SOP)

1. Create the Terms of Use document in Entra admin center:
   **Entra ID > Conditional Access > Terms of use > New terms**.
   Agree the document content with the customer first - this is a legal artefact, not a technical one.
2. Record its object ID.
3. Create the CA policy by hand from the definition below, substituting the ID.
4. Leave it **report-only** until the rest of the Prem set is enforced.
5. Record the ToU object ID in the customer's documentation. It is needed again if the policy is
   ever rebuilt, and a new ToU document gets a new ID, which silently breaks the policy.

## Policy definition

```json
{
  "displayName": "SKV365-CA-Prem-006-ExternalTermsOfUse",
  "state": "enabledForReportingButNotEnforced",
  "conditions": {
    "clientAppTypes": [
      "all"
    ],
    "applications": {
      "includeApplications": [
        "All"
      ],
      "excludeApplications": []
    },
    "users": {
      "includeUsers": [],
      "excludeUsers": [],
      "includeGroups": [],
      "excludeGroups": [
        "SKV365-CA-Global-Exclusions"
      ],
      "includeRoles": [],
      "excludeRoles": [],
      "includeGuestsOrExternalUsers": {
        "guestOrExternalUserTypes": "internalGuest,b2bCollaborationGuest,b2bCollaborationMember,otherExternalUser",
        "externalTenants": {
          "membershipKind": "all"
        }
      }
    }
  },
  "grantControls": {
    "operator": "OR",
    "builtInControls": [],
    "customAuthenticationFactors": [],
    "termsOfUse": [
      "{TERMS-OF-USE-ID}"
    ]
  },
  "sessionControls": null
}
```

## Notes
- Terms of Use requires Entra ID P1 or higher.
- A ToU that is re-published as a new version prompts every user to re-accept. Plan the timing.
- Guests are the usual target here - confirm the policy's user scope matches the customer's
  external collaboration model before enforcing.
