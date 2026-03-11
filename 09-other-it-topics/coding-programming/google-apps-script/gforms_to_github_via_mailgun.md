# Google Forms to Mailgun and GitHub

Switching to **Mailgun** is a smart move for professional workflows. It decouples the automation from your personal Gmail account, provides better deliverability, and allows you to send from a custom domain like `tickets@yourcompany.com`.

### Prerequisites

1. **Mailgun API Key:** Found in your Mailgun Dashboard.
2. **Mailgun Domain:** The domain you’ve verified in Mailgun (e.g., `mg.yourdomain.com`).
3. **Base URL:** Use `https://api.mailgun.net/v3/` for US servers or `https://api.eu.mailgun.net/v3/` for EU servers.

------

### Updated Script with Mailgun Integration

I have updated the `CONFIG` and replaced the `sendSummaryEmail` function to use `UrlFetchApp` for the Mailgun API.

JavaScript

```javascript
/**
 * CONFIGURATION: Update these values
 */
const CONFIG = {
  // --- GitHub Config ---
  GITHUB_TOKEN: 'your_gh_token',
  ORG_NAME: 'your_org',
  REPO_NAME: 'your_repo',
  ASSIGNEE: 'username',
  DEFAULT_LABELS: ['support-ticket'],

  // --- Mailgun Config ---
  MAILGUN_API_KEY: 'key-xxxxxxxxxxxxxxxxxxxxxxx',
  MAILGUN_DOMAIN: 'your-domain.com',
  MAILGUN_BASE_URL: 'https://api.mailgun.net/v3/', // Use .eu. if in Europe
  FROM_EMAIL: 'Ticket System <tickets@your-domain.com>',
  TARGET_EMAIL: 'support@your-company.com'
};

function onFormSubmit(e) {
  const responses = e.response.getItemResponses();
  const userEmail = e.response.getRespondentEmail() || "No Email Provided";
  const timestamp = Utilities.formatDate(new Date(), Session.getScriptTimeZone(), "yyyy-MM-dd HH:mm");

  let markdownBody = `### Form Submission Summary\n**Submitted at:** ${timestamp}\n**User:** ${userEmail}\n\n---\n`;
  let attachments = [];
  let fileLinks = "";

  responses.forEach(itemResponse => {
    const item = itemResponse.getItem();
    const question = item.getTitle();
    const answer = itemResponse.getResponse();

    if (item.getType() === FormApp.ItemType.FILE_UPLOAD && answer && answer.length > 0) {
      fileLinks += `\n**${question}:**\n`;
      answer.forEach(fileId => {
        try {
          const file = DriveApp.getFileById(fileId);
          attachments.push(file.getBlob());
          file.setSharing(DriveApp.Access.ANYONE_WITH_LINK, DriveApp.Permission.VIEW);
          fileLinks += `- [View File: ${file.getName()}](${file.getUrl()})\n`;
        } catch (err) {
          fileLinks += `- *Error processing file: ${err.message}*\n`;
        }
      });
    } else {
      markdownBody += `**${question}**\n${answer}\n\n`;
    }
  });

  const fullMarkdown = markdownBody + (fileLinks ? "---\n### Attachments\n" + fileLinks : "");
  const subject = `New Ticket - ${timestamp}`;

  // 1. Send via Mailgun
  sendMailgunEmail(subject, fullMarkdown, attachments, userEmail);

  // 2. Create GitHub Issue
  createGitHubIssue(subject, fullMarkdown, CONFIG.DEFAULT_LABELS);
}

/**
 * Sends Email via Mailgun API
 */
function sendMailgunEmail(subject, markdownContent, attachments, userEmail) {
  const apiUrl = `${CONFIG.MAILGUN_BASE_URL}${CONFIG.MAILGUN_DOMAIN}/messages`;

  // Convert Markdown-ish text to basic HTML for email
  const htmlBody = markdownContent
    .replace(/\n/g, '<br>')
    .replace(/\*\*(.*?)\*\*/g, '<b>$1</b>')
    .replace(/### (.*?)(<br>|$)/g, '<h3>$1</h3>')
    .replace(/\[(.*?)\]\((.*?)\)/g, '<a href="$2">$1</a>');

  // Payload for Mailgun (using multipart/form-data for attachments)
  const payload = {
    'from': CONFIG.FROM_EMAIL,
    'to': CONFIG.TARGET_EMAIL,
    'h:Reply-To': userEmail, // High value: hit reply to email the user directly
    'subject': subject,
    'html': htmlBody
  };

  // Add attachments to the payload
  attachments.forEach((blob, index) => {
    payload[`attachment[${index}]`] = blob;
  });

  const options = {
    method: 'post',
    headers: {
      'Authorization': 'Basic ' + Utilities.base64Encode('api:' + CONFIG.MAILGUN_API_KEY)
    },
    payload: payload,
    muteHttpExceptions: true
  };

  try {
    const response = UrlFetchApp.fetch(apiUrl, options);
    if (response.getResponseCode() !== 200) {
      console.error("Mailgun Error: " + response.getContentText());
    } else {
      console.log("Mailgun Email Sent Successfully.");
    }
  } catch (err) {
    console.error("Mailgun Fetch Error: " + err.toString());
  }
}

/**
 * Creates the issue in the Organization Repository
 */
function createGitHubIssue(title, body, labels) {
  const url = `https://api.github.com/repos/${CONFIG.ORG_NAME}/${CONFIG.REPO_NAME}/issues`;
  const options = {
    method: 'post',
    contentType: 'application/json',
    headers: {
      'Authorization': 'Bearer ' + CONFIG.GITHUB_TOKEN,
      'Accept': 'application/vnd.github.v3+json',
      'User-Agent': 'Google-Apps-Script-Connector'
    },
    payload: JSON.stringify({
      title: title,
      body: body,
      assignees: [CONFIG.ASSIGNEE],
      labels: labels
    }),
    muteHttpExceptions: true
  };

  UrlFetchApp.fetch(url, options);
}
```

------

### Why this works better:

- **Identity Control:** The `FROM_EMAIL` can be any address associated with your Mailgun domain.
- **The "Reply-To" Header:** I’ve added `'h:Reply-To'`. When you receive the ticket in your inbox and click "Reply," it will automatically populate the user's email address instead of your automated domain.
- **Native Attachments:** Mailgun handles `attachments.push(file.getBlob())` effortlessly. It sends the actual file data, not just links.
- **Scalability:** Unlike Gmail, which has daily sending limits (around 100–1500 per day), Mailgun can handle thousands of submissions without breaking a sweat.

### Note on Basic Auth

Mailgun uses **Basic Authentication**. The script handles this by encoding your API key: `Utilities.base64Encode('api:' + CONFIG.MAILGUN_API_KEY)`. This is the standard way to talk to Mailgun's servers safely.

