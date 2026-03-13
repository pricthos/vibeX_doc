# VibeX User Manual
**Last Updated:** March 3, 2026

---

## 1. Getting Started {#getting-started}

### 1.1 Sign Up and Login {#signup-login}
#### Email registration and Google social login
VibeX supports both standard email registration and Google social login for user convenience.

* **Email Registration:** You can create an account by clicking “Sign Up” at the bottom of the login page. Complete the process by verifying your email through the confirmation message.
* **Google Social Login:** You can quickly sign in by clicking “Continue with Google.” Ensure your browser is not blocking authentication popups.

### 1.2 Account Settings {#account-settings}
#### Profile management, password reset, and account deletion
* **Password Reset:** Request a reset link via “Forgot Password?” on the login page. For Google users, please change passwords directly in Google account settings.
* **Account Deletion:** Navigate to `Settings → Account Management → Delete Account`. Once deleted, all data is permanently removed and cannot be recovered.

### 1.3 Plans and X {#pricing-and-x}
#### Pro, Ultra, Business, and Enterprise plan features and X usage
**X** is the dedicated currency consumed for usage-based tasks such as AI-powered generation and code modification. Annual billing offers approximately a **20% discount**.

#### 1. Pro Plan
* **Monthly/Annual:** $39.00 / month | $374.40 / year
* **Credits:** 100,000 X / month
* **Key Features:** Unlimited apps, In-app code editing. Ideal for side projects and casual use.

#### 2. Ultra Plan
* **Monthly/Annual:** $99.00 / month | $950.40 / year
* **Credits:** 250,000 X / month
* **Key Features:** Backend functions, Custom domain, GitHub integration. Designed for production services.

#### 3. Business Plan
* **Monthly/Annual:** $299.00 / month | $2,870.40 / year
* **Credits:** 500,000 X / month
* **Key Features:** Recommended for teams with high workloads. Includes early access to beta features.

#### 4. Enterprise Plan
* **Pricing:** Custom negotiation
* **Credits:** 1,200,000 X / month
* **Key Features:** Highest-tier plan with premium support for large-scale generation. Minimum 5 members required.

---

## 2. Manage Projects & Dashboard {#dashboard}

### 2.1 Dashboard Overview & Navigation {#dashboard-overview}
#### View your app list, search projects, and use the “Continue where you left off” feature
* **Continue Work:** Quickly return to recent projects from the center of the home screen.
* **Search:** Find specific projects using the search bar or filter by workspace.

### 2.2 Creating and Managing Apps (Projects) {#manage-projects}
#### Create new apps, edit project details, duplicate, or delete projects
* **Duplicate:** Reuse layout or code by selecting 'Duplicate' from the project card menu.
* **Delete:** Permanently remove projects. Deleted code cannot be recovered.

### 2.3 Team Workspace {#team-workspace}
#### Collaborate by inviting team members and assigning permissions
* **Collaboration:** Create a workspace and invite colleagues via email.
* **Permissions:** Assign 'Editor' (full access) or 'Viewer' (read-only) roles to members.

---

## 3. App Planning & Generation {#app-planning}

### 3.1 Conversational App Planning {#conversational-planning}
#### Designing UI and Feature Structure with Text Prompts
* **Prompting:** Describe your idea freely. Providing specific details like target audience and main colors ensures high-quality results.
* **Mobile-First:** Specify "mobile-first layout" in the prompt if you want a mobile style UI.

### 3.2 Refining the Project Plan {#refining-plan}
#### Requesting Database Schemas, API Lists, and Milestones
* **Detailed Specs:** Request structured data like page lists in table format or backend JSON schemas to refine the plan.

### 3.3 Understanding the Project Generation Pipeline {#generation-pipeline}
#### Starting to Generating Process and status tracking
The process involves four stages:
1. **Starting:** Allocating initialization environment.
2. **Setting up project:** Installing required packages.
3. **Thinking:** AI analyzes structure and components.
4. **Generating:** Final code writing and asset rendering.

---

## 4. Editor & Live Preview {#editor-preview}

### 4.1 Editor Interface {#editor-interface}
#### Using the Code, Data, and Dashboard Tabs
* **Code Tab:** Directly edit frontend source code.
* **Data Tab:** Manage JSON data and DB structures.
* **Dashboard Tab:** Manage deployment, environment variables, and domains.

### 4.2 Live Preview and Logs {#preview-logs}
#### Viewing Render Results and Debugging with Console Logs
* **Preview:** View real-time rendering on the right side.
* **Console:** Check JavaScript logs and build errors for debugging.

### 4.3 Code Editing and Version Control {#code-versioning}
#### Editing with AI Prompts, Auto-Save, and Version Rollback
* **Rollback:** Use the history panel to revert code to an earlier state if needed.

### 4.4 Asset Management {#asset-management}
#### Uploading and Replacing Image and Font Files
* **Upload:** Drag and drop assets into the file tree (max 5MB).
* **Replace:** Update the `src` path in code to reflect the new file name.

---

## 5. Prompting Tips {#prompting-tips}

### 5.1 Design and UI Optimization {#design-optimization}
#### Tips for Colors, Fonts, Dark Mode, and Responsive Mobile Design
* **Specify Values:** Use HEX codes and exact CSS values (e.g., `border-radius: 16px`) for consistent results.

### 5.2 Implementing Logic {#logic-implementation}
#### Requesting Admin Pages, Authentication, and Payment Modules
* **Detailed Flow:** Describe authentication or payment scenarios (e.g., Stripe integration) to generate functional logic.

### 5.3 Error Troubleshooting {#troubleshooting}
#### Debugging Code When Build or Rendering Errors Occur
* **Error Logs:** Paste red error messages from the Console into the chat for AI-assisted debugging.

---

## 6. Community Templates {#community-templates}

### 6.1 Browsing Templates {#template-search}
#### Searching and Previewing Templates by Category
* **Filtering:** Use keywords or category filters to find the right structure for your project.

### 6.2 Importing Templates {#import-templates}
#### Copying Templates into Your Workspace for Customization
* **Import:** Create an independent copy to freely modify code and add features.

### 6.3 Sharing and Licenses {#template-license}
#### Uploading Your Project and Important Usage Notes
* **Sharing:** Enable 'Public' in project settings to share with the community.
* **Copyright:** Replace template assets with your own for commercial use.

---

## 7. Backend & Integrations {#backend-integrations}

### 7.1 Using Backend Functions {#backend-functions}
#### Connecting External Databases and Calling APIs
* **Functions:** Implement server-side logic and connect DBs like MongoDB or Supabase.

### 7.2 Environment Variable Configuration {#env-variables}
#### Secure Management of Sensitive Information
* **Security:** Store API secret keys in the Environment Variables menu; never hardcode them in the frontend.

### 7.3 GitHub Integration {#github-integration}
#### Connecting Permissions and Exporting Source Code to Your Repository
* **Push:** Export code to GitHub repositories (Ultra plan and above).

---

## 8. Publish & Export {#publish-export}

### 8.1 Publishing Your App {#app-publishing}
#### Deploying via the Publish Button and Generating a Public URL
* **Update:** Remember to click 'Publish' again after modifying code to reflect changes live.

### 8.2 Connecting a Custom Domain {#custom-domain}
#### Configuring DNS and Automatic SSL (HTTPS) Setup
* **SSL:** Certificates are automatically issued after domain connection is successful.

### 8.3 Downloading Source Code {#download-source}
#### Exporting the Project as a ZIP File and Running It Locally
* **Local Run:** Download the ZIP, run `npm install`, and then `npm run dev` to execute locally.
