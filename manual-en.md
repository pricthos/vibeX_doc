# VibeX User Manual
**Last Updated:** March 3, 2026

---

## 1. Getting Started {#getting-started}

### 1.1 Sign Up and Login : Email registration and Google social login {#signup-login}
VibeX supports both standard email registration and Google social login for user convenience.

* **Email Registration:** You can create an account by clicking “Sign Up” at the bottom of the login page. After agreeing to the required terms, complete the process by verifying your email through the confirmation message sent to your inbox. If you cannot find the verification email, please check your spam folder or confirm that there are no leading or trailing spaces in the email address you entered.
* **Google Social Login:** You can quickly sign in by clicking “Continue with Google.” If clicking the button only shows a blank screen, your browser may be blocking the authentication popup. Please allow popups and try again.

> 💡 **Having trouble accessing your account?**
> If the sign-up button does not respond or the page keeps loading indefinitely, try temporarily disabling your browser’s AdBlock extension or access VibeX using Incognito Mode.

### 1.2 Account Settings : Profile management, password reset, and account deletion {#account-settings}
* **Password Reset:** If you signed up using an email account, you can request a password reset email via the “Forgot Password?” link on the login page. If you use Google social login, password changes must be made directly in your Google account settings, not within VibeX.
* **Account Deletion and Data Removal:** You can permanently delete your account by navigating to `Settings → Account Management → Delete Account`. Once deleted, your account and all associated data will be permanently removed. Due to privacy policies, deleted data cannot be recovered under any circumstances. Before deleting your account, we strongly recommend exporting and backing up any important project code from the editor.

### 1.3 Plans and X : Pro, Ultra, Business, and Enterprise plan features and X usage {#pricing-and-x}
**X** refers to the dedicated currency consumed when performing usage-based tasks such as AI-powered app planning, code generation, and modification within the VibeX platform. VibeX offers four different plans depending on your needs. If you choose annual billing, you will receive approximately a **20% discount**. Monthly X reset on your billing renewal date and do not roll over to the next month.

#### 1. Pro Plan
* **Monthly Billing:** $39.00 / month
* **Annual Billing:** $374.40 / year 
* **Credits:** 100,000 X / month
* **Key Features:** Unlimited number of apps, In-app code editing. This plan is ideal for casual use or testing side projects.

#### 2. Ultra Plan
* **Monthly Billing:** $99.00 / month
* **Annual Billing:** $950.40 / year
* **Credits:** 250,000 X / month
* **Key Features:** Backend functions, Custom domain connection, GitHub integration. This plan is designed for users preparing to run real production services.

#### 3. Business Plan
* **Monthly Billing:** $299.00 / month
* **Annual Billing:** $2,870.4 / year
* **Credits:** 500,000 X / month
* **Key Features:** Recommended for teams with frequent updates and larger workloads that require higher X usage. Includes early access to beta features.

#### 4. Enterprise Plan
* **Monthly Billing:** Custom pricing
* **Annual Billing:** Custom pricing
* **Credits:** 1,200,000 X / month
* **Key Features:** The highest-tier plan for teams requiring large-scale generation and premium support. 
* **Configuration:**
    * Tier 1: $3,000 / month $28,800 / year (based on 10 members) 
    * Tier 2: Custom negotiation starting at $3,000 depending on organization size.
    * Team Requirement: Minimum of 5 members required.

---

## 2. Manage Projects & Dashboard {#dashboard}
The Dashboard is the main workspace you see immediately after logging into VibeX. It allows you to organize and manage multiple project ideas in one place.

### 2.1 Dashboard Overview & Navigation {#dashboard-overview}
* **Continue Where You Left Off:** Located at the center of the main home screen, this section displays the project cards you were recently working on. With a single click, you can quickly return to the editor and resume your work.
* **App List & Search:** You can quickly find a specific project using the “Search apps…” bar at the top. The “My Apps (Personal Workspace)” dropdown also allows you to filter projects by workspace.

### 2.2 Creating and Managing Apps (Projects) {#manage-projects}
* **Create a New Project:** Click the “+ New App (or New Project)” button in the upper-right corner of the dashboard to start a new project from scratch.
* **Edit Name and Project Information:** You can change the project name from the editor header or project settings. To save the change, press Enter or click outside the input field. Project descriptions are automatically saved after a short delay.
* **Duplicate a Project:** If you want to reuse an existing app’s layout or code, open the three-dot (More) menu on the project card and select Duplicate to create an identical copy.
* **Delete a Project:** You can delete a project by selecting Delete from the same menu.
    * ⚠️ **Important:** Once deleted, the project and its code are permanently removed from the server and cannot be recovered. Please proceed carefully.

> 💡 **Project Management Tip**
> Currently, VibeX does not support a dedicated folder system. If you have many projects, we recommend using consistent naming conventions with brackets, such as: `[E-commerce] Main Page`, `[Portfolio] Contact Form`.

### 2.3 Team Workspace: Collaborate by inviting team members and assigning permissions {#team-workspace}
* **Invite Team Members:** Create a Team Workspace in your account settings and send invitations by entering your colleagues’ email addresses.
* **Permission Settings:** Invited members can be granted different permissions: Editor access (modify code freely) or Viewer access (view only). If your project appears as read-only, request a permission update from the administrator.
* **Ownership Limitation:** Currently, VibeX does not support transferring full project ownership to another user account.

---

## 3. App Planning & Generation {#app-planning}
You can start planning and building an app with AI using only text prompts—without complex diagrams or formal specifications.

