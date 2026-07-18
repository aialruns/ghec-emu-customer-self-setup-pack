# Customer Tutorial: Self-Serve Setup for GitHub Enterprise Cloud (EMU + Data Residency)

> Use this tutorial to complete setup without live GitHub hand-holding.  
> **Estimated time:** 90-150 minutes for a first tenant setup.

## 0. Before You Start (Preflight)

### Why this matters

Most setup failures come from missing permissions, missing values, or completing steps in the wrong order.

### Required roles

- GitHub enterprise setup admin from the welcome email
- Microsoft Entra role: Cloud Application Administrator or equivalent
- Azure role that can grant tenant-wide admin consent, if you are using Azure billing

### Required values worksheet

| Item | Your value |
| --- | --- |
| Enterprise name |  |
| Enterprise slug or subdomain |  |
| Hosting choice (`GitHub.com` or `GHE.com`) |  |
| Data residency region, if using `GHE.com` |  |
| Identity provider (`Entra`, `Okta`, `PingFederate`, or other) |  |
| Authentication method (`OIDC` or `SAML`) |  |
| Billing method (`Azure` or `GitHub direct`) |  |
| Pilot user group for provisioning |  |

### Reference links

- Trial setup: <https://docs.github.com/en/enterprise-cloud@latest/admin/overview/setting-up-a-trial-of-github-enterprise-cloud>
- Data residency onboarding: <https://docs.github.com/en/enterprise-cloud@latest/admin/data-residency/getting-started-with-data-residency-for-github-enterprise-cloud>

---

## 1. Create the enterprise (managed users)

### Why this matters

Your enterprise type and hosting choice determine the authentication model, data location, and future management pattern.

### What to click

1. Open <https://github.com/account/enterprises/new?ref_product=ghec&ref_type=trial&ref_style=button&ref_plan=enterprise>.
2. Choose **Get started with managed users**.
3. Under **Data hosting**, choose your desired `GHE.com` data residency region from the dropdown, or leave the default option to host on `GitHub.com` without data residency.
4. Complete the enterprise name, slug or subdomain, industry, number of seats, country or region, identity provider, admin name, and work email.
5. Accept the trial terms.
6. Click **Create enterprise**.

### What you should see

- Enterprise creation confirmation
- Welcome email for the setup admin
- If using data residency, the invitation email may take up to an hour to arrive

### Screenshot checkpoint

Capture a screenshot of the completed form before final submission. Mask sensitive fields.

---

## 2. Secure the setup admin

### Why this matters

The setup admin is your break-glass identity for authentication, provisioning changes, and recovery.

### What to click

1. Open the setup email in a private or incognito browser window.
2. Set a password for the setup admin.
3. Enable 2FA and store recovery codes securely.

### What you should see

- Successful sign-in to the enterprise overview
- 2FA enabled for the setup user

### Screenshot checkpoint

Capture the enterprise overview and the 2FA confirmation screen. Mask sensitive values.

---

## 3. Configure authentication (OIDC or SAML)

### Why this matters

Managed users cannot access enterprise resources until authentication is configured and working.

### Recommended path

- For Microsoft Entra ID, prefer `OIDC` unless `SAML` is required by policy.

### References

- OIDC: <https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/configuring-authentication-for-enterprise-managed-users/configuring-oidc-for-enterprise-managed-users>
- SAML: <https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/configuring-authentication-for-enterprise-managed-users/configuring-saml-single-sign-on-for-enterprise-managed-users>
- Entra SAML walkthrough: <https://learn.microsoft.com/en-us/entra/identity/saas-apps/github-enterprise-managed-user-tutorial>

### What to click (SAML path)

1. In Entra ID, create the **GitHub Enterprise Managed User** enterprise application.
2. Assign at least one pilot user as **Enterprise Owner**.
3. Configure the basic SAML values using your enterprise slug.
4. Copy the Login URL, Entra Identifier, and certificate into the GitHub SAML settings.
5. Run the SSO test from GitHub, then save and enable the configuration.

### What you should see

- GitHub confirms that the provider settings are valid
- SSO is enabled

### Screenshot checkpoint

Capture:

- The Entra SAML configuration page
- The GitHub SSO validation success message

---

## 4. Configure SCIM provisioning

### Why this matters

SCIM creates and updates managed users and group memberships from your identity provider.

### References

- GitHub SCIM: <https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/provisioning-user-accounts-with-scim/configuring-scim-provisioning-for-users>
- Entra SCIM tutorial: <https://learn.microsoft.com/en-us/entra/identity/saas-apps/github-enterprise-managed-user-provisioning-tutorial>

