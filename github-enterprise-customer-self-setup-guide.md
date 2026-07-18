# GitHub Enterprise Cloud: Customer Self-Setup Guide

**GitHub Enterprise Cloud with Enterprise Managed Users (EMU) and Data Residency**

> **Estimated time:** 90–150 minutes for a first tenant setup.

---

## Overview

This guide walks your team through self-service setup of GitHub Enterprise Cloud (GHEC) with Enterprise Managed Users (EMU). It covers the setup flow in the order most customers should complete it so your enterprise is authenticated, provisioned, billed, and validated for pilot users before go-live.

Use this guide if you want a single document that is concise, actionable, and easy to follow without live GitHub hand-holding.

---

## Prerequisites

Missing roles, values, or permissions is the most common cause of setup delays.

### Required roles

| Role | Who needs it |
| --- | --- |
| GitHub enterprise setup admin (from the welcome email) | Your GitHub admin |
| Microsoft Entra: Cloud Application Administrator or equivalent | Your identity admin |
| Azure: permission to grant tenant-wide admin consent | Your Azure admin, if using Azure billing |

### Required values

| Item | Your value |
| --- | --- |
| Enterprise name | |
| Enterprise slug or subdomain | |
| Hosting choice (`GitHub.com` or `GHE.com`) | |
| Data residency region, if using `GHE.com` | |
| Identity provider (`Entra`, `Okta`, `PingFederate`, or other) | |
| Authentication method (`OIDC` or `SAML`) | |
| Billing method (`Azure` or `GitHub direct`) | |
| Pilot user group for provisioning | |

### Reference links

- Trial setup: <https://docs.github.com/en/enterprise-cloud@latest/admin/overview/setting-up-a-trial-of-github-enterprise-cloud>
- Data residency onboarding: <https://docs.github.com/en/enterprise-cloud@latest/admin/data-residency/getting-started-with-data-residency-for-github-enterprise-cloud>

---

## Setup Phases

### Phase 1 — Create the enterprise

**Why this matters**  
Your enterprise type and hosting choice determine the authentication model, data location, and future management pattern.

**What to do**

1. Open the enterprise creation page and choose **Get started with managed users**.
2. Under **Data hosting**, choose your `GHE.com` data residency region or leave the default to host on `GitHub.com`.
3. Complete the enterprise name, slug or subdomain, industry, number of seats, country or region, identity provider, admin name, and work email.
4. Accept the terms and click **Create enterprise**.

**What you should see**

- Enterprise creation confirmation
- Welcome email for the setup admin
- If using data residency, the invitation email may take up to an hour to arrive

**Screenshot checkpoint**  
Capture the completed form before final submission. Mask sensitive fields.

---

### Phase 2 — Secure the setup admin

**Why this matters**  
The setup admin is your break-glass identity for authentication, provisioning changes, and recovery.

**What to do**

1. Open the welcome email in a private or incognito browser window.
2. Set a strong password.
3. Enable two-factor authentication (2FA).
4. Store recovery codes securely.

**What you should see**

- Successful sign-in to the enterprise overview
- 2FA enabled for the setup user

**Screenshot checkpoint**  
Capture the enterprise overview and 2FA confirmation screen. Mask sensitive values.

---

### Phase 3 — Configure authentication

**Why this matters**  
Managed users cannot access enterprise resources until authentication is configured and working.

**Recommended path**  
For Microsoft Entra ID, prefer `OIDC` unless `SAML` is required by policy.

**Reference links**

