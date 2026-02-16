# On-Demand GAPS Call

Let's break down Method 4: **Deploying your Gmail script as a Web App** for on-demand triggering via a URL. This is a powerful method if you want to trigger your script from outside the Apps Script editor or a Google Sheet, for example, from a simple web browser, a bookmark, or another application that can make HTTP requests.

Here's a step-by-step guide:

### Step-by-Step Guide: Deploying a Gmail Script as a Web App

**Prerequisites:**

- You have a Google Account.
- You have a Gmail script function ready (e.g., `sendMyCustomEmail()`, `processUnreadLabeledEmails()`).

------

**Step 1: Open Your Google Apps Script Project**

1. Go to `script.google.com`.
2. Open your existing Gmail script project. If you don't have one, create a new project by going to `script.google.com/create`.
3. Name your project (e.g., "GmailWebAppTrigger").

**Step 2: Add `doGet()` or `doPost()` Function to Your Script**

For a web app to respond to HTTP requests, it needs a special function:

- `doGet(e)`: This function is executed when the web app URL is accessed via a `GET` request (e.g., by typing the URL in a browser, or a simple link click).
- `doPost(e)`: This function is executed when the web app URL receives a `POST` request (typically used when sending data from a form or another application).

For simple on-demand triggering, `doGet()` is usually sufficient.

1. In your `Code.gs` file, add the `doGet(e)` function. Inside `doGet(e)`, you will call your existing Gmail script function.

**Example `Code.gs` content:**

JavaScript

   ```js
   // This is your existing Gmail function that you want to trigger
   function sendMyCustomEmail() {
     var recipient = "your_email@example.com"; // **IMPORTANT: Replace with a real email for testing**
     var subject = "Automated Email from Web App Trigger";
     var body = "Hello, this email was sent via a Web App GET request using Google Apps Script!";

     try {
       GmailApp.sendEmail(recipient, subject, body);
       Logger.log("Email sent successfully to: " + recipient);
     } catch (e) {
       Logger.log("Error sending email: " + e.toString());
     }
   }

   // This is the special function that makes your script a Web App
   function doGet(e) {
     // Call your Gmail script function here
     sendMyCustomEmail();

     // Return a simple text response to the browser/caller
     // This is important so the web app doesn't just hang or show an error
     return ContentService.createTextOutput("Gmail script 'sendMyCustomEmail' executed successfully!");
   }
   ```

2. **Save your script:** Click the save icon (floppy disk) or press `Ctrl + S` (Windows) / `Cmd + S` (Mac).

**Step 3: Deploy Your Script as a Web App**

1. In the Apps Script editor, click on the **"Deploy"** button (top right, usually next to "Run").
2. Select **"New deployment"** from the dropdown.
3. In the "Select type" dialog, click the gear icon and choose **"Web app"**.
4. Configure the deployment settings:
   - **Description (optional):** Give your deployment a name (e.g., "Initial Deployment", "Version 1.0").
   - **Execute as:** This is crucial. Choose **"Me"** (your email address). This means the script will run under *your* permissions and quotas.
   - **Who has access:** This determines who can trigger your web app.
     - **"Me only"**: Only you can access the URL.
     - **"Anyone with Google account"**: Any authenticated Google user can access it.
     - **"Anyone"**: *No authentication required*. Anyone with the URL can access it. **Use "Anyone" with extreme caution** as it exposes your endpoint publicly. For simple personal on-demand triggers, "Me only" or "Anyone with Google account" is safer. For this example, let's go with "Anyone" for ease of testing in a browser, but remember the security implications.
5. Click the **"Deploy"** button.

**Step 4: Authorize the Script (First-Time Deployment)**

1. The first time you deploy, a "Review permissions" dialog will appear. Click on it.
2. Select the Google account you are using for the script.
3. You'll see a warning "Google hasn't verified this app." This is normal for your own personal scripts. Click **"Go to [Your Project Name] (unsafe)"** (don't worry, it's safe because it's *your* script).
4. Review the permissions the script needs (e.g., "Send email as you"). If you're comfortable, click **"Allow"**.

**Step 5: Get the Web App URL**

1. After successful deployment and authorization, you'll see a "Deployment succeeded!" dialog.
2. Copy the **"Web app URL"** provided. This is the URL you will use to trigger your script. It will look something like: `https://script.google.com/macros/s/AKfycb.../exec`
3. Click "Done".

**Step 6: Trigger Your Script On Demand**

Now that you have the Web App URL, you can trigger your `sendMyCustomEmail` function by accessing this URL.

1. **Open a Web Browser:** Paste the copied "Web app URL" into your browser's address bar and press Enter.
2. **Observe the Result:**
   - If successful, you should see the text "Gmail script 'sendMyCustomEmail' executed successfully!" (or whatever message you set in `ContentService.createTextOutput()`).
   - Check your `your_email@example.com` inbox. You should receive the email sent by your script.
   - You can also go back to the Apps Script editor, click on **"Executions"** in the left sidebar to see a log of the script's run, including any `Logger.log()` messages.

------

**Important Security Considerations & Best Practices for Web Apps:**

- **"Who has access" is critical:**
  - **"Anyone"**: Means *anyone in the world* with that URL can trigger your script. This is highly risky if your script performs sensitive actions (like deleting emails, accessing private data, or sending emails to many recipients). Only use this if you fully understand and accept the risks.
  - **"Anyone with Google account"**: Requires the user to be logged into a Google account. It's more secure than "Anyone" but still allows any authenticated Google user to trigger it.
  - **"Me only"**: Only you (the developer) can trigger the URL. This is the *safest* option for personal, on-demand use from a browser or bookmark.
- **Input Validation:** If your `doGet` or `doPost` function will be processing parameters (`e.parameter`), *always validate and sanitize inputs* to prevent malicious injections or unexpected behavior.
- **Error Handling:** Implement robust `try...catch` blocks within your `doGet` or `doPost` and the functions they call. Return informative error messages or status codes to the caller.
- **Quotas:** Be mindful of Apps Script daily quotas. Repeatedly triggering a script that sends many emails could hit your Gmail sending limits.
- **Version Control:** When you make changes to your script, you'll need to create a **new deployment** to update the running web app. You can manage deployments from `Deploy > Manage deployments`. Use version numbers or descriptive names for your deployments.

By following these steps, you'll have a fully functional web app that allows you to trigger your Gmail script on demand simply by accessing a URL!