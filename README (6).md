# Autonomous Identity & Access Governance Bot

An enterprise-grade, event-driven identity governance framework built using **Azure Functions (Python)** and **Microsoft Graph API**. This system continuously audits **Microsoft Entra ID** tenants to identify identity anomalies, alerts security operations teams via **Microsoft Teams Adaptive Cards**, and processes one-click administrator approval workflows to execute real-time, zero-trust cloud remediations.

---

## 📌 Purpose & Business Case
Unmanaged guest accounts, missing Multi-Factor Authentication (MFA) parameters, and permanent over-privileged administrator assignments represent primary attack surfaces for corporate cloud breaches. 

This project solves identity sprawl by transforming security monitoring from a manual, reactive checklist into an **automated, self-contained auditing cycle**. By leveraging serverless infrastructure, the framework minimizes management overhead while ensuring identity configurations maintain continuous compliance with modern zero-trust architecture rules.

---

## 🔄 Project Workflow Diagram

1. **Audit Phase**: A time-triggered Azure Function invokes daily scans against premium Microsoft Graph API directory endpoints.
2. **Analysis Phase**: The engine filters accounts based on specific identity criteria (such as guest inactivity tracking or incomplete authentication methods).
3. **Alert Phase**: If a configuration drift or vulnerability is identified, a structured webhook constructs and sends an interactive JSON Adaptive Card directly to an assigned Microsoft Teams IT administration channel.
4. **Remediation Phase**: The IT administrator reviews the card data directly within Teams and clicks the action trigger. An HTTP-triggered Azure Function processes the request payload and instantly updates the configuration in Microsoft Entra ID.

---

## 🛠️ Technology Stack Used

*   **Cloud Identity Orchestration**: Microsoft Entra ID (Features: Entra ID P2 Identity Logging, Group & Account Scans)
*   **API Management Layer**: Microsoft Graph API v1.0 (Endpoints used: `/users`, `/credentialUserRegistrationDetails`, `/directoryRoles`)
*   **Serverless Execution**: Azure Functions (Python v2 Model, featuring Timer Triggers and HTTP Webhook routes)
*   **Hosting Runtime Infrastructure**: Azure Consumption Plan (Optimized for pay-as-you-go free execution tiers)
*   **ChatOps UI Interface**: Microsoft Teams (Incoming Webhooks & JSON-formatted Interactive Adaptive Cards)

---

## 🚶‍♂️ End-to-End Implementation Steps

### Step 1: Microsoft Entra ID Enterprise App Registration
To establish secure communication between Azure and your identity data directories, register an isolated service application within your Entra ID tenant to generate API credentials.

1. Navigate to the **Microsoft Entra Admin Center** > **Identity** > **Applications** > **App registrations**.
2. Select **New Registration**, name it `Identity-Access-Governance-Bot`, and click **Register**.
3. Copy the **Application (client) ID** and **Directory (tenant) ID** values.
4. Navigate to **Certificates & secrets**, generate a new client secret, and securely store the secret value string.

<!-- INSERT_SCREENSHOT: Place a screenshot of your Microsoft Entra App Registration Overview screen here -->
<!-- SCREENSHOT_PLACEHOLDER -->

---

### Step 2: Configuring High-Level Microsoft Graph API Permissions
Configure explicit least-privilege enterprise directory read and write policies to authorize the background bot engine.

1. Inside your App Registration dashboard, click **API permissions** > **Add a permission** > **Microsoft Graph**.
2. Choose **Application permissions** (not Delegated permissions).
3. Search for and check these specific permission scopes:
   * `User.ReadWrite.All` — Required to query guest fields and automatically disable non-compliant targets.
   * `AuditLog.Read.All` — Grants explicit access to premium sign-in properties (`signInActivity`).
   * `RoleManagement.Read.Directory` — Grants read-only visibility into privileged directory group hierarchies.
4. **Crucial Action**: Click **"Grant admin consent for [Your Organization Name]"** to clear security authorization flags.

<!-- INSERT_SCREENSHOT: Place a screenshot of your configured and authorized Microsoft Graph API permissions table here -->
<!-- SCREENSHOT_PLACEHOLDER -->

---

### Step 3: Setting Up the Microsoft Teams Incoming Webhook Channel
Configure your target Microsoft Teams collaboration workspace to receive external JSON payloads securely.

1. Open **Microsoft Teams**, create or choose an IT infrastructure operational channel, and click **Manage Channel**.
2. Navigate to **Connectors** or **Apps** > search for **Incoming Webhook**, and choose add.
3. Name your webhook connector (e.g., `Identity Governance Bot`), customize an icon, and select create.
4. Copy the long webhook endpoint destination URL.

<!-- INSERT_SCREENSHOT: Place a screenshot of your Microsoft Teams Incoming Webhook configuration URL card here -->
<!-- SCREENSHOT_PLACEHOLDER -->

---

### Step 4: Deploying Serverless Infrastructure on Azure
Build and deploy the Python backend compute modules into your Azure Pay-As-You-Go subscription architecture.

1. Open the **Azure Portal**, select **Create a Resource**, and select **Function App**.
2. Configure basic deployment settings:
   * **Runtime Stack**: Python
   * **Hosting Plan**: Consumption (Serverless, free execution tier)
   * **Storage**: Pair it with a standard local LRS storage account block.
3. Deploy your project code models using Visual Studio Code Extensions or the Azure CLI tools.
4. Navigate to the App Settings configuration blade to add your variables (`TENANT_ID`, `CLIENT_ID`, `CLIENT_SECRET`, `TEAMS_WEBHOOK_URL`).

<!-- INSERT_SCREENSHOT: Place a screenshot of your deployed Azure Functions displaying active Timer and HTTP triggers inside the Azure Portal dashboard here -->
<!-- SCREENSHOT_PLACEHOLDER -->

---

### Step 5: Validating Live Governance Remediation (The Live Test)
Execute an intentional identity vulnerability setup within your test environment to verify end-to-end framework execution.

1. Inject or locate a sample Guest user account in your Entra ID tenant that simulates over 90 days of total inactivity.
2. Manually trigger or execute your `Timer-Audit-Engine` function routine.
3. Confirm that an interactive card detailing the target user profile arrives inside your Microsoft Teams operational channel channel.
4. Click the **Remediate Account** button inside the Teams UI layout.
5. Return to your **Microsoft Entra ID User Blade** and confirm that the account state has changed instantly to **Account Enabled: No**.

<!-- INSERT_SCREENSHOT: Place a side-by-side or stacked screenshot showing the Adaptive Card active inside Microsoft Teams alongside the changed account state within your Microsoft Entra ID portal here -->
<!-- SCREENSHOT_PLACEHOLDER -->