- [OIDC for EMU](https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/configuring-authentication-for-enterprise-managed-users/configuring-oidc-for-enterprise-managed-users)
- [SAML for EMU](https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/configuring-authentication-for-enterprise-managed-users/configuring-saml-single-sign-on-for-enterprise-managed-users)
- [Entra SAML walkthrough](https://learn.microsoft.com/en-us/entra/identity/saas-apps/github-enterprise-managed-user-tutorial)

**If using SAML**

1. In Entra ID, create the **GitHub Enterprise Managed User** enterprise application.
2. Assign at least one pilot user as **Enterprise Owner**.
3. Configure the basic SAML values using your enterprise slug.
4. Copy the Login URL, Entra Identifier, and certificate into the GitHub SAML settings.
5. Run the SSO test from GitHub, then save and enable the configuration.

**What you should see**

- GitHub confirms the provider settings are valid
- SSO is enabled

**Screenshot checkpoint**  
Capture the Entra SAML configuration page and the GitHub SSO validation success message.

---

### Phase 4 — Configure SCIM provisioning

**Why this matters**  
SCIM creates and updates managed users and group memberships from your identity provider.

**Reference links**

- [SCIM provisioning](https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/provisioning-user-accounts-with-scim/configuring-scim-provisioning-for-users)
- [Entra SCIM tutorial](https://learn.microsoft.com/en-us/entra/identity/saas-apps/github-enterprise-managed-user-provisioning-tutorial)

**What to do**

1. Create a classic PAT as the setup user with at least the `scim:enterprise` scope.
2. In the Entra application, open **Provisioning** and set the mode to **Automatic**.
3. Set the tenant URL:
   - `GitHub.com` EMU: `https://api.github.com/scim/v2/enterprises/{enterprise}`
   - `GHE.com` EMU: `https://api.{subdomain}.ghe.com/scim/v2/enterprises/{subdomain}`
4. Paste the PAT as the secret token.
5. Click **Test Connection**, save the configuration, then start provisioning.
6. Use **Provision on demand** for pilot users.

**What you should see**

- Test connection succeeds
- Pilot users are provisioned and visible in enterprise **People**

**Screenshot checkpoint**  
Capture the SCIM test success screen and the GitHub enterprise **People** list with pilot users.

---

### Phase 5 — Connect Azure subscription *(if using Azure billing)*

**Why this matters**  
Billing and invoicing behavior changes after the Azure connection is configured.

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
Policy and team mapping enable repeatable access management without manual seat-by-seat operations.

**Reference links**

- [Set up Copilot for enterprise](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/set-up-copilot/enable-copilot/set-up-for-enterprise)
- [Grant access](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-access/grant-access)

**What to do**

1. Enable Copilot at the enterprise policy level.
2. Create enterprise teams for Copilot users.
3. Map the teams to identity provider groups for lifecycle automation.
4. Assign Copilot licenses to those teams.
5. Validate that pilot users can sign in from the IDE and use Copilot.

**What you should see**

- Seats are consumed for mapped team members
- Pilot users can access Copilot in the IDE

**Screenshot checkpoint**  
Capture the Copilot policy enabled screen and the team license assignment screens.

---

## Validation Checklist

Complete all rows before declaring go-live.

| Validation item | Status | Evidence |
| --- | --- | --- |
| Setup admin secured with 2FA and recovery codes | | |
| SSO works for a pilot managed user | | |
| SCIM provisioning works for pilot users and groups | | |
| Billing method connected and confirmed | | |
| Copilot seats assigned and active in the IDE | | |

If any row fails, resolve it before go-live.

---

## Common Pitfalls

| Symptom | Likely cause | Quick fix |
| --- | --- | --- |
| SSO test fails or returns unauthorized | Pilot user not assigned in the Entra app or wrong role | Assign the test user with the **Enterprise Owner** role, then retry |
| SAML settings are rejected | URL format mismatch or trailing slash issue | Re-enter the Identifier, Reply URL, and Sign-on URL exactly as documented for your slug |
| SCIM test connection fails | Wrong tenant URL, wrong token scope, or expired PAT | Correct the tenant URL pattern and recreate the PAT with `scim:enterprise` |
| Users are not provisioning | Provisioning not started, wrong scoping, or sync delay | Start provisioning, check scope assignments, and use **Provision on demand** |
| Managed user cannot sign in | User is using a personal GitHub username instead of the managed username | Use the managed username and the identity provider sign-in flow |
| Azure subscription cannot connect | Missing tenant-wide admin consent rights | Use an eligible Azure admin account or complete the consent workflow |
| Copilot seat assigned but no IDE access | Team mapping incomplete or policy not enabled | Verify team membership, enterprise policy, and seat assignment |

---

## After Go-Live

1. Confirm network allowlists for `GHE.com` endpoints, if using data residency.
2. Review feature differences for data residency before broad rollout.
3. Add an internal admin SOP for PAT rotation and certificate refresh.
4. Stand up enterprise organizations and baseline policy sets.
5. Roll out GitHub Copilot at scale and enable GitHub Advanced Security.
