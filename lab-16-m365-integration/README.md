# ☁️ Lab 16 — Microsoft 365 Integration

![Splunk](https://img.shields.io/badge/Splunk_Enterprise-000000?style=for-the-badge&logo=splunk&logoColor=white)
![Microsoft 365](https://img.shields.io/badge/Microsoft_365-D83B01?style=for-the-badge&logo=microsoftoffice&logoColor=white)
![Entra ID](https://img.shields.io/badge/Entra_ID-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white)
![Cloud Logs](https://img.shields.io/badge/Cloud_Audit_Logs-1D9E75?style=for-the-badge)

> **Pulling cloud audit logs into Splunk.** Microsoft 365 is where most enterprise work happens — email, files, Teams, logins. This lab connects Splunk to the M365 audit trail using an Entra ID app registration and the Office 365 Management Activity API, so sign-ins, mailbox activity, file access, and admin changes all become searchable.

---

## 🎯 Introduction — Why Integrate Microsoft 365?

Microsoft 365 audit logs capture user logins, email activity, file access, Teams messages, and admin changes. Those events are essential for security monitoring, compliance, and incident response.

Splunk collects them through the **Office 365 Management Activity API**, which needs an **Entra ID (Azure AD) app registration** — an identity Splunk uses to authenticate — with the right API permissions. No forwarder is involved; Splunk pulls the data directly from Microsoft's cloud.

> 🔐 **Security note:** This integration uses a Client ID, Tenant ID, and Client Secret. The secret is a live credential — treat it like a password: store it in a vault, never share it, and **never commit it to GitHub or any repo.** (Screenshots in this lab have these values redacted for exactly this reason.)

---

## 🏗️ How the Integration Works

```
   ┌────────────────────────────┐
   │   Microsoft 365 Cloud      │
   │   Exchange · SharePoint ·  │
   │   Teams · Entra ID sign-ins│
   └────────────────────────────┘
                │
                │  Management Activity API
                │  (auth: Tenant ID + Client ID + Secret)
                ▼
   ┌────────────────────────────┐
   │   Splunk Add-on for O365   │
   │   pulls audit events       │
   │   every 5 min → index=o365 │
   └────────────────────────────┘

   Entra ID App Registration provides the credentials.
   Admin consent grants the ActivityFeed permissions.
```

---

## 🎓 Learning Objectives

After this lab, you will be able to:

- Register an application in Microsoft Entra ID
- Configure API permissions for the Management Activity API
- Generate a client secret for authentication
- Install and configure the Splunk Add-on for Office 365
- Set up M365 log inputs and verify audit logs reach Splunk

**Prerequisites:** An M365 tenant with audit logging enabled, Global/Application Administrator role, Splunk running (Labs 1–3), ports 8089/9997 open (Lab 12).

---

## Task 1 — Register an App in Entra ID

1. Go to [portal.azure.com](https://portal.azure.com) and sign in with a Global Administrator account.
2. Search **Entra ID** → open **Microsoft Entra ID**.
3. Left menu → **App registrations** → **+ New registration**.

![New registration](screenshots/step02-new-registration.png)

4. Fill in the form:

| Field | Value |
|---|---|
| Name | `Splunk-M365-Integration` |
| Supported account types | Single tenant (this org only) |
| Redirect URI | Leave blank |

5. Click **Register**.

![App registration form](screenshots/step01-app-registration.png)

6. On the overview page, copy and securely save three values: **Application (client) ID**, **Directory (tenant) ID**, and **Object ID**. You'll need the first two in Task 5.

![App overview — IDs redacted](screenshots/step04-app-overview.png)

> 📌 In the screenshot above, the real IDs are blacked out. In your own setup, copy them into a secure note or password manager — not a public document.

**✅ Checkpoint:** The app is registered and you've saved the Client ID and Tenant ID.

---

## Task 2 — Configure API Permissions

7. On the app page → **API permissions** → **+ Add a permission**.

![Add a permission](screenshots/step03-api-permissions-add.png)

8. Choose **Office 365 Management APIs**.

![Office 365 Management APIs](screenshots/step04-office365-apis.png)

9. Click **Application permissions** (not Delegated — Splunk runs with no user logged in).
10. Expand **ActivityFeed** and check:
    - `ActivityFeed.Read` — read your org's activity data
    - `ActivityFeed.ReadDlp` — read DLP policy events
11. Expand **ServiceHealth** and check `ServiceHealth.Read`.
12. Click **Add permissions**.
13. Click **Grant admin consent for [Your Org]** → **Yes**.

![Grant admin consent](screenshots/step05-grant-consent.png)

14. Verify each permission shows a green check and "Granted."

![Consent granted 1](screenshots/step06-consent-granted-1.png)

![Consent granted 2](screenshots/step07-consent-granted-2.png)

**✅ Checkpoint:** All three permissions show Status: Granted.

---

## Task 3 — Create a Client Secret

15. On the app page → **Certificates & secrets** → **+ New client secret**.

![New client secret](screenshots/step08-new-client-secret.png)

16. Description `Splunk-M365-Secret`, Expires 24 months → **Add**.

> ⚠️ **The secret VALUE is shown only once.** Copy the **Value** (not the Secret ID) immediately and store it securely. Treat it like a password — never commit it to version control.

![Secret created — value redacted](screenshots/step11-secret-created.png)

17. **(Optional) Assign the Reader role** if your setup needs directory-level read access for detailed collection.

![Reader role 1](screenshots/step09-reader-role-1.png)

![Reader role 2](screenshots/step10-reader-role-2.png)

![Reader role 3](screenshots/step11-reader-role-3.png)

![Reader role 4](screenshots/step12-reader-role-4.png)

**✅ Checkpoint:** The client secret is created and its value saved securely.

---

## Task 4 — Install the Splunk Add-on for Office 365

18. Splunk Web → **Apps → Find More Apps** → search **Splunk Add-on for Microsoft Office 365** → **Install** (authenticate with your Splunk.com account).

![Install add-on 1](screenshots/step13-install-addon-1.png)

![Install add-on 2](screenshots/step14-install-addon-2.png)

![Install add-on 3](screenshots/step15-install-addon-3.png)

19. Restart when prompted:

```bash
sudo systemctl restart Splunkd
```

**✅ Checkpoint:** The add-on appears in Manage Apps.

---

## Task 5 — Configure the Tenant in Splunk

20. Open **Apps → Splunk Add-on for Microsoft Office 365 → Configuration** → **Add**.
21. Fill in:

| Field | Value |
|---|---|
| Name | `MyOrgM365` |
| Tenant ID | Directory (tenant) ID from Task 1 |
| Client ID | Application (client) ID from Task 1 |
| Client Secret | The secret value from Task 3 |
| Endpoint | Worldwide (default) |

22. Click **Add**.

![Tenant config form — IDs redacted](screenshots/step17-tenant-config-form.png)

![Add configuration](screenshots/step18-config-add.png)

23. A green status means authentication succeeded.

![Tenant green status](screenshots/step19-tenant-green-status.png)

> ⚠️ Red / "Authentication failed"? Re-check the Client ID, Tenant ID, and Secret, and confirm admin consent was granted (Task 2).

**✅ Checkpoint:** The tenant shows green/active status.

---

## Task 6 — Configure Inputs and Verify

24. In the add-on → **Inputs** → **Create New Input**. Choose which M365 feeds to collect:

| Content Type | What it collects |
|---|---|
| `Audit.AzureActiveDirectory` | Sign-ins, MFA, password changes, admin actions |
| `Audit.Exchange` | Email activity, mailbox access, forwarding rules |
| `Audit.SharePoint` | File access, sharing, downloads (SharePoint/OneDrive) |
| `Audit.General` | Teams, Forms, Power Apps, other services |
| `DLP.All` | Data Loss Prevention policy matches |

25. For each input set: a name (e.g. `m365azure`), Tenant `MyOrgM365`, the content type, Index `o365` (create it first), Interval `300`.

![Inputs config 1](screenshots/step20-inputs-config-1.png)

![Inputs config 2](screenshots/step21-inputs-config-2.png)

26. Wait ~10 minutes, then search:

```
index=o365 | head 20
index=o365 | stats count by sourcetype | sort -count
```

![O365 results 1](screenshots/step22-o365-results-1.png)

![O365 results 2](screenshots/step23-o365-results-2.png)

> 💡 The Management Activity API can take up to 12 hours to activate a new subscription. If nothing appears in the first hour, wait and recheck. To check for API errors: `index=_internal source="*ta_ms_o365*" (error OR warning)`.

**✅ Checkpoint:** M365 audit events appear in the `o365` index.

---

## 🛠️ Troubleshooting

| Problem | Solution |
|---|---|
| Tenant status red | Re-check Client ID, Tenant ID, Secret; confirm admin consent granted |
| No data after 1 hour | Normal — the API has up to a 12-hour activation delay; wait |
| "Authentication failed" | Secret may be expired or mistyped; regenerate and re-enter |
| Some feeds empty | That activity may not have occurred yet, or the content type isn't enabled |

---

## 🧠 Conclusion — What You Learned

This lab connected Splunk to the cloud — no agent, just API authentication.

**About Cloud Log Collection**
- M365 audit logs come in via the Management Activity API, not a forwarder
- An Entra ID app registration is the identity Splunk uses to authenticate
- Admin consent is what actually grants the app permission to read the logs

**About Credential Security**
- Client secrets are live credentials — vault them, rotate them, never commit them
- This is why every credential in this lab's screenshots is redacted
- Revoking a secret in Entra ID instantly invalidates it everywhere

**Why This Matters**
- M365 is the top target for identity attacks (phishing, MFA fatigue, mailbox rules)
- Sign-in and mailbox audit logs are where you detect account compromise
- Cloud log integration is a core skill for modern, cloud-first SOCs

---

## ✅ Verify Before Moving On

- [ ] App registered in Entra ID with the three API permissions granted
- [ ] Client secret created and stored securely (not in any repo)
- [ ] The O365 add-on installed and the tenant shows green status
- [ ] Inputs configured for the feeds you want
- [ ] `index=o365` returns audit events

---

**Next:** [Lab 17 →](../lab-17/)

[← Back to lab index](../README.md)

---

## 👤 Author

**Nasrin Ismet**
SOC Analyst & GRC Consultant | M.S. Cybersecurity

*"Find it, prove it, govern it."*

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/kismetara/)
[![GitHub](https://img.shields.io/badge/GitHub-Portfolio-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/ismet60)

⭐ *Star this repo if it helped*
