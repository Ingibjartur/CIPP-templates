# Policy rename map - 2026-09-12

All tiers renumbered to close numbering gaps. `displayName` drives the deterministic GUID, so
every renamed policy gets a new GUID and arrives in CIPP as a **new** template. The old row must
be deleted by hand after the sync.

Only Core has ever been synced, so the CIPP cleanup is just the two Core rows marked below.

| Tier | Old name | New name | New GUID | Action in CIPP |
|---|---|---|---|---|
| Core | `SKV365-CA-Core-001-RequireMFA` | `SKV365-CA-Core-001-RequireMFA` | `51186535-a2f8-5258-b4e1-ad15acaac5e9` | none - unchanged |
| Core | `SKV365-CA-Core-002-BlockLegacyAuth` | `SKV365-CA-Core-002-BlockLegacyAuth` | `1d10162e-c969-5ed7-a83c-ad47a4def69b` | none - unchanged |
| Core | `SKV365-CA-Core-003-RequireAppProtection` | `SKV365-CA-Core-003-RequireAppProtection` | `8f91ea37-1d85-5230-8d97-b8d404f48225` | none - unchanged |
| Core | `SKV365-CA-Core-004-BlockNonAllowedCountries` | `SKV365-CA-Core-004-BlockNonAllowedCountries` | `e0fa466b-d887-5bac-97af-9b91f6bfc090` | none - unchanged |
| Core | `SKV365-CA-Core-006-BlockDeviceCodeFlow` | `SKV365-CA-Core-005-BlockDeviceCodeFlow` | `112640c1-0448-5911-8576-08301fc9bd60` | **delete old row** |
| Core | `SKV365-CA-Core-007-AdminPhishResistantMFA` | `SKV365-CA-Core-006-AdminPhishResistantMFA` | `f889bf40-70fe-5fd3-a05b-4b0fa891b631` | **delete old row** |
| Ess | `SKV365-CA-Ess-001-RequireCompliantDevice` | `SKV365-CA-Ess-001-RequireCompliantDevice` | `e81fe89e-6048-51aa-8794-685330f8668b` | none - unchanged |
| Ess | `SKV365-CA-Ess-002-SecurityInfoRegistration` | `SKV365-CA-Ess-002-SecurityInfoRegistration` | `034e6e6c-80d3-526f-83f2-435eef2ab511` | none - unchanged |
| Ess | `SKV365-CA-Ess-003-AdminSignInFrequency` | `SKV365-CA-Ess-003-AdminSignInFrequency` | `7ebea0ec-a143-5745-aebb-06b6ce1f8fd2` | none - unchanged |
| Ess | `SKV365-CA-Ess-004-GuestSignInFrequency` | `SKV365-CA-Ess-004-GuestSignInFrequency` | `d7cdee19-f348-5548-b4b1-87c99d1ae77e` | none - unchanged |
| Ess | `SKV365-CA-Ess-005-BlockLegacyPowerShell` | `SKV365-CA-Ess-005-BlockLegacyPowerShell` | `8df052b2-b53c-55f3-8f6a-0da22f8a1089` | none - unchanged |
| Prem | `SKV365-CA-Prem-015-UserRiskPolicy` | `SKV365-CA-Prem-014-UserRiskPolicy` | `34e8756b-d57f-58f2-a384-63d1d6c72e5f` | none - never synced |
| Prem | `SKV365-CA-Prem-016-SignInRiskPolicy` | `SKV365-CA-Prem-015-SignInRiskPolicy` | `9e360b13-e701-5787-a566-51b97530a147` | none - never synced |
| Prem | `SKV365-CA-Prem-017-BlockHighRiskSignIns` | `SKV365-CA-Prem-016-BlockHighRiskSignIns` | `2bbe7ad7-7b4d-54a4-a0e5-aa453bc56eab` | none - never synced |
| Prem | `SKV365-CA-Prem-002-PAWAdminAccess` | `SKV365-CA-Prem-001-PAWAdminAccess` | `cec6d0a9-07d7-5f3b-b09e-b6a67e6559de` | none - never synced |
| Prem | `SKV365-CA-Prem-003-ThirdPartyApps` | `SKV365-CA-Prem-002-ThirdPartyApps` | `45f3b07e-5834-5aad-9a13-30e78eb9bb8d` | none - never synced |
| Prem | `SKV365-CA-Prem-004-BlockSensitiveDownload` | `SKV365-CA-Prem-003-BlockSensitiveDownload` | `9690b030-323c-5e36-8ac4-39c35abab7fe` | none - never synced |
| Prem | `SKV365-CA-Prem-005-ContinuousAccessEval` | `SKV365-CA-Prem-004-ContinuousAccessEval` | `98d7b45d-6645-5397-8dc7-52e1a4db144c` | none - never synced |
| Prem | `SKV365-CA-Prem-006-JITAdminAccess` | `SKV365-CA-Prem-005-JITAdminAccess` | `9a27cf3e-350e-5b33-8461-36c92417873c` | none - never synced |
| Prem | `SKV365-CA-Prem-007-ExternalTermsOfUse` | `SKV365-CA-Prem-006-ExternalTermsOfUse` | `928fe724-3142-518f-aaa9-503f98a2d587` | none - never synced |
| Prem | `SKV365-CA-Prem-008-PhishResistantMFAAll` | `SKV365-CA-Prem-007-PhishResistantMFAAll` | `f412d5d9-e9a5-5e8e-a431-d8d88e8ab593` | none - never synced |
| Prem | `SKV365-CA-Prem-009-TokenProtection` | `SKV365-CA-Prem-008-TokenProtection` | `67cefd91-0c84-5c05-b80b-9cde6dd16a3d` | none - never synced |
| Prem | `SKV365-CA-Prem-010-EnrollmentSignInFrequency` | `SKV365-CA-Prem-009-EnrollmentSignInFrequency` | `dcbaf21e-8dac-52eb-bb43-88103bfd6361` | none - never synced |
| Prem | `SKV365-CA-Prem-011-BlockAuthTransfer` | `SKV365-CA-Prem-010-BlockAuthTransfer` | `22087887-d73c-5f47-bfd9-c06f16e0fb30` | none - never synced |
| Prem | `SKV365-CA-Prem-012-IdleSessionTimeout` | `SKV365-CA-Prem-011-IdleSessionTimeout` | `32130448-147e-55bd-96ec-e734d481cade` | none - never synced |
| Prem | `SKV365-CA-Prem-013-WorkloadIdentityRisk` | `SKV365-CA-Prem-012-WorkloadIdentityRisk` | `da74dbb9-0630-5356-8296-618703d4e29f` | none - never synced |
| Prem | `SKV365-CA-Prem-014-ProtectedActions` | `SKV365-CA-Prem-013-ProtectedActions` | `e56fd07b-a39a-5971-8f2e-03722e882377` | none - never synced |
| FL | `SKV365-CA-FL-001-MobileMFA` | `SKV365-CA-FL-001-MobileMFA` | `8122a418-f6e5-5f62-93c4-e45f05b3317b` | none - unchanged |
| FL | `SKV365-CA-FL-002-BlockLegacyAuth` | `SKV365-CA-FL-002-BlockLegacyAuth` | `b7989c6b-bbb6-5c84-a0fe-8b0ec6a836b4` | none - unchanged |
| FL | `SKV365-CA-FL-003-RequireMDM` | `SKV365-CA-FL-003-RequireMDM` | `484aeec8-069d-533f-af2d-0fbd3973345f` | none - unchanged |
| FLLight | `SKV365-CA-FLLight-004-BlockDesktopAccess` | `SKV365-CA-FLLight-001-BlockDesktopAccess` | `b289d2df-7792-5112-a9a9-9db7dad9060f` | none - never synced |
| FLEss | `SKV365-CA-FLEss-001-EmailAccess` | `SKV365-CA-FLEss-001-EmailAccess` | `f5de503d-a9d0-58cd-a9a7-723e7ca9db8e` | none - unchanged |

## GUID derivation

    uuid5(uuid5(NAMESPACE_URL, "https://wise.is/skv365/ca"), displayName)

Reproducible from the displayName alone. The Command Center must use the same derivation if it
is ever updated to emit repo-ready JSON, or the two will produce different GUIDs for the same
policy and CIPP will end up with duplicates.
