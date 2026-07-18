# GitHub Enterprise Cloud: Customer Self-Setup Guide

**GitHub Enterprise Cloud with Enterprise Managed Users (EMU) and Data Residency**

> **Estimated time:** 90–150 minutes for a first tenant setup.  
> For the full step-by-step tutorial, see [`customer-self-setup-tutorial.md`](customer-self-setup-tutorial.md).

---

## Overview

This guide walks your team through the self-service setup of GitHub Enterprise Cloud (GHEC) with Enterprise Managed Users (EMU). It covers the critical phases in sequence so your enterprise is authentication-ready, provisioned, and billing-connected before go-live.

---

## Prerequisites

Gather these items before you begin. Missing any one of them is the most common cause of setup delays.

### Required roles

| Role | Who needs it |
| --- | --- |
| GitHub enterprise setup admin (from the welcome email) | Your GitHub admin |
| Microsoft Entra: Cloud Application Administrator or equivalent | Your identity admin |
| Azure: permission to grant tenant-wide admin consent | Your Azure admin (if using Azure billing) |

### Required values

| Item | Your value |
| --- | --- |
| Enterprise name | |
| Enterprise slug or subdomain | |
| Hosting choice (`GitHub.com` or `GHE.com`) | |
| Data residency region (if using `GHE.com`) | |
| Identity provider (`Entra`, `Okta`, `PingFederate`, or other) | |
| Authentication method (`OIDC` or `SAML`) | |
| Billing method (`Azure` or `GitHub direct`) | |
| Pilot user group for provisioning | |

---

## Setup Phases

### Phase 1 — Create the Enterprise

1. Open the enterprise creation page and choose **Get started with managed users**.
2. Select your data residency region (or leave the default for `GitHub.com`).
3. Fill in the enterprise name, slug, industry, seat count, country, identity provider, admin name, and work email.
4. Accept the trial terms and click **Create enterprise**.

> **Note:** If using data residency, the invitation email may take up to an hour to arrive.

---

### Phase 2 — Secure the Setup Admin

The setup admin is your break-glass identity — protect it immediately.

1. Open the welcome email in a private or incognito browser window.
2. Set a strong password.
3. Enable two-factor authentication (2FA) and save recovery codes in a secure location.

---

### Phase 3 — Configure Authentication

Managed users cannot access enterprise resources until authentication is configured.

**Recommended:** Use `OIDC` for Microsoft Entra ID unless `SAML` is required by policy.

**SAML path (summary):**

1. In Entra ID, create the **GitHub Enterprise Managed User** enterprise application.
2. Assign at least one pilot user as **Enterprise Owner**.
3. Configure the basic SAML values using your enterprise slug.
4. Copy the Login URL, Entra Identifier, and certificate into the GitHub SAML settings.
5. Run the SSO test from GitHub and save the configuration.

📖 Reference docs:
- [OIDC for EMU](https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/configuring-authentication-for-enterprise-managed-users/configuring-oidc-for-enterprise-managed-users)
- [SAML for EMU](https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/configuring-authentication-for-enterprise-managed-users/configuring-saml-single-sign-on-for-enterprise-managed-users)

---

### Phase 4 — Configure SCIM Provisioning

SCIM creates and updates managed users and group memberships automatically from your identity provider.

1. Create a classic personal access token (PAT) as the setup user with at least the `scim:enterprise` scope.
2. In the Entra application, open **Provisioning** and set the mode to **Automatic**.
3. Set the tenant URL:
   - `GitHub.com` EMU: `https://api.github.com/scim/v2/enterprises/{enterprise}`
   - `GHE.com` EMU: `https://api.{subdomain}.ghe.com/scim/v2/enterprises/{subdomain}`
4. Paste the PAT as the secret token.
5. Click **Test Connection**, save, and start provisioning.
6. Use **Provision on demand** to validate pilot users.

📖 Reference docs:
- [SCIM provisioning](https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/provisioning-user-accounts-with-scim/configuring-scim-provisioning-for-users)

---

### Phase 5 — Connect Azure Subscription *(if using Azure billing)*

1. In the GitHub enterprise, go to **Billing and licensing** > **Payment information**.
2. Click **Connect Azure subscription**.
3. Authenticate with an eligible Azure admin account.
4. Select the subscription and confirm.

📖 Reference docs:
- [Connect Azure subscription](https://docs.github.com/en/enterprise-cloud@latest/billing/how-tos/set-up-payment/connect-azure-sub)

---

### Phase 6 — Enable GitHub Copilot *(optional)*

1. Enable Copilot at the enterprise policy level.
2. Create enterprise teams for Copilot users.
3. Map the teams to identity provider groups for lifecycle automation.
4. Assign Copilot licenses to those teams.
5. Validate that pilot users can sign in from their IDE and use Copilot.

📖 Reference docs:
- [Set up Copilot for enterprise](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/set-up-copilot/enable-copilot/set-up-for-enterprise)

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

---

## Common Pitfalls

| Symptom | Likely cause | Quick fix |
| --- | --- | --- |
| SSO test fails or returns unauthorized | Pilot user not assigned in the Entra app, or wrong role | Assign the test user with the **Enterprise Owner** role, then retry |
| SAML settings are rejected | URL format mismatch or trailing slash | Re-enter the Identifier, Reply URL, and Sign-on URL exactly as documented for your slug |
| SCIM test connection fails | Wrong tenant URL, wrong token scope, or expired PAT | Correct the tenant URL pattern and recreate the PAT with `scim:enterprise` |
| Users are not provisioning | Provisioning not started, wrong scoping, or sync delay | Start provisioning, check scope, and use **Provision on demand** |
| Managed user cannot sign in | Using a personal GitHub username instead of the managed username | Use the managed username and the identity provider sign-in flow |
| Azure subscription cannot connect | Missing tenant-wide admin consent | Use an eligible Azure admin account or complete the consent workflow |
| Copilot seat assigned but no IDE access | Team mapping incomplete or policy not enabled | Verify team membership, enterprise policy, and seat assignment |

---

## After Go-Live

1. Confirm network allowlists for `GHE.com` endpoints (if using data residency).
2. Review feature differences for data residency before broad rollout.
3. Add an internal admin SOP for PAT rotation and certificate refresh.
4. Stand up enterprise organizations and baseline policy sets.
5. Roll out GitHub Copilot at scale and enable GitHub Advanced Security.

---

*For the complete step-by-step tutorial with detailed screenshots and troubleshooting depth, see [`customer-self-setup-tutorial.md`](customer-self-setup-tutorial.md).*