### What to click

1. Create a classic PAT as the setup user with at least the `scim:enterprise` scope.
2. In the Entra application, open **Provisioning** and set the mode to **Automatic**.
3. Set the tenant URL:
   - `GitHub.com` EMU: `https://api.github.com/scim/v2/enterprises/{enterprise}`
   - `GHE.com` EMU: `https://api.{subdomain}.ghe.com/scim/v2/enterprises/{subdomain}`
4. Paste the PAT as the secret token.
5. Click **Test Connection**, save the configuration, then start provisioning.
6. Use **Provision on demand** for pilot users.

### What you should see

- Test connection successful
- Pilot users provisioned and visible in enterprise **People**

### Screenshot checkpoint

Capture the SCIM test success screen and the GitHub enterprise **People** list with pilot users.

---

## 5. Connect the Azure subscription

### Why this matters

Billing and invoicing behavior changes after the Azure connection is configured.

### References

- Azure billing concepts: <https://docs.github.com/en/enterprise-cloud@latest/billing/concepts/azure-subscriptions>
- Connect Azure subscription: <https://docs.github.com/en/enterprise-cloud@latest/billing/how-tos/set-up-payment/connect-azure-sub>

### What to click

1. In the GitHub enterprise, go to **Billing and licensing** > **Payment information**.
2. Click **Connect Azure subscription**.
3. Authenticate with an eligible Azure admin account.
4. Select the subscription and confirm.

### What you should see

- The subscription is connected and visible in billing settings

### Screenshot checkpoint

Capture the connected Azure subscription summary screen.

---

## 6. If also enabling GitHub Copilot

### Why this matters

Policy and team mapping enable repeatable access management without manual seat-by-seat operations.

### References

- Enterprise Copilot setup: <https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/set-up-copilot/enable-copilot/set-up-for-enterprise>
- Grant access: <https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-access/grant-access>

### What to click

1. Enable Copilot at the enterprise policy level.
2. Create enterprise teams for Copilot users.
3. Map the teams to identity provider groups for lifecycle automation.
4. Assign Copilot licenses to those teams.
5. Validate that pilot users can sign in from the IDE and use Copilot.

### What you should see

- Seats consumed for mapped team members
- Pilot users can access Copilot in the IDE

### Screenshot checkpoint

Capture the Copilot policy enabled screen and the team license assignment screens.

---

## 7. Validation and go-live

### Complete this table

| Validation | Status | Evidence |
| --- | --- | --- |
| Setup admin secured with 2FA and recovery codes |  |  |
| SSO works for a pilot managed user |  |  |
| SCIM provisioning works for pilot users and groups |  |  |
| Billing method connected and confirmed |  |  |
| Copilot seats assigned and active in the IDE |  |  |

If any row fails, resolve it through the troubleshooting section before go-live.

---

## Troubleshooting playbook

| Symptom | Likely cause | Fix | Verify |
| --- | --- | --- | --- |
| SSO test fails or returns unauthorized | Pilot user not assigned in the Entra app or wrong role | Assign the test user and the **Enterprise Owner** role, then retry the SSO test | GitHub SSO validation succeeds |
| SAML settings are rejected | URL format mismatch or trailing slash issue | Re-enter the Identifier, Reply URL, and Sign-on URL exactly as documented for your slug | Save succeeds and the test passes |
| SCIM test connection fails | Wrong tenant URL or token scope, or expired token | Correct the tenant URL pattern, recreate the PAT with `scim:enterprise`, and retest | **Test Connection** succeeds |
| Users are not provisioning | Provisioning not started, wrong scoping, or sync delay | Start provisioning, check scope assignments, and run **Provision on demand** | User appears in enterprise **People** |
| Managed user cannot sign in | User is using an email or personal GitHub username instead of the managed username | Use the managed username and the identity provider sign-in flow | User reaches the enterprise |
| Azure subscription cannot connect | Missing tenant-wide admin consent rights | Use an eligible Azure admin account or complete the consent workflow | Subscription appears connected |
| Copilot seat assigned but no IDE access | Team mapping incomplete or policy not enabled | Verify team membership, enterprise policy, and seat assignment | Copilot is active in the IDE |

---

## Optional hardening after go-live

1. Confirm network allowlists for `GHE.com` endpoints, if using data residency.
2. Review feature differences for data residency before broad rollout.
3. Add an internal admin SOP for token rotation and certificate refresh.
