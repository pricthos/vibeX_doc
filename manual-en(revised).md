# VibeX User Manual
**Last Updated:** March 3, 2026

---

## 1. Getting Started {#getting-started}

### 1.1 Sign Up and Login {#signup-login}
#### Email registration and Google social login
VibeX supports both standard email registration and Google social login for user convenience.

* **Email Registration:** You can create an account by clicking **“Sign Up”** at the bottom of the login page. after agreeing to the required terms, complete the process by verifying your email through the confirmation message sent to your inbox. If you cannot find the verification email, please check your spam folder or confirm that there are no leading or trailing spaces in the email address you entered.
* **Google Social Login:** You can quickly sign in by clicking **“Continue with Google.”**  
   If clicking the button only shows a blank screen, your browser may be blocking the authentication popup. Please allow popups and try again.
  
![signup-login](https://stg-cdn.vibe-x.app/images/iVNfJw33EU-1773635732708.png)
   
> 💡 **Having trouble accessing your account?**  
 If the sign-up button does not respond or the page keeps loading indefinitely, try temporarily disabling your browser’s **AdBlock extension** or access VibeX using **Incognito Mode**.


### 1.2 Account Settings {#account-settings}
#### Profile management, password reset, and dava removal
* **Password Reset:** Request a reset link via “Forgot Password?” on the login page. For Google users, please change passwords directly in Google account settings.
* **Data Removal:** Due to privacy policies, deleted data **cannot be recovered under any circumstances**. Before deleting your account, we strongly recommend exporting and backing up any important project code from the editor.

### 1.3 Plans and X {#pricing-and-x}
#### Pro, Ultra, Business, and Enterprise plan features and X usage
**X** is the dedicated currency consumed for usage-based tasks such as AI-powered generation and code modification. Annual billing offers approximately a **20% discount**.

### **1\. Pro Plan**

* **Monthly Billing:** $39.00 / month  
* **Annual Billing:** $375 / year   
* **Credits:** 390 X / month  
* **Key Features:** Unlimited number of apps, In-app code editing. This plan is ideal for casual use or testing side projects.

### **2\. Ultra Plan**

* **Monthly Billing:** $99.00 / month  
* **Annual Billing:** $950 / year  
* **Credits:** 950 X / month  
* **Key Features:** Backend functions, Custom domain connection, GitHub integration. This plan is designed for users preparing to run real production services.

### **3\. Business Plan**

* **Monthly Billing:** $299.00 / month  
* **Annual Billing:** $2,870 / year  
* **Credits:** 3,700 X / month  
* **Key Features:** Recommended for teams with frequent updates and larger workloads that require higher X usage. Includes **early access to beta features**.

### **4\. Enterprise Plan**

* **Monthly Billing:** Custom pricing  
* **Annual Billing:** Custom pricing  
* **Credits:** Custom  
* **Key Features:** The highest-tier plan for teams requiring large-scale generation and premium support. Includes **all Business plan features plus priority technical support**.  
* **Configuration:**  
* Team Requirement: Minimum of 5 members required.

### 1.4 Purchase Additional X

#### When Your X Is Used Up During a Subscription

If you have used all of your X while subscribed, you can purchase additional X. You can buy as many as you need without upgrading your plan. For every $1, you will receive 10 X, and no additional discounts apply.


---

## 2. Manage Projects & Dashboard {#dashboard}
The **Dashboard** is the main workspace you see immediately after logging into VibeX.  
 It allows you to organize and manage multiple project ideas in one place.

### 2.1 Dashboard Overview & Navigation {#dashboard-overview}
#### View your app list, search projects, and use the “Continue where you left off” feature
* **Continue Where You Left Off:** Located in the center of the screen after clicking **Home \> Apps**, this section displays the project cards you were recently working on. With a single click, you can quickly return to the editor and resume your work.

* **App List & Search:** Use the **“Search apps…”** bar at the top to quickly find a specific project. You can also mark preferred apps with a **star (☆)** and filter the list to view only your favorites.

### 2.2 Creating and Managing Apps (Projects) {#manage-projects}
#### Create new apps, edit project details, duplicate, or delete projects
* **Create a New Project:** home \> apps button \> Click the **“+ New App (or New Project)”** button in the upper-right corner of the dashboard to start a new project from scratch.
* **Edit Name and Project Information:** You can change the project name from the editor header or project settings. To save the change, press **Enter** or click outside the input field. Project descriptions are automatically saved after a short delay.
* **Duplicate a Project:** If you want to reuse an existing app’s layout or code, open the **three-dot (More)** menu on the project card and select **Duplicate** to create an identical copy.
* **Delete a Project:** You can delete a project by selecting **Delete** from the same menu.  
  ⚠️ **Important:**  
   Once deleted, the project and its code are permanently removed from the server and **cannot be recovered**. Please proceed carefully.

>💡 **Project Management Tip** Currently, VibeX does not support a dedicated **folder system**.  
 If you have many projects, we recommend using consistent naming conventions with brackets, such as: `[E-commerce] Main Page,[Portfolio] Contact Form`  
This makes searching and managing projects much easier.

### 2.3 Team Workspace {#team-workspace}
#### Collaborate by inviting team members and assigning permissions (Editor or Viewer)
If working alone becomes difficult, you can invite teammates to collaborate on a project together.

* **Invite Team Members:** Click the **Share** button within the app and enter your colleague’s email address to send an invitation..
* **Permission Settings:** Invited members can be granted different permissions depending on the administrator’s settings. They may receive **Editor access**, allowing them to modify code freely, or **Viewer access**, which allows them to view the project without making changes. If your project appears as **read-only**, please request a permission update from the workspace administrator.
* **Ownership Limitation:** Currently, VibeX does **not support transferring full project ownership** to another user account.

---

## 3. App Planning & Generation {#app-planning}
You can start planning and building an app with AI using only text prompts—without complex diagrams or formal specifications.

### 3.1 Conversational App Planning {#conversational-planning}
#### Designing UI and Feature Structure with Text Prompts


* **Entering a Prompt:** Type your app idea freely into the text input field located at the center of the home screen, then click **Start(paper plane) button** or press **Enter**. If you want to write multiple lines, use **Shift \+ Enter** to insert a line break.  
* **Clear Instructions:** If the prompt is short or abstract, the result will also be generic. Providing more specific details—such as the target users, main colors, and core features—will help the AI generate a much more detailed project plan.  
* **Platform Specification:** VibeX generates **web-based applications by default**. If you want a mobile-style application, clearly specify in the prompt: “Design the UI with a **mobile-first layout and touch interactions**.”

>💡 **When Conversations Are Delayed or Interrupted:**  
 If the request is complex, the AI may take **1–2 minutes** during the **Thinking** stage to analyze the project structure. If the response stops midway, simply type **“Continue writing”** and the AI will continue from where it stopped.

### 3.2 Refining the Project Plan {#refining-plan}
#### Requesting Database Schemas, API Lists, and Milestones
In addition to basic screen layouts, you can ask the AI to generate more detailed technical specifications required for development.

* **Structured Data Requests:** You can request structured documentation such as: “List all pages required for this app and summarize the main features of each page in a table format.”  
* **Backend / API Planning:** You may request backend specifications such as:  
   “Add a **JSON-based database schema** for storing data.” or “Define a list of **REST API endpoints** for communication between the client and server.”  
- **Maintaining Context:** If the conversation becomes too long, the AI may forget earlier instructions (such as applying a specific theme). In that case, remind the AI by saying: “Remember to apply the **dark mode rules mentioned earlier**.”



### 3.3 Understanding the Project Generation Pipeline {#generation-pipeline}

#### Starting to Generating Process and status tracking
Once you are satisfied with the planning conversation, click the **“Generate Project”** button on the right side of the screen to begin creating the actual code.

The process involves four stages:
1. **Starting:** Allocates the project initialization environment.  
2. **Setting up project:** Installs required packages on the server for frameworks such as **React** or **Vue**.  
3. **Thinking:** The AI analyzes the overall application structure and designs relationships between components.  
4. **Generating:** The final stage where the AI writes the actual code and renders assets such as logos and images.

>💡 **Auto-Fix System:**  
 If the generation progress bar suddenly resets to the beginning, do not worry. When an error is detected during generation, the AI automatically fixes the code and restarts the build process as part of its built-in recovery system.


---

## 4. Editor & Live Preview {#editor-preview}
Once the code has been successfully generated, you will be redirected to the **VibeX Editor**.  
 Here, you can review the generated code, interact with AI, and refine your application in detail.

### 4.1 Editor Interface {#editor-interface}
#### Using the Code, Data, and Dashboard Tabs
The editor is divided into three main tabs, each designed for a specific purpose.

* **Code Tab:** This is where you can directly view and edit the frontend source code that builds the application interface.  
* **Data Tab:** This tab allows you to view and manage sample **JSON data** or backend database structures used by the application.  
* **Dashboard Tab:** Here you can manage operational settings for your app, such as checking deployment status, configuring environment variables, and connecting custom domains.

### 4.2 Live Preview and Logs {#preview-logs}

#### **Viewing Render Results**
* **Preview Panel:** On the right side of the editor, you can see a **live preview** of the rendered application. If you refresh the browser, the app’s internal state (such as items in a shopping cart or login status) may reset. This is normal behavior for web applications.

### 4.3 Code Editing and Version Control {#code-versioning}
#### Editing with AI Prompts, Auto-Save, and Version Rollback
* **Auto-Save:** The VibeX editor automatically saves code changes in the background whenever modifications are detected. However, if you refresh the page immediately after manually editing the code—before the preview panel updates—your changes may be lost.  
    
* **Regeneration and Rollback:** When you enter a prompt to regenerate code, the existing code will be overwritten with the new version. If you prefer the previous result, you can use the **“Rollback”** option in the conversation history panel on the left to revert the project to an earlier state.

### 4.4 Asset Management {#asset-management}
#### Uploading and Replacing Image and Font Files
If you are not satisfied with the images generated by AI or want to use your own logo, you can manage assets manually.

* **Uploading Assets:** You can upload images directly by **dragging and dropping files from your computer into the file tree panel** on the left side.  
    
* **File Size and Format Restrictions:** For optimal performance, each file must be **5MB or smaller**. Supported image formats include **JPG, PNG, SVG, and WEBP**.  
   For security reasons, executable files such as **.exe or .sh** cannot be uploaded.  
    
* **Replacing Images:** After uploading an asset, update the image path in the code (for example, `src="..."`) with the newly uploaded file name. The change will be reflected immediately in the preview.

---

## 5. Prompting Tips {#prompting-tips}
The quality of the **prompt** you provide to the AI directly determines the quality of the application that will be generated. Use the following tips to guide the AI more effectively and achieve the design and functionality you want.

### 5.1 Design and UI Optimization {#design-optimization}
#### Tips for Colors, Fonts, Dark Mode, and Responsive Mobile Design
*  **Provide Clear References and Themes:** Instead of using vague instructions like *“Make it look nice,”* provide clear references. For example: “Design it with a **minimalist theme similar to the Linear app**” or “Redesign it in a **clean Apple-style layout with generous spacing**.”  
    
* **Specify Exact Colors and Design Values:** Define colors using **HEX codes** such as `#1E1E1E` or `#FF5733` for backgrounds or primary buttons. You can also specify CSS values directly, such as: “Set the **border-radius of all card UI components to 16px** and the **padding to 24px**.” Providing exact values helps maintain consistent design results.  
    
* **Prepare for Multilingual Support:** If you plan to support multiple languages, ask the AI to structure the UI text accordingly. For example: “Do not hardcode UI text. Instead, separate Korean and English strings using an **i18n-style structure**.”

### 5.2 Implementing Logic {#logic-implementation}

#### Requesting Admin Pages, Authentication, and Payment Modules
If you want more than just UI mockups and need **functional application logic**, describe the workflow in detail.

* **Authentication and State Management:** For example: “Create an email login form and include **React state logic to manage the login status**.”  
    
* **Database Structure:** You can request a structured database setup by asking: “Define a **JSON-based database schema** for user information and post data at the top of the code.”  
    
* **Separate Routing and Admin Pages:** If you need additional functionality beyond the user interface, specify it clearly: “Create a **separate admin dashboard** with its own routing for product registration and user management.”  
    
* **External Payment Integration:** You can also describe a detailed user flow such as: “When the user clicks the checkout button in the cart, open a **Stripe payment modal** and trigger a mock **payment success alert** after completion.”

### 5.3 Error Troubleshooting {#troubleshooting}
#### Debugging Code When Build or Rendering Errors Occur

* **Strongly Request Preservation of Existing Code:** When modifying only a specific part of the app, clearly instruct the AI to keep everything else unchanged. For example: “**Keep the existing layout and logic exactly the same**, and only add a notification icon to the top-right corner.”  
    
* **Paste Error Logs:** Copy the red error messages from the **Console panel** at the bottom-right of the editor and paste them directly into the chat. Then ask: “Fix this error.” The AI can analyze the error and suggest a correction.  
    
* **Provide Possible Causes:** Instead of simply saying *“There is an error,”* include a suspected cause. For example: “The build failed due to a **package conflict**. Please fix the problematic library import.”  
    
* **Handling Unexpected Results:** If the AI generates code that does not match your instructions, avoid overwriting everything immediately. Instead, use the **Rollback** feature in the history panel to restore the previous version, refine your prompt with clearer instructions, and try again.


---

## 6. Backend & Integrations {#backend-integrations}
To build a powerful application beyond a simple frontend UI—such as storing real data or processing payments—you will need integrations with external systems.

### 6.1 Using Backend Functions {#backend-functions}
#### **External Integration & API Calls**
Backend Functions allow you to implement logic that runs directly on the server.
* **Payment Module Integration:** You can implement payments by calling external payment APIs such as **Stripe** or **PortOne (Portone)** through Backend Functions.  
   Before enabling production payments, make sure to validate the full flow thoroughly in **test mode**

* **Timeout Warnings:** If you call external APIs excessively or create an infinite loop inside a function, the execution may be stopped due to a **timeout**.  
   We recommend keeping your server-side logic lightweight and efficient.

### 6.2 Environment Variable Configuration {#env-variables}
#### Secure Management of Sensitive Information
When integrating external services, pay close attention to security settings.

* **Secret Key Management:** Never place secret keys (such as an **OpenAI API key**) directly in frontend code, as exposure can lead to unintended charges. Instead, store them securely under **Dashboard → Secrets** and configure the app to reference them **only from the backend**.  
    
* **Verify Environment Variable Names:** Make sure the environment variable key name you registered matches the key referenced in code exactly, including letter case (for example, `process.env.VITE_API_KEY`).  
    
* **API Rate Limits:** To prevent abuse, if your app generates excessive traffic in a short period or calls external APIs abnormally, access may be temporarily restricted by the system’s built-in **rate limiting**.

### 6.3 GitHub Integration {#github-integration}
#### Connecting Permissions and Exporting Source Code to Your Repository
You can export and manage your generated code safely in GitHub. (This feature is available starting from the **Ultra plan**.)

* **Creating a Repository:** After connecting your GitHub account, you can export your project and choose whether to create a **Public** or **Private** repository. If the repository name contains special characters or already exists, creation may fail. We recommend using lowercase letters and hyphens (`-`).  
    
* **Fixing Push Permission Errors:** If a push fails due to an expired authentication token between VibeX and GitHub, disconnect the GitHub integration in VibeX settings and reconnect it to refresh permissions.  
    
* **Branch Management:** Currently, VibeX does not support advanced Git operations such as creating additional branches (e.g., `dev`, `feature`) beyond `main`, or running Pull Request (PR) workflows inside the platform.  
    
* **Avoiding Conflicts:** If your target GitHub repository contains commits or changes that VibeX is not aware of, overwriting may be blocked. To avoid issues, we recommend connecting to a **new, empty repository** whenever possible.

---

## 7. Publish & Export {#publish-export}

### 7.1 Publishing Your App {#app-publishing}
#### Deploying via the Publish Button and Generating a Public URL
You can deploy your app to the web instantly without complex server configuration.

* **Start Deployment:** Click the **“Publish”** button in the upper-right corner of the editor.  
  Your current code will be built and uploaded to the VibeX hosting server, and a **public URL** will be generated for anyone to access.  
    
* **Deployment Conditions:** If the AI is still generating code in the background or the project has not been fully saved, the **Publish** button may be disabled. Please wait until the generation process is completely finished before deploying.  
    
* **Applying Updates:** Changes made in the editor are **not automatically reflected** on the already deployed live server. After modifying the code, you must click the **“Publish”** button again to overwrite the previous deployment with the new version.  
    
* **Initial Access Delay:** If you encounter a **404 error** immediately after deployment, it may be due to propagation delays across the global CDN network. Please wait **1–3 minutes**, clear your browser cache, and try accessing the URL again.

### 7.2 Connecting a Custom Domain {#custom-domain}
#### Configuring DNS and Automatic SSL (HTTPS) Setup
Instead of using the default VibeX URL, you can connect your own custom domain such as [**myapp.com**](http://myapp.com).

* **DNS Configuration:** Go to **Dashboard → Domain Settings**, enter your domain, and register the provided **A record** or **CNAME value** in the DNS settings of your domain provider.  
    
* **Propagation Time:** After updating DNS settings, it may take **up to 24–48 hours** for the changes to propagate across the global internet infrastructure.  
    
* **Automatic SSL (HTTPS):** Once the domain connection is completed successfully, VibeX will automatically issue and apply a **SSL security certificate**.  
  However, it may take **1–2 hours** after connecting the domain for the certificate to be issued, during which your browser may temporarily display a **security warning**.  
    
* **WWW Redirect:** To ensure both [**www.myapp.com**](http://www.myapp.com) and **myapp.com** work correctly, you should register both the **root domain** and the **www subdomain** in your DNS settings.

### 7.3 Downloading Source Code {#download-source}
#### Exporting the Project as a ZIP File and Running It Locally
This feature is useful if you want full ownership of your project code or plan to host it on another platform such as **Vercel**.

* **Download as ZIP:** Click the **Export icon** in the upper-right corner of the editor and select **“Download as ZIP.”** All source code and asset files will be downloaded to your local computer as a compressed archive.  
    
* **Running Locally:** To run the project locally, open the project folder in a terminal and execute: npm install, npm run dev This will install the required dependencies and start the development server.  
    
* **External Hosting Deployment (e.g., Vercel):** Direct one-click deployment to external platforms such as Vercel is not currently supported within VibeX. Instead, we recommend **pushing your code to your GitHub repository** and then connecting that repository to Vercel for deployment.
