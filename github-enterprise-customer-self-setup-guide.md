# GitHub Enterprise Cloud: Customer Self-Setup Guide

**GitHub Enterprise Cloud with Enterprise Managed Users (EMU) and Data Residency**

> **Estimated time:** 90–150 minutes for a first tenant setup.

---

## Table of Contents

- [Overview](#overview)
- [Audience, Scope, and Success Criteria](#audience-scope-and-success-criteria)
- [Fast Path for a First Pilot](#fast-path-for-a-first-pilot)
- [Early Decisions](#early-decisions)
- [Before You Begin](#before-you-begin)
- [Setup Phases](#setup-phases)
- [Validation Procedure](#validation-procedure)
- [Common Pitfalls and Diagnostics](#common-pitfalls-and-diagnostics)
- [Go-Live Readiness and Immediate Next Steps](#go-live-readiness-and-immediate-next-steps)

---

## Overview

This guide helps you set up GitHub Enterprise Cloud (GHEC) with Enterprise Managed Users (EMU) using a self-service approach. It follows the setup path most customers use so you can configure authentication, provisioning, billing, and pilot-user validation before broader rollout.

Use this guide if you want one document that is practical, easy to follow, and detailed enough to complete setup confidently on your own.

---

## Audience, Scope, and Success Criteria

### Intended audience

This guide is for first-time enterprise administrators setting up GitHub Enterprise Cloud with EMU. It assumes coordination between:

- A GitHub enterprise setup admin
- An identity administrator for your identity provider
- An Azure administrator, if Azure billing will be connected

### What this guide covers

This guide covers:

- Creating the enterprise account
- Securing the setup admin
- Configuring authentication for EMU
- Configuring SCIM provisioning for pilot users and groups
- Connecting Azure billing, if applicable
- Optionally enabling GitHub Copilot for a pilot group
- Validating that the tenant is ready for broader rollout

### What this guide does not cover

This guide does not cover:

- Repository migration into GitHub
- Detailed organization and repository governance design
- Full production rollout planning beyond immediate go-live readiness
- GitHub Advanced Security rollout
- Step-by-step instructions for every identity provider beyond the linked references

### Success criteria

By the end of this guide, you should have:

- A working GitHub Enterprise Cloud enterprise with EMU
- A secured setup admin with 2FA and recovery codes stored safely
- Successful authentication for at least one pilot managed user
- Successful SCIM provisioning for pilot users and groups
- Billing connected and verified, if required
- Optional Copilot access verified for a pilot group

---

## Fast Path for a First Pilot

If your goal is a safe pilot before broader rollout, use this path first:

1. Create the enterprise and secure the setup admin.
2. Choose your hosting, authentication, billing, and Copilot timing decisions before changing settings.
3. Configure authentication for one pilot user or pilot group only.
4. Configure SCIM for the same pilot scope and verify user creation in enterprise **People**.
5. Validate sign-in, provisioning, and billing before assigning more users.
6. Enable Copilot only after core identity validation unless Copilot access is part of the pilot success criteria.

If any pilot check fails, stop there and fix it before moving to broader user assignments.

---

## Early Decisions

Use this table to choose the safest path before you begin detailed setup.

| Decision area | Option | Choose this when | Notes |
| --- | --- | --- | --- |
| Hosting | `GitHub.com` | You do not need data residency and want the simplest hosting path | Best fit for customers who do not have residency-specific requirements |
| Hosting | `GHE.com` | You need GitHub data residency in a supported region | Confirm region choice, network allowlists, and feature differences before rollout |
| Authentication | `OIDC` | You use Microsoft Entra ID or prefer modern federation without SAML certificate lifecycle | Recommended default for Entra unless policy requires `SAML` |
| Authentication | `SAML` | Your policy, provider standard, or existing operating model requires SAML | Plan for certificate ownership, renewal, and testing before enablement |
| Billing | Azure billing | You purchased GitHub through Azure Marketplace or need Azure-based billing alignment | Requires an eligible Azure admin and subscription access |
| Billing | GitHub direct billing | Your GitHub billing is managed directly with GitHub | No Azure subscription connection is needed |
| Copilot timing | Enable Copilot during pilot | Copilot validation is a required pilot outcome | Keep the scope to a small pilot team only |
| Copilot timing | Enable Copilot after identity validation | Your priority is safe EMU sign-in and provisioning first | Recommended default for first-time setup |

---

## Before You Begin

Most setup delays are caused by missing permissions, missing required values, or completing steps out of order.

### Blockers to clear before you start

Do not begin detailed configuration until these blockers are cleared:

- You know whether the enterprise will be hosted on `GitHub.com` or `GHE.com`.
- Your identity provider team is available to create or update the enterprise application.
- You have identified at least one pilot user and one pilot group for testing.
- If using Azure billing, you know which Azure subscription will be connected and who can approve it.
- You have a secure place to store recovery codes, SCIM tokens, and any SAML certificate details.

### Permissions required to start

| Role or permission | Why it is needed | Who usually provides it |
| --- | --- | --- |
| GitHub enterprise setup admin (from the welcome email) | Create the enterprise, configure EMU, and recover access if needed | Your GitHub admin |
| Identity provider admin access (for example, Entra Cloud Application Administrator or equivalent) | Create the enterprise application, assign pilot users, and configure federation and SCIM | Your identity admin |
| Permission to grant tenant-wide admin consent, if using Azure billing | Complete the Azure subscription connection | Your Azure admin |

### Values to collect before setup

| Item | Your value |
| --- | --- |
| Enterprise name | |
| Enterprise slug or subdomain | |
| Hosting choice (`GitHub.com` or `GHE.com`) | |
| Data residency region, if using `GHE.com` | |
| Identity provider (`Entra`, `Okta`, `PingFederate`, or other) | |
| Authentication method (`OIDC` or `SAML`) | |
| Billing method (`Azure` or `GitHub direct`) | |
| Pilot user or pilot group for authentication testing | |
| Pilot group for SCIM provisioning | |
| Azure subscription name or ID, if using Azure billing | |
| Copilot pilot team, if enabling Copilot | |

### Dependencies by phase

| Area | Dependencies that must exist first |
| --- | --- |
| Authentication | Enterprise created, setup admin secured, chosen auth method confirmed, pilot user or group assigned in the identity provider |
| SCIM provisioning | Authentication path selected, pilot provisioning group created, SCIM token owner identified, secure token storage available |
| Azure billing | Enterprise created, billing method confirmed as Azure, eligible Azure admin and target subscription identified |

### Reference links

- Trial setup: <https://docs.github.com/en/enterprise-cloud@latest/admin/overview/setting-up-a-trial-of-github-enterprise-cloud>
- Data residency onboarding: <https://docs.github.com/en/enterprise-cloud@latest/admin/data-residency/getting-started-with-data-residency-for-github-enterprise-cloud>

---

## Setup Phases

### Phase 1 — Create the enterprise

**Why this matters**  
The choices you make here determine how your enterprise is hosted, where data is stored, and how users sign in and are managed.

**What to do**

1. Open the enterprise creation page and choose **Get started with managed users**.
2. Under **Data hosting**, choose your `GHE.com` data residency region, or leave the default to host on `GitHub.com`.
3. Enter the enterprise name, slug or subdomain, industry, number of seats, country or region, identity provider, admin name, and work email.
4. Accept the terms and click **Create enterprise**.

**What you should see**

- A confirmation that your enterprise was created
- A welcome email for the setup admin
- If you’re using data residency, the invitation email may take up to an hour to arrive

**Screenshot checkpoint**  
Capture the completed form before final submission. Mask sensitive fields.

---

### Phase 2 — Secure the setup admin

**Why this matters**  
The setup admin account is the primary account used to complete setup and recover access if needed.

**What to do**

1. Open the welcome email in a private or incognito browser window.
2. Set a strong password.
3. Enable two-factor authentication (2FA).
4. Store recovery codes in your approved secure location.
5. Keep this account available as your emergency recovery access path until pilot validation is complete.

**What you should see**

- Successful sign-in to the enterprise overview
- 2FA enabled for the setup admin

**Screenshot checkpoint**  
Capture the enterprise overview and 2FA confirmation screen. Mask sensitive values.

---

### Phase 3 — Configure authentication

**Why this matters**  
Managed users cannot access GitHub until authentication is configured and working correctly.

**Choose your path**

- Prefer `OIDC` if you use Microsoft Entra ID and do not have a policy requirement for `SAML`.
- Use `SAML` if your organization requires it or your identity architecture is already standardized on SAML.

**Reference links**

- [OIDC for EMU](https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/configuring-authentication-for-enterprise-managed-users/configuring-oidc-for-enterprise-managed-users)
- [SAML for EMU](https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/configuring-authentication-for-enterprise-managed-users/configuring-saml-single-sign-on-for-enterprise-managed-users)
- [Entra SAML walkthrough](https://learn.microsoft.com/en-us/entra/identity/saas-apps/github-enterprise-managed-user-tutorial)

**Before you save any authentication change**

1. Limit assignments to one pilot user or a small pilot group.
2. Keep the setup admin signed in on a separate browser session.
3. Record the exact enterprise slug or subdomain you are using in the identity provider values.
4. Use the GitHub test flow before broad rollout.

**OIDC path**

1. Follow the GitHub OIDC guide for your identity provider and create the required enterprise application or federation configuration.
2. Assign only the pilot user or pilot group.
3. Enter the required issuer and client details in GitHub exactly as documented for your enterprise.
4. Run the GitHub authentication test with a pilot user.
5. Save and enable the configuration only after the test succeeds.

**SAML path**

1. In Entra ID, create the **GitHub Enterprise Managed User** enterprise application.
2. Assign at least one pilot user as **Enterprise Owner**.
3. Configure the basic SAML values using your enterprise slug.
4. Copy the Login URL, Entra Identifier, and certificate into the GitHub SAML settings.
5. Run the SSO test from GitHub, then save and enable the configuration only after the test succeeds.

**What you should see**

- GitHub confirms the provider settings are valid
- A pilot user completes the test sign-in and lands in the enterprise successfully

**Testing note before broader rollout**  
Do not expand assignments until at least one pilot user can sign in successfully, reach the enterprise, and complete the expected managed-user flow.

**Recovery or rollback if the auth test fails**

- Do not broaden user assignments or start SCIM rollout.
- Keep the setup admin session open while you troubleshoot.
- Correct the identity provider values or user assignments, then rerun the test.
- If you already enabled the configuration and pilot access fails, use the setup admin session to revert the recent authentication change before continuing.

**Screenshot checkpoint**  
Capture the identity provider configuration page and the GitHub authentication validation success message.

---

### Phase 4 — Configure SCIM provisioning

**Why this matters**  
SCIM automatically creates and updates managed users and group memberships from your identity provider.

**Reference links**

- [SCIM provisioning](https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/provisioning-user-accounts-with-scim/configuring-scim-provisioning-for-users)
- [Entra SCIM tutorial](https://learn.microsoft.com/en-us/entra/identity/saas-apps/github-enterprise-managed-user-provisioning-tutorial)

**Before you start**

- Decide who owns the SCIM token. For first-time setup, use a documented admin-owned pattern so the token is not tied to an unknown or temporary owner.
- Store the token in your approved secret storage system.
- Record when the token was created, who owns it, and when it must be rotated.
- Keep provisioning scoped to the pilot group until create, update, and access behavior are validated.

**What to do**

1. Create a classic PAT as the setup user with at least the `scim:enterprise` scope.
2. In the identity provider application, open **Provisioning** and set the mode to **Automatic**.
3. Set the tenant URL:
   - `GitHub.com` EMU: `https://api.github.com/scim/v2/enterprises/{enterprise}`
   - `GHE.com` EMU: `https://api.{subdomain}.ghe.com/scim/v2/enterprises/{subdomain}`
4. Paste the PAT as the secret token.
5. Click **Test Connection**, save the configuration, then start provisioning.
6. Keep user and group scoping limited to the pilot assignment.
7. Use **Provision on demand** for your pilot users first.

**What you should see**

- The test connection succeeds
- Pilot users are provisioned and visible in enterprise **People**
- Pilot group membership updates are reflected as expected

**Explicit validation steps**

1. Provision one pilot user on demand and confirm the managed user appears in enterprise **People**.
2. Provision a second pilot user, if available, to confirm repeatability.
3. Change pilot group membership in the identity provider and confirm the update is reflected.
4. Before broad rollout, run one controlled remove-or-unassign test for a noncritical pilot account and verify the expected lifecycle result in GitHub, such as the account being deprovisioned, suspended, or otherwise updated according to your identity provider flow.

**Screenshot checkpoint**  
Capture the SCIM test success screen and the GitHub enterprise **People** list with pilot users.

---

### Phase 5 — Connect Azure subscription *(if using Azure billing)*

**Why this matters**  
This step connects your billing relationship so charges and invoicing behave as expected.

**Reference links**

- [Azure billing concepts](https://docs.github.com/en/enterprise-cloud@latest/billing/concepts/azure-subscriptions)
- [Connect Azure subscription](https://docs.github.com/en/enterprise-cloud@latest/billing/how-tos/set-up-payment/connect-azure-sub)

**What to do**

1. In the GitHub enterprise, go to **Billing and licensing** > **Payment information**.
2. Click **Connect Azure subscription**.
3. Authenticate with an eligible Azure admin account.
4. Select the subscription and confirm.

**What you should see**

- The subscription is connected and visible in billing settings

**Screenshot checkpoint**  
Capture the connected Azure subscription summary screen.

---

### Phase 6 — Enable GitHub Copilot *(optional)*

**Why this matters**  
Enterprise policy and team mapping help you manage access consistently without assigning seats one at a time.

**Recommended timing**  
For first-time setup, enable Copilot after authentication and SCIM validation unless Copilot access is part of the pilot objective.

**Reference links**

- [Set up Copilot for enterprise](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/set-up-copilot/enable-copilot/set-up-for-enterprise)
- [Grant access](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-access/grant-access)

**What to do**

1. Enable Copilot at the enterprise policy level.
2. Create enterprise teams for Copilot users.
3. Map the teams to identity provider groups for lifecycle automation.
4. Assign Copilot licenses to those teams.
5. Confirm that pilot users can sign in from the IDE and use Copilot.

**What you should see**

- Seats are assigned to mapped team members
- Pilot users can access Copilot in the IDE

**Screenshot checkpoint**  
Capture the Copilot policy enabled screen and the team license assignment screens.

---

## Validation Procedure

Run this procedure before you move from pilot setup to broader rollout.

| Step | What to test | Expected result | Evidence to capture |
| --- | --- | --- | --- |
| 1 | Sign in as the setup admin | Setup admin can still access the enterprise and recovery path is intact | Enterprise overview and 2FA confirmation |
| 2 | Sign in as one pilot managed user through the configured auth flow | Pilot user reaches the enterprise successfully | Successful sign-in screen or enterprise landing page |
| 3 | Provision one pilot user on demand through SCIM | Managed user appears in enterprise **People** | SCIM success screen and **People** entry |
| 4 | Update pilot group membership | Membership change is reflected as expected | Identity provider assignment change and resulting GitHub state |
| 5 | Connect billing, if applicable | Billing method is connected and visible | Billing summary page |
| 6 | Validate Copilot, if applicable | Pilot user can access Copilot in the IDE | Copilot policy screen and IDE confirmation |

### Concise go/no-go checklist

| Validation item | Status | Evidence |
| --- | --- | --- |
| Setup admin secured with 2FA and recovery codes | | |
| Authentication works for a pilot managed user | | |
| SCIM provisioning works for pilot users and groups | | |
| Billing method connected and confirmed, if using Azure billing | | |
| Copilot seats assigned and active in the IDE, if enabled | | |

If any item is incomplete, resolve it before broader rollout.

---

## Common Pitfalls and Diagnostics

Use this table as a quick diagnostics aid. Start with the first checks before changing multiple settings at once.

| Symptom | Check first | Where to look | Likely cause | What to do |
| --- | --- | --- | --- | --- |
| SSO test fails or returns unauthorized | Confirm the pilot user is assigned to the app and has the correct role | Identity provider app assignments and GitHub auth test | Pilot user not assigned or wrong role | Assign the test user with the **Enterprise Owner** role if required, then retry |
| SAML settings are rejected | Recheck every URL and entity value for exact format | Identity provider SAML settings and GitHub SAML page | URL format mismatch or trailing slash issue | Re-enter the Identifier, Reply URL, and Sign-on URL exactly as documented for your enterprise slug |
| OIDC test fails | Recheck issuer and client values and confirm pilot assignment scope | Identity provider OIDC app settings and GitHub OIDC page | Issuer or client mismatch, or wrong user scope | Correct the values, keep testing limited to the pilot assignment, and rerun the test |
| SCIM test connection fails | Validate the tenant URL and PAT scope first | Identity provider provisioning page and token details | Wrong tenant URL, wrong token scope, or expired PAT | Correct the tenant URL pattern and recreate the PAT with `scim:enterprise` |
| Users are not provisioning | Confirm provisioning was started and pilot scoping is correct | Identity provider provisioning logs and GitHub enterprise **People** | Provisioning not started, wrong scoping, or sync delay | Start provisioning, check scope assignments, and use **Provision on demand** |
| Managed user cannot sign in | Confirm the user is using the managed identity path, not a personal GitHub account | Pilot user sign-in flow and enterprise **People** | User is using the wrong username or sign-in flow | Use the managed username and the identity provider sign-in flow |
| Azure subscription cannot connect | Confirm Azure admin consent rights and target subscription access | Azure admin session and GitHub billing page | Missing tenant-wide admin consent or subscription access | Use an eligible Azure admin account or complete the consent workflow |
| Copilot seat assigned but no IDE access | Confirm enterprise policy, team mapping, and pilot team membership | Copilot policy, enterprise team membership, and IDE sign-in | Team mapping incomplete or policy not enabled | Verify team membership, enterprise policy, and seat assignment |

When troubleshooting, capture the failing screen, the exact identity provider assignment used for testing, and the time of the test so you can compare it with provisioning or authentication logs.

---

## Go-Live Readiness and Immediate Next Steps

### Ready for broader rollout when

- The setup admin path is secured and documented.
- Pilot authentication succeeds for at least one managed user.
- SCIM create and update behavior is validated for the pilot scope.
- Billing is connected and confirmed, if applicable.
- Optional Copilot access is working for the pilot users, if enabled.
- Required evidence and screenshots are stored for handoff or signoff.

### Immediate next operational steps

1. Confirm network allowlists for `GHE.com` endpoints, if using data residency.
2. Review any feature differences for data residency before broader rollout.
3. Publish an internal admin SOP for SCIM token rotation, certificate refresh if using `SAML`, and setup-admin recovery.
4. Decide who owns day-2 operations for identity, billing, and enterprise policy changes.
5. Stand up enterprise organizations and baseline policy sets.
6. Expand from the pilot group in controlled stages rather than all at once.
7. Roll out GitHub Copilot at scale and GitHub Advanced Security only after the core enterprise path is stable.
