# Customer Tutorial: Self-Serve Setup for GitHub Enterprise Cloud (EMU + Data Residency)

> Use this tutorial to complete setup without live GitHub hand-holding.  
> Estimated time: 90-150 minutes for first tenant setup.

## 0) Before You Start (Preflight)

### Why this matters

Most setup failures come from missing permissions, missing values, or wrong setup sequence.

### Required roles

- GitHub enterprise setup admin (from welcome email)
- Microsoft Entra role: Cloud Application Administrator or equivalent
- Azure role capable of tenant-wide admin consent (if using Azure billing)

### Required values worksheet

| Item | Your value |
| --- | --- |
| Enterprise name |  |
| Enterprise slug/subdomain |  |
| Hosting choice (`GitHub.com` / `GHE.com`) |  |
| Data residency region (if GHE.com) |  |
| IdP (Entra/Okta/PingFederate/other) |  |
| Auth method (OIDC/SAML) |  |
| Billing method (Azure/GitHub direct) |  |
| Pilot user group for provisioning |  |

### Reference links

- Trial setup: https://docs.github.com/en/enterprise-cloud@latest/admin/overview/setting-up-a-trial-of-github-enterprise-cloud
- Data residency onboarding: https://docs.github.com/en/enterprise-cloud@latest/admin/data-residency/getting-started-with-data-residency-for-github-enterprise-cloud

---

## 1) Create the Enterprise (Managed Users)

### Why this matters

Your enterprise type and hosting choice determine authentication model, data location, and future management pattern.

### What to click

1. Open: https://github.com/account/enterprises/new?ref\_product=ghec&ref\_type=trial&ref\_style=button&ref\_plan=enterprise
2. Choose **Get started with managed users**.
3. Under **Data hosting**, choose your desirec GHE.com data residency region (suggested for government users) from dropdown or leave on "Host on GitHub.com without data residency"
4. Complete enterprise name, slug/subdomain, industry, number of seats, country/region, identity provider, admin name and work email.
5. Accept Trial terms
6. Click **Create enterprise**.

### What you should see

- Enterprise creation confirmation.
- Welcome email for setup admin (Data Residency invitation email can take up to an hour to receive)

### Screenshot checkpoint

Capture a screenshot of the completed form before final submit (mask sensitive fields).

---

## 2) Secure the Setup Admin

### Why this matters

The setup admin is your break-glass identity for auth/provisioning changes and recovery.

### What to click

1. Open setup email in private/incognito browser.
2. Set password for setup admin.
3. Enable 2FA and store recovery codes securely.

### What you should see

- Successful sign-in to enterprise overview.
- 2FA enabled for setup user.

### Screenshot checkpoint

Capture enterprise overview and 2FA enabled confirmation (mask any sensitive values).

---

## 3) Configure Authentication (OIDC or SAML)

### Why this matters

Managed users cannot access enterprise resources until authentication is configured and working.

### Recommended path

- For Entra ID, prefer OIDC unless SAML is required by policy.

### References

- OIDC: https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/configuring-authentication-for-enterprise-managed-users/configuring-oidc-for-enterprise-managed-users
- SAML: https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/configuring-authentication-for-enterprise-managed-users/configuring-saml-single-sign-on-for-enterprise-managed-users
- Entra SAML walkthrough: https://learn.microsoft.com/en-us/entra/identity/saas-apps/github-enterprise-managed-user-tutorial

### What to click (SAML path)

1. In Entra ID, create **GitHub Enterprise Managed User** enterprise app.
2. Assign at least one pilot user as **Enterprise Owner**.
3. Configure Basic SAML values using your enterprise slug.
4. Copy Login URL, Entra Identifier, and cert into GitHub SAML settings.
5. Run SSO test from GitHub, then save and enable.

### What you should see

- GitHub confirms provider settings validated.
- SSO enabled.

### Screenshot checkpoint

Capture:
- Entra SAML configuration page
- GitHub SSO validation success message

---

## 4) Configure SCIM Provisioning

### Why this matters

SCIM creates and updates managed users and group membership lifecycle from your IdP.

### References

- GitHub SCIM: https://docs.github.com/en/enterprise-cloud@latest/admin/managing-iam/provisioning-user-accounts-with-scim/configuring-scim-provisioning-for-users
- Entra SCIM tutorial: https://learn.microsoft.com/en-us/entra/identity/saas-apps/github-enterprise-managed-user-provisioning-tutorial

