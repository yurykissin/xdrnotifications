# 🔒 XDR Security Dashboard Notifications

Automated Azure Logic App that queries Microsoft 365 Defender Advanced Hunting APIs on a schedule and sends a styled HTML security dashboard report via email.
<img width="616" height="474" alt="image" src="https://github.com/user-attachments/assets/54429bc2-8f12-4436-94db-517260b6d048" />

## What Gets Deployed

| Resource | Type | Description |
|----------|------|-------------|
| Logic App | `Microsoft.Logic/workflows` | Runs on schedule, queries Defender APIs, sends email report |
| API Connection | `Microsoft.Web/connections` | Office 365 connection for sending email |

## Dashboard Report Includes

- 🚨 Total Alerts (30 days) — High & Medium severity breakdown
- 🔐 OAuth Applications count
- 👥 Total Identities
- 🦠 Device Malware Events
- 🎣 Phishing Events Analysis
- 🔗 Direct links to Microsoft Security Portal

## Deploy to Azure

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fyurykissin%2Fxdrnotifications%2Fmain%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fyurykissin%2Fxdrnotifications%2Fmain%2FcreateUiDefinition.json)

## Prerequisites

1. **App Registration** in Azure AD with the following:
   - Go to **Azure Portal → Azure Active Directory → App registrations → New registration**
   - Name it (e.g., `XDR-Dashboard-Reader`) and click **Register**
   - Copy the **Application (client) ID** from the Overview page
   - Go to **Certificates & secrets** → **New client secret** → set an expiry → **Add**
   - Copy the **Secret Value** immediately (it won't be shown again)
   - Go to **API permissions** → **Add a permission** → **APIs my organization uses** → search `Microsoft Threat Protection`
   - Select **Application permissions** → check `AdvancedHunting.Read.All` → **Add permissions**
   - Click **Grant admin consent** for your organization
2. **Office 365 mailbox** for the sender email address
3. An Azure subscription with permissions to create Logic Apps

## Post-Deployment Steps

1. Go to the Azure Portal → your Resource Group
2. Open the **Office 365 API connection** resource
3. Click **Edit API connection** → **Authorize** → sign in with the sender account
4. Click **Save**
5. The Logic App will run automatically on the configured schedule

## Testing Locally (Before Publishing)

You can test the full portal wizard experience locally before pushing to GitHub:

### 1. Validate the ARM template
```bash
az deployment group validate \
  --resource-group <YOUR_RG> \
  --template-file azuredeploy.json \
  --parameters clientId=<GUID> clientSecret=<SECRET> \
               emailRecipient=test@example.com emailSender=sender@example.com
```

### 2. Test the Portal UI (createUiDefinition.json)

Use the **Azure Create UI Definition Sandbox** to preview the wizard:

1. Open the sandbox: https://portal.azure.com/?feature.customPortal=false#blade/Microsoft_Azure_CreateUIDef/SandboxBlade
2. Clear the default content in the editor
3. Paste the contents of `createUiDefinition.json`
4. Click **Preview** to see the wizard

### 3. Full end-to-end deployment test
```bash
az deployment group create \
  --resource-group <YOUR_RG> \
  --template-file azuredeploy.json \
  --parameters clientId=<GUID> clientSecret=<SECRET> \
               emailRecipient=test@example.com emailSender=sender@example.com
```

## Files

| File | Description |
|------|-------------|
| `azuredeploy.json` | ARM template — the Logic App and API connection |
| `createUiDefinition.json` | Azure Portal wizard UI definition |
| `XDRNotifications.txt` | Original Logic Apps workflow export (reference) |

## License

MIT

---

## Disclaimer

This ARM template is provided "as is", without warranties or guarantees of any kind.
Use at your own risk. You are responsible for reviewing, validating, and testing this template in a non‑production environment before deploying it to production.
The author assumes no liability for resource costs, configuration issues, or service disruptions resulting from the use of this template.

### Security Considerations

- Review all resource configurations before deployment.
- Validate role assignments, network rules, and identity configurations.
- Confirm compliance with your organization's security and governance standards.
- Ensure secrets/keys are not hard‑coded in templates or parameter files.

## Contributing

Contributions are welcome! When contributing:

- Do not include sensitive information.
- Ensure resource configurations follow Azure best practices.
- Confirm the template passes ARM validation (`az deployment ... --validate`).
- Follow the Code of Conduct (below).

## Code of Conduct

This project adopts the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct).