### 3.1 Conversational App Planning {#conversational-planning}
* **Entering a Prompt:** Type your app idea freely into the text input field on the home screen. Use `Shift + Enter` for line breaks.
* **Clear Instructions:** Providing specific details—such as target users, main colors, and core features—will help the AI generate a much more detailed plan.
* **Platform Specification:** VibeX defaults to web. For mobile, specify: “Design the UI with a mobile-first layout and touch interactions.”

> 💡 **When Conversations Are Delayed:**
> If the response stops midway, simply type “Continue writing” and the AI will resume from where it stopped.

### 3.2 Refining the Project Plan {#refining-plan}
* **Structured Data Requests:** e.g., “List all pages required and summarize features in a table format.”
* **Backend / API Planning:** e.g., “Add a JSON-based database schema” or “Define a list of REST API endpoints.”
* **Maintaining Context:** If the AI forgets instructions, remind it: “Remember to apply the dark mode rules mentioned earlier.”

### 3.3 Understanding the Project Generation Pipeline {#generation-pipeline}
The generation process proceeds through four stages:
1.  **Starting:** Allocates the initialization environment.
2.  **Setting up project:** Installs required packages (React, Vue, etc.).
3.  **Thinking:** AI analyzes the overall structure and designs relationships.
4.  **Generating:** AI writes the actual code and renders assets.

---

## 4. Editor & Live Preview {#editor-preview}

### 4.1 Editor Interface : Using the Code, Data, and Dashboard Tabs {#editor-interface}
* **Code Tab:** View and edit the frontend source code.
* **Data Tab:** Manage sample JSON data or backend database structures.
* **Dashboard Tab:** Manage operational settings (deployment, env variables, domains).

### 4.2 Live Preview and Logs {#preview-logs}
* **Preview Panel:** Real-time rendering on the right. Note that internal state may reset on browser refresh.
* **Console Logs:** Click “Console” (bottom-right) to view build errors or JavaScript logs.

### 4.3 Code Editing and Version Control {#code-versioning}
* **Auto-Save:** Saves in the background, but avoid refreshing immediately after a manual edit.
* **Regeneration and Rollback:** If you dislike a new version, use “Rollback” in the history panel to revert to an earlier state.

### 4.4 Asset Management {#asset-management}
* **Uploading Assets:** Drag and drop files into the file tree panel on the left.
* **Restrictions:** 5MB or smaller. Supports JPG, PNG, SVG, WEBP. No executable files (.exe, .sh).
* **Replacing Images:** Update the image path in the code (e.g., `src="..."`) with the new file name.

---

## 5. Prompting Tips {#prompting-tips}

### 5.1 Design and UI Optimization {#design-optimization}
* **Provide References:** e.g., “Minimalist theme similar to Linear” or “Clean Apple-style layout.”
* **Specify Values:** Use HEX codes (#1E1E1E) and exact CSS values (border-radius: 16px).
* **Multilingual Support:** Ask to separate strings using an i18n-style structure.

### 5.2 Implementing Logic {#logic-implementation}
* **Authentication:** “Create an email login form with React state for isLoggedIn.”
* **Database:** “Define a JSON-based schema at the top of the code.”
* **Admin Pages:** “Create a separate admin dashboard with its own routing.”

### 5.3 Error Troubleshooting {#troubleshooting}
* **Preserve Code:** “Keep existing layout exactly the same, only add [feature].”
* **Paste Logs:** Copy red error messages from the Console into the chat and ask “Fix this error.”
* **Rollback:** If results are unexpected, rollback and refine your prompt.

---

## 6. Community Templates {#community-templates}

### 6.1 Browsing and Importing {#import-templates}
* **Import:** Click “Import” to create an independent copy in your workspace.
* **Compatibility:** If errors occur, ask: “Update dependency versions in package.json and fix compatibility issues.”

### 6.2 Sharing and Licenses {#template-license}
* **Sharing:** Enable “Public” in project settings to share with the community.
* **Assets:** Replace template assets with your own for commercial use to avoid copyright issues.

---

## 7. Backend & Integrations {#backend-integrations}

### 7.1 Backend Functions and DB {#backend-functions}
* **Connection:** Connect MongoDB, Supabase, etc. Check credentials and IP whitelisting if connection fails.
* **Payments:** Implement Stripe or PortOne through Backend Functions. Validate in test mode first.

### 7.2 Environment Variables (.env) {#env-variables}
* **Secret Keys:** Store keys (OpenAI, etc.) in `Dashboard → Environment Variables`. Never hardcode them in the frontend.
* **Naming:** Ensure case-sensitive matching (e.g., `process.env.VITE_API_KEY`).

### 7.3 GitHub Integration {#github-integration}
* **Push:** Export code to a Public or Private repository. Requires Ultra plan.
* **Conflicts:** We recommend connecting to a new, empty repository to avoid overwrite blocks.

---

## 8. Publish & Export {#publish-export}

### 8.1 Publishing Your App {#app-publishing}
* **Deployment:** Click “Publish” to host on VibeX servers.
* **Updates:** You must click “Publish” again after modifying code to reflect changes on the live site.

### 8.2 Custom Domain {#custom-domain}
* **DNS:** Register A or CNAME records at your domain provider.
* **SSL:** VibeX automatically issues SSL certificates (may take 1–2 hours after connection).

### 8.3 Downloading Source Code {#download-source}
* **ZIP:** Export as a compressed archive for local execution (`npm install`, `npm run dev`) or external hosting (Vercel).