### What to click

1. Create PAT classic as setup user with at least `scim:enterprise`.
2. In Entra app, open **Provisioning** and set mode to automatic.
3. Set tenant URL:
    - GitHub.com EMU: `https://api.github.com/scim/v2/enterprises/{enterprise}`
    - GHE.com EMU: `https://api.{subdomain}.ghe.com/scim/v2/enterprises/{subdomain}`
4. Paste PAT as secret token.
5. Click **Test Connection**, save, then start provisioning.
6. Use **Provision on demand** for pilot users.

### What you should see

- Test connection successful.
- Pilot users provisioned and visible in enterprise People.

### Screenshot checkpoint

Capture SCIM test success and GitHub enterprise People list with pilot users.

---

## 5) Connect Azure Subscription

### Why this matters

Billing and invoicing behavior changes after Azure connection.

### References

- Azure billing concepts: https://docs.github.com/en/enterprise-cloud@latest/billing/concepts/azure-subscriptions
- Connect Azure subscription: https://docs.github.com/en/enterprise-cloud@latest/billing/how-tos/set-up-payment/connect-azure-sub

### What to click

1. In GitHub enterprise, go to **Billing and licensing** -> **Payment information**.
2. Click **Connect Azure subscription**.
3. Authenticate with eligible Azure admin.
4. Select subscription and confirm.

### What you should see

- Subscription connected and visible in billing settings.

### Screenshot checkpoint

Capture connected Azure subscription summary screen.

---

## 6) (If also enabling GitHub Copilot) - Enable GitHub Copilot and Automate Access

### Why this matters

Policy + team mapping allows repeatable access management without manual seat-by-seat operations.

### References

- Enterprise Copilot setup: https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/set-up-copilot/enable-copilot/set-up-for-enterprise
- Grant access: https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-access/grant-access

### What to click

1. Enable Copilot at enterprise policy level.
2. Create enterprise team(s) for Copilot users.
3. Map team(s) to IdP group(s) for lifecycle automation.
4. Assign Copilot licenses to those teams.
5. Validate pilot users can sign in from IDE and use Copilot.

### What you should see

- Seats consumed for mapped team members.
- Pilot users can access Copilot in IDE.

### Screenshot checkpoint

Capture Copilot policy enabled and team license assignment screens.

---

## 7) Validation and Go-Live

### Complete this table

| Validation | Status | Evidence |
| --- | --- | --- |
| Setup admin secured with 2FA and recovery codes |  |  |
| SSO works for pilot managed user |  |  |
| SCIM provisioning works for pilot users/groups |  |  |
| Billing method connected and confirmed |  |  |
| Copilot seats assigned and active in IDE |  |  |

If any row fails, resolve via troubleshooting section before go-live.

---

## Troubleshooting Playbook

| Symptom | Likely cause | Fix | Verify |
| --- | --- | --- | --- |
| SSO test fails / unauthorized | Pilot user not assigned in Entra app or wrong role | Assign test user and Enterprise Owner role, retry SSO test | GitHub SSO validation success |
| SAML settings rejected | URL format mismatch or trailing slash issue | Re-enter Identifier/Reply/Sign-on URLs exactly per docs and slug | Save succeeds and test passes |
| SCIM test connection fails | Wrong tenant URL or token scope/expired token | Correct tenant URL pattern; recreate PAT with `scim:enterprise`; retest | Test Connection success |
| Users not provisioning | Provisioning not started, wrong scoping, or sync delay | Start provisioning, check scope assignments, run Provision on demand | User appears in enterprise People |
| Managed user cannot sign in | Using email/personal GitHub username instead of managed username format | Use managed username and IdP sign-in flow | User reaches enterprise |
| Azure subscription cannot connect | Missing tenant-wide admin consent rights | Use eligible Azure admin account or consent workflow | Subscription appears connected |
| Copilot seat assigned but no IDE access | Team mapping incomplete or policy not enabled | Verify team membership, enterprise policy, and seat assignment | Copilot active in IDE |

---

## Optional hardening after go-live

1. Confirm network allowlists for GHE.com endpoints (if data residency).
2. Review feature differences for data residency before broad rollout.
3. Add internal admin SOP for token rotation and certificate refresh.
