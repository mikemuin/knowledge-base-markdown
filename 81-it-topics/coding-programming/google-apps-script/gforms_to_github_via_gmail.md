# Google Forms to Email and GitHub

Here is the complete, refined, and field-agnostic script. This version handles **Organization-owned repos**, **dynamic file uploads** (both as email attachments and Drive links in GitHub), and **automatic GitHub labeling**.

### Complete Google Apps Script

```javascript
/**
 * CONFIGURATION: Update these values before running
 */
const CONFIG = {
  GITHUB_TOKEN: 'your_personal_access_token_here', // Requires 'repo' scope
  ORG_NAME: 'your_organization_name',             // The GitHub Org name
  REPO_NAME: 'your_repo_name',                   // The specific Repository name
  ASSIGNEE: 'your_github_username',              // GitHub username to assign to
  TARGET_EMAIL: 'support@example.com',           // Email to receive the summary
  DEFAULT_LABELS: ['support-ticket', 'automated'] // Labels to apply on creation
};

/**
 * Main Trigger Function
 * Setup: In Apps Script, go to Triggers (clock icon) -> Add Trigger
 * -> Choose 'onFormSubmit' -> Event Source: 'From form' -> Event Type: 'On form submit'
 */
function onFormSubmit(e) {
  const responses = e.response.getItemResponses();
  const userEmail = e.response.getRespondentEmail() || "Anonymous";
  const timestamp = Utilities.formatDate(new Date(), Session.getScriptTimeZone(), "yyyy-MM-dd HH:mm");

  let markdownBody = `### Form Submission Summary\n**Submitted at:** ${timestamp}\n**User:** ${userEmail}\n\n---\n`;
  let attachments = [];
  let fileLinksMarkdown = "";

  // Dynamic iteration through all form fields
  responses.forEach(itemResponse => {
    const item = itemResponse.getItem();
    const question = item.getTitle();
    const answer = itemResponse.getResponse();

    // Check if the item is a File Upload type
    if (item.getType() === FormApp.ItemType.FILE_UPLOAD && answer && answer.length > 0) {
      fileLinksMarkdown += `\n**${question}:**\n`;

      // 'answer' for file upload is an array of File IDs
      answer.forEach(fileId => {
        try {
          const file = DriveApp.getFileById(fileId);

          // 1. Prepare for Email: Get the file data (blob)
          attachments.push(file.getBlob());

          // 2. Prepare for GitHub: Set permissions so the link is accessible
          // Note: This makes the file "Anyone with the link can view"
          file.setSharing(DriveApp.Access.ANYONE_WITH_LINK, DriveApp.Permission.VIEW);

          fileLinksMarkdown += `- [View File: ${file.getName()}](${file.getUrl()})\n`;
        } catch (err) {
          fileLinksMarkdown += `- *Error processing file (${fileId}): ${err.message}*\n`;
        }
      });
    } else {
      // Standard text/choice/date fields
      markdownBody += `**${question}**\n${answer}\n\n`;
    }
  });

  // Combine content and file links
  const fullMarkdown = markdownBody + (fileLinksMarkdown ? "---\n### Attachments\n" + fileLinksMarkdown : "");
  const subject = `New Ticket - ${timestamp}`;

  // 1. Send the Email
  sendSummaryEmail(subject, fullMarkdown, attachments);

  // 2. Create the GitHub Issue
  createGitHubIssue(subject, fullMarkdown, CONFIG.DEFAULT_LABELS);
}

/**
 * Sends an HTML formatted email with attachments
 */
function sendSummaryEmail(subject, markdownContent, attachments) {
  try {
    // Convert basic markdown to simple HTML for the email client
    const htmlBody = markdownContent
      .replace(/\n/g, '<br>')
      .replace(/\*\*(.*?)\*\*/g, '<b>$1</b>')
      .replace(/### (.*?)(<br>|$)/g, '<h3>$1</h3>')
      .replace(/\[(.*?)\]\((.*?)\)/g, '<a href="$2">$1</a>');

    MailApp.sendEmail({
      to: CONFIG.TARGET_EMAIL,
      subject: subject,
      htmlBody: htmlBody,
      attachments: attachments
    });
    console.log("Email sent successfully.");
  } catch (err) {
    console.error("Email Error: " + err.toString());
  }
}

/**
 * Creates the issue in the Organization Repository
 */
function createGitHubIssue(title, body, labels) {
  const url = `https://api.github.com/repos/${CONFIG.ORG_NAME}/${CONFIG.REPO_NAME}/issues`;

  const payload = {
    title: title,
    body: body,
    assignees: [CONFIG.ASSIGNEE],
    labels: labels
  };

  const options = {
    method: 'post',
    contentType: 'application/json',
    headers: {
      'Authorization': 'Bearer ' + CONFIG.GITHUB_TOKEN,
      'Accept': 'application/vnd.github.v3+json',
      'User-Agent': 'Google-Apps-Script-Connector'
    },
    payload: JSON.stringify(payload),
    muteHttpExceptions: true
  };

  try {
    const response = UrlFetchApp.fetch(url, options);
    const code = response.getResponseCode();
    const result = JSON.parse(response.getContentText());

    if (code === 201) {
      console.log(`GitHub Issue Created: ${result.html_url}`);
      return result.html_url;
    } else {
      console.error(`GitHub API Error (${code}): ${response.getContentText()}`);
    }
  } catch (err) {
    console.error("GitHub Network Error: " + err.toString());
  }
  return null;
}
```

------

### Important Developer Notes:

1. **Field Agnostic Strategy:** The script uses `e.response.getItemResponses()` to crawl the form. If you add, delete, or rename questions, the script will simply output whatever it finds.
2. **Organization Permissions:** Ensure your `GITHUB_TOKEN` is a **Personal Access Token (Classic)** or **Fine-grained Token**. If it's a Classic token, it needs the `repo` scope. If your Org uses **SAML SSO**, you must click "Configure SSO" next to the token in your GitHub settings to authorize it for that specific Org.
3. **File Sharing:** Since GitHub Issues cannot "host" Drive files, the script changes the file permission to **"Anyone with the link can view."** This is necessary so your team can actually open the links from within the GitHub interface.
4. **HTML Email:** I included a simple regex-based markdown-to-html converter for the email section. This ensures your Drive links appear as clickable text (e.g., *View File: image.png*) rather than messy raw URLs.

### Next Steps

1. Paste this into your Google Apps Script editor.
2. Update the `CONFIG` variables.
3. **Set the Trigger** via the clock icon in the sidebar (this is the most common step people forget!).

**Would you like me to help you format the GitHub issue body into a specific table layout to make it even more readable?**