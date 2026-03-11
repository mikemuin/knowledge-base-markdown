# Project Milestones Countdown

The following guide provides a complete, end-to-end walkthrough for deploying the **Project Milestone Tracker**. These instructions are designed so that any team member—regardless of technical background—can set up the system from scratch.

------

## Part 1: Spreadsheet Preparation

The script relies on a specific data structure. If the headers or tab names don't match, the script will not be able to "read" your data.

1. **Create a New Sheet:** Open [sheets.new](https://sheets.new/).

2. **Rename the Tab:** Double-click the tab at the bottom (usually "Sheet1") and rename it exactly to **`Milestones`**.

3. **Define Headers:** In Row 1, enter the following:

   - **Cell A1:** `Group` (e.g., Marketing, Development, HR)
   - **Cell B1:** `Milestone` (e.g., Launch Date, Contract Review)
   - **Cell C1:** `Date`

4. **Format the Date Column:** Select all of **Column C**, then go to **Format > Number > Date**.

   > **Note:** The script will ignore any row whose date is not a valid Google Sheets date format.

------

## Part 2: Installing the Scripts

You will be using two files: `Code.gs` (the brain) and `Index.html` (the face).

1. **Open the Editor:** In your Sheet, go to **Extensions > Apps Script**.
2. **Rename Project:** Click on "Untitled project" at the top left and name it **`Milestone Tracker`**.
3. **The Backend (`Code.gs`):**
   - Delete the default `function myFunction() {...}` code.
   - Paste the **Code.gs** script provided below.
   - **Important:** Update the `SETTINGS.EMAIL.RECIPIENTS` line with the actual email addresses of your team.
4. **The Frontend (`Index.html`):**
   - Click the **+** icon next to "Files" in the left sidebar.
   - Select **HTML**.
   - Name it exactly **`Index`** (the editor adds the .html for you).
   - Paste the **Index.html** script provided below.
5. **Save:** Click the **Disk Icon** (Save Project) in the toolbar.

------

## Part 3: Deploying the Web App

Even though the script is written, it isn't "live" as a website until you deploy it.

1. **New Deployment:** Click the blue **Deploy** button > **New deployment**.
2. **Select Type:** Click the gear icon (Select type) and choose **Web app**.
3. **Configuration:**
   - **Description:** `Milestone Dashboard V1`
   - **Execute as:** `Me`
   - **Who has access:** `Anyone` (This allows your team to view the link without you managing individual permissions).
4. **Authorize:** Click **Deploy**. A popup will appear asking for permissions.
   - Select your Google Account.
   - You will see a "Google hasn't verified this app" warning. Click **Advanced** > **Go to Milestone Tracker (unsafe)**.
   - Click **Allow**.
5. **Save the URL:** Copy the **Web App URL** that appears. This is the link you will share with your team.

------

## Part 4: Automating the Weekly Email

To have emails sent automatically every Monday, you must set up a "Trigger."

1. **Open Triggers:** In the Apps Script sidebar (left), click the **Clock icon (Triggers)**.
2. **Add Trigger:** Click the **+ Add Trigger** button in the bottom right.
3. **Settings:**
   - **Choose function to run:** `sendWeeklyDigest`
   - **Choose deployment:** `Head`
   - **Event source:** `Time-driven`
   - **Type of time based trigger:** `Week timer`
   - **Day of week:** `Monday`
   - **Time of day:** `8am to 9am`
4. **Save:** Click Save.

------

## The Complete Scripts

### File: `Code.gs`

JavaScript

```javascript
const SETTINGS = {
  SHEET_NAME: 'Milestones',
  COLUMNS: {
    GROUP: 0,
    MILESTONE: 1,
    DATE: 2
  },
  UI: {
    TITLE: 'Project Milestone Tracker',
    THRESHOLD_URGENT: 7,
    THRESHOLD_SOON: 14
  },
  EMAIL: {
    RECIPIENTS: 'team@example.com', // <--- UPDATE THIS
    SUBJECT: 'Weekly Project Milestone Digest 🚀'
  }
};

function doGet() {
  const template = HtmlService.createTemplateFromFile('Index');
  template.title = SETTINGS.UI.TITLE;
  return template.evaluate()
    .setTitle(SETTINGS.UI.TITLE)
    .setXFrameOptionsMode(HtmlService.XFrameOptionsMode.ALLOWALL)
    .addMetaTag('viewport', 'width=device-width, initial-scale=1');
}

function getMilestoneData() {
  try {
    const ss = SpreadsheetApp.getActiveSpreadsheet();
    const sheet = ss.getSheetByName(SETTINGS.SHEET_NAME);
    if (!sheet) throw new Error(`Tab "${SETTINGS.SHEET_NAME}" not found.`);
    const data = sheet.getDataRange().getValues();
    if (data.length <= 1) return [];
    data.shift();
    const tz = Session.getScriptTimeZone();
    return data
      .filter(row => row[SETTINGS.COLUMNS.DATE] instanceof Date && !isNaN(row[SETTINGS.COLUMNS.DATE]))
      .map(row => ({
        group: row[SETTINGS.COLUMNS.GROUP] || 'General',
        task: row[SETTINGS.COLUMNS.MILESTONE] || 'Unnamed Task',
        dateStr: Utilities.formatDate(row[SETTINGS.COLUMNS.DATE], tz, "MMM dd, yyyy"),
        timestamp: row[SETTINGS.COLUMNS.DATE].getTime(),
        urgentLimit: SETTINGS.UI.THRESHOLD_URGENT,
        soonLimit: SETTINGS.UI.THRESHOLD_SOON
      }));
  } catch (e) {
    console.error("Error: " + e.toString());
    return [];
  }
}

function sendWeeklyDigest() {
  const data = getMilestoneData();
  if (data.length === 0) return;
  data.sort((a, b) => a.timestamp - b.timestamp);
  let emailBody = `<div style="font-family: sans-serif; padding: 20px; border: 1px solid #eee; border-radius: 10px; max-width: 600px;">`;
  emailBody += `<h2 style="color: #4f46e5; margin-top: 0;">${SETTINGS.EMAIL.SUBJECT}</h2>`;
  data.forEach(m => {
    const today = new Date(); today.setHours(0,0,0,0);
    const mDate = new Date(m.timestamp); mDate.setHours(0,0,0,0);
    const daysLeft = Math.round((mDate - today) / (1000 * 60 * 60 * 24));
    if (daysLeft >= -1) {
      const color = daysLeft <= SETTINGS.UI.THRESHOLD_URGENT ? '#ef4444' : (daysLeft <= SETTINGS.UI.THRESHOLD_SOON ? '#f59e0b' : '#10b981');
      emailBody += `<div style="margin-bottom: 15px; border-left: 4px solid ${color}; padding: 5px 0 5px 15px;">
          <strong style="font-size: 1.1em; color: #111;">${m.task}</strong> <br>
          <span style="color: #666; font-size: 0.9em;">${m.group} | Due: ${m.dateStr}</span> <br>
          <b style="color: ${color}; font-size: 0.9em;">${daysLeft < 0 ? 'DUE NOW' : daysLeft + ' days remaining'}</b>
        </div>`;
    }
  });
  const url = ScriptApp.getService().getUrl();
  emailBody += `<br><a href="${url}" style="display: inline-block; background: #4f46e5; color: white; padding: 12px 25px; text-decoration: none; border-radius: 5px; font-weight: bold;">Open Dashboard</a></div>`;
  MailApp.sendEmail({ to: SETTINGS.EMAIL.RECIPIENTS, subject: SETTINGS.EMAIL.SUBJECT, htmlBody: emailBody });
}

function onOpen() {
  SpreadsheetApp.getUi().createMenu('🚀 Apps')
    .addItem('View Dashboard Link', 'showUrl')
    .addItem('Send Test Email Now', 'sendWeeklyDigest')
    .addToUi();
}

function showUrl() {
  const url = ScriptApp.getService().getUrl();
  const html = `<p>Dashboard URL:</p><input type="text" value="${url}" style="width: 100%" readonly onclick="this.select()">`;
  SpreadsheetApp.getUi().showModalDialog(HtmlService.createHtmlOutput(html).setWidth(400).setHeight(150), 'Web App Link');
}
```

### File: `Index.html`

HTML

```html
<!DOCTYPE html>
<html>
  <head>
    <base target="_top">
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
      :root { --bg: #f9fafb; --card: #ffffff; --text: #111827; --primary: #4f46e5; --danger: #ef4444; --warning: #f59e0b; --success: #10b981; }
      body { font-family: 'Inter', sans-serif; background: var(--bg); color: var(--text); padding: 2rem; margin: 0; }
      .wrapper { max-width: 800px; margin: 0 auto; }
      .group-block { background: var(--card); border-radius: 12px; padding: 1.5rem; margin-bottom: 2rem; box-shadow: 0 1px 3px rgba(0,0,0,0.1); border: 1px solid #e5e7eb; }
      .group-name { font-size: 0.8rem; font-weight: 700; color: #6b7280; text-transform: uppercase; letter-spacing: 0.05em; margin-bottom: 1rem; }
      .row { display: flex; justify-content: space-between; align-items: center; padding: 1rem 0; border-top: 1px solid #f3f4f6; }
      .task-meta { display: flex; flex-direction: column; }
      .task-name { font-weight: 600; color: var(--text); }
      .task-date { font-size: 0.8rem; color: #6b7280; }
      .counter { font-weight: 700; padding: 0.5rem 1rem; border-radius: 6px; font-size: 0.8rem; min-width: 100px; text-align: center; }
      .status-urgent { background: #fee2e2; color: var(--danger); }
      .status-soon { background: #fef3c7; color: var(--warning); }
      .status-safe { background: #d1fae5; color: var(--success); }
      .status-past { background: #f3f4f6; color: #6b7280; }
      footer { text-align: center; font-size: 0.8rem; color: #9ca3af; margin-top: 2rem; }
    </style>
  </head>
  <body>
    <div class="wrapper">
      <h1 style="margin-bottom: 2rem; font-weight: 800;"><?= title ?></h1>
      <div id="content">Syncing milestones...</div>
      <footer id="footer"></footer>
    </div>
    <script>
      const MS_PER_DAY = 1000 * 60 * 60 * 24;
      window.onload = () => {
        google.script.run.withSuccessHandler(render).withFailureHandler(err => { document.getElementById('content').innerHTML = "Error: " + err; }).getMilestoneData();
      };
      function render(data) {
        const container = document.getElementById('content');
        if (!data || data.length === 0) { container.innerHTML = "<h3>No data found.</h3>"; return; }
        data.sort((a, b) => a.timestamp - b.timestamp);
        const groups = data.reduce((acc, item) => { if (!acc[item.group]) acc[item.group] = []; acc[item.group].push(item); return acc; }, {});
        let html = '';
        const today = new Date(); today.setHours(0,0,0,0);
        for (const [name, milestones] of Object.entries(groups)) {
          html += `<div class="group-block"><div class="group-name">${name}</div>`;
          milestones.forEach(m => {
            const mDate = new Date(m.timestamp); mDate.setHours(0,0,0,0);
            const daysLeft = Math.round((mDate - today) / MS_PER_DAY);
            let style = 'status-safe'; let label = `${daysLeft} Days Left`;
            if (daysLeft < 0) { style = 'status-past'; label = 'Passed'; }
            else if (daysLeft === 0) { style = 'status-urgent'; label = 'DUE TODAY'; }
            else if (daysLeft <= m.urgentLimit) { style = 'status-urgent'; label = `${daysLeft} Days`; }
            else if (daysLeft <= m.soonLimit) { style = 'status-soon'; label = `${daysLeft} Days`; }
            html += `<div class="row"><div class="task-meta"><span class="task-name">${m.task}</span><span class="task-date">Due: ${m.dateStr}</span></div><span class="counter ${style}">${label}</span></div>`;
          });
          html += `</div>`;
        }
        container.innerHTML = html;
        document.getElementById('footer').innerText = "Last Updated: " + new Date().toLocaleString();
      }
    </script>
  </body>
</html>
```

------

## Usage Tips

- **The Custom Menu:** Once you refresh your Spreadsheet, you will see a menu called **Apps**. You can use this to quickly copy the dashboard link or force-send a test email to yourself.
- **Modifying Column Order:** If you ever decide to move your columns (e.g., put Date in Column A), just update the `COLUMNS` numbers in the `SETTINGS` at the top of `Code.gs`.
- **Updating the Web App:** If you change the code in the future, you **must** click **Deploy > Manage Deployments > Pencil Icon > New Version**, then click **Deploy**. Saving the script alone does not update the live website.

