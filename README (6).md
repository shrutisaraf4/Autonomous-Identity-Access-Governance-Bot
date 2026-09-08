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
2. Select **New Registration**, name it `Identity-Access-Governance-Bot`, and click **Register**<img width="1907" height="870" alt="image" src="https://github.com/user-attachments/assets/ebf452e4-fd9e-440e-9198-0e641e2c3d3e" />
3. Copy the **Application (client) ID** and **Directory (tenant) ID** values.<img width="1570" height="727" alt="image" src="https://github.com/user-attachments/assets/e4da8118-5a60-40cd-84ed-6db47395218e" />
4. Navigate to **Certificates & secrets**, generate a new client secret, and securely store the secret value string.
<img width="1532" height="792" alt="image" src="https://github.com/user-attachments/assets/129e699e-eba6-49f2-a0f8-8d08c22a3a65" />
<img width="1575" height="813" alt="image" src="https://github.com/user-attachments/assets/32c1ebbf-e8e2-4ec3-aaf4-a84bc3c596a7" />
<img width="1615" height="842" alt="image" src="https://github.com/user-attachments/assets/8b067dc3-7b4d-4328-bf10-1b64ccd6b559" />

---

### Step 2: Configuring High-Level Microsoft Graph API Permissions
Configure explicit least-privilege enterprise directory read and write policies to authorize the background bot engine.

1. Inside your App Registration dashboard, click **API permissions** > **Add a permission** > **Microsoft Graph**.
2. Choose **Application permissions** (not Delegated permissions).<img width="1618" height="813" alt="image" src="https://github.com/user-attachments/assets/1c1fc583-448d-4770-8c84-3feeb7933a15" />
3. Search for and check these specific permission scopes:
   * `User.ReadWrite.All` — Required to query guest fields and automatically disable non-compliant targets.<img width="917" height="817" alt="image" src="https://github.com/user-attachments/assets/ce1fe506-bd72-4b74-b613-006fe9f27075" />
   * `AuditLog.Read.All` — Grants explicit access to premium sign-in properties (`signInActivity`).<img width="962" height="797" alt="image" src="https://github.com/user-attachments/assets/8abe76dd-fde9-4c8d-9c98-f42a7c2cf861" />
   * `RoleManagement.Read.Directory` — Grants read-only visibility into privileged directory group hierarchies.<img width="927" height="787" alt="image" src="https://github.com/user-attachments/assets/ceab781c-0bb4-4aa9-9663-b8d6abe8c3d5" />
4. **Crucial Action**: Click **"Grant admin consent for [Your Organization Name]"** to clear security authorization flags.
<img width="1572" height="807" alt="image" src="https://github.com/user-attachments/assets/6a572029-37dc-4d8f-9123-032035b7a807" />
<img width="1557" height="457" alt="image" src="https://github.com/user-attachments/assets/debe7e80-2e9d-4162-b9a4-c04c795f056e" />
<img width="1257" height="397" alt="image" src="https://github.com/user-attachments/assets/4eca1125-f314-46c8-9d17-e32a0b16eff3" />


---

### Step 3: Setting Up the Microsoft Teams Incoming Webhook Channel
Configure your target Microsoft Teams collaboration workspace to receive external JSON payloads securely.

1. Open **Microsoft Teams**, create or choose an IT infrastructure operational channel, and click **Manage Channel**.<img width="1895" height="550" alt="image" src="https://github.com/user-attachments/assets/f783f171-ea2c-4962-81f0-1d0fdd779965" />
<img width="867" height="817" alt="image" src="https://github.com/user-attachments/assets/4c96ea96-2b29-481b-9bf6-6f2d904ef2d7" />
<img width="1835" height="697" alt="image" src="https://github.com/user-attachments/assets/6e56292d-0a9a-4a3d-b1fb-0f53f08f3b19" />
2. Navigate to **Workflows** > search for **Send webhook alerts to a channel**, and click to add.<img width="975" height="761" alt="image" src="https://github.com/user-attachments/assets/beb17b88-2ebd-4c84-ac6a-5a219e47d2ef" />
<img width="977" height="727" alt="image" src="https://github.com/user-attachments/assets/06c3836f-43f2-4b13-9068-aeccf57a680a" />
3. Copy the long webhook endpoint destination URL.
<img width="987" height="766" alt="image" src="https://github.com/user-attachments/assets/1ab080bd-ae1f-4ce0-b4e8-929a5db1b8ae" />

---

### Step 4: Deploying Serverless Infrastructure on Azure
Build and deploy the Python backend compute modules into your Azure Pay-As-You-Go subscription architecture.

1. Open the **Azure Portal**, select **Create a Resource**, and select **Function App**.<img width="1085" height="346" alt="image" src="https://github.com/user-attachments/assets/ba02bc43-88e3-4b99-899d-27c298dfd51b" />
2. Configure basic deployment settings:
     * **Hosting Plan**: Consumption (Serverless, free execution tier)<img width="1915" height="611" alt="image" src="https://github.com/user-attachments/assets/4428c17b-701a-44e8-89a7-3b9dd589230d" />
     * **Runtime Stack**: Powershell
     * **Version**: Select 7.2 (or the highest 7.x version available).
     * **Region: Choose your local or closest data center region (e.g., India South Central).<img width="1420" height="792" alt="image" src="https://github.com/user-attachments/assets/bffb98d9-8ec3-493f-b65d-2b913ee3524b" />
     * **Storage**: Pair it with a standard local LRS storage account block.<img width="1037" height="817" alt="image" src="https://github.com/user-attachments/assets/15103c16-b57f-4c4c-97d5-c6470adbb407" />
     * Proceed through the wizard tabs (Hosting, Monitoring) keeping the defaults, and click Review + Create, then Create*
<img width="1110" height="804" alt="image" src="https://github.com/user-attachments/assets/acc49a72-1c57-4ea1-b640-7b68d142c68c" /><img width="1163" height="862" alt="image" src="https://github.com/user-attachments/assets/15f0a3d4-4017-4acb-968a-f7087f8c0698" />
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
