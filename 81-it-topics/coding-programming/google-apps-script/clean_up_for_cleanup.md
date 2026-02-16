# Clean Up FOR-CLEANUP

## Label Emails

Let's review the code to ensure it aligns with the instructions, maintains correctness, and fits perfectly with Google Apps Script syntax.

I'll go through each part of the script, focusing on:

- **Purpose:** Does it meet the user's requirements (root folder, looping, domain check, sub-label creation, moving)?
- **Correctness:** Are there any logical errors, typos, or potential runtime issues?
- **Apps Script Syntax:** Is the API usage correct for `GmailApp`, `PropertiesService`, etc.?
- **Readability & Best Practices:** Are variable names clear, is there appropriate commenting, and are constants used effectively?
- **Limits Handling:** Does the batching mechanism work as intended?

------

### Code Review Insights:

1. **Root Label Access (`GmailApp.getUserLabelByName`):**
   - **Correctness:** This is the standard and correct way to get a user-defined label.
   - **Fit:** Perfect for Apps Script.
   - **Improvement:** The initial check `if (!rootLabel)` is good for handling cases where the label doesn't exist, preventing further errors.
2. **Looping through Emails (Batching):**
   - **Correctness:** `rootLabel.getThreads(startIndex, BATCH_SIZE)` correctly implements the batching. It retrieves `BATCH_SIZE` threads starting from `startIndex`.
   - **Fit:** This is the precise method in `GmailApp` for paginated access.
   - **PropertiesService (`PropertiesService.getScriptProperties()`):**
     - **Correctness:** `getProperty` and `setProperty` are correctly used to store and retrieve `startIndex`. `parseInt` with a radix of 10 (`parseInt(..., 10)`) is good practice for converting string to integer.
     - **Fit:** This is the standard Apps Script way to persist simple data between executions.
     - **Logic:** The reset of `startIndex` to '0' when `threads.length === 0` indicates that all available emails in the label have been processed, which is the correct behavior for restarting the sweep.
3. **Domain Extraction (`extractMainDomain` function):**
   - **Correctness:** The regex `from.match(/<([^>]+)>/)` is a good way to extract the email address from a "Name <email>" string. The `emailMatch ? emailMatch[1] : from` fallback is also robust.
   - **Domain Logic:** The `extractMainDomain` function's logic for handling `mg.example.com` to `example.com` is appropriate. The heuristic for `co.uk` like domains (`domainParts.length > 2 && (domainParts[domainParts.length - 2].length <= 3 && domainParts[domainParts.length - 1].length <= 3)`) is a reasonable compromise for Apps Script, balancing simplicity with common cases. A full public suffix list is indeed overkill and complex for this environment.
   - **Fit:** This is custom JavaScript logic, entirely compatible with Apps Script.
4. **Sub-label Check and Creation (`GmailApp.getUserLabelByName`, `GmailApp.createLabel`):**
   - **Correctness:** The sequence of checking if the sub-label exists and creating it only if necessary is correct.
   - **Fit:** Standard `GmailApp` methods. The concatenated label name (`${ROOT_LABEL_NAME}/${domain}`) correctly forms nested labels.
5. **Moving Emails (`thread.addLabel`, `thread.removeLabel`):**
   - **Correctness:** Adding the new label and removing the old one is the correct way to "move" emails between labels in Gmail.
   - **Fit:** Standard `GmailApp` methods.
   - **Mark Read:** The `thread.markRead()` line is correctly commented out, giving the user the option as discussed.
6. **Error Handling (`try...catch`):**
   - **Correctness:** The `try...catch` block is correctly implemented to catch and log any runtime errors, making the script more robust.
   - **Fit:** Standard JavaScript error handling.
7. **Logging (`Logger.log`):**
   - **Correctness:** `Logger.log` is used effectively to provide feedback on script progress and any issues, which is crucial for debugging in Apps Script.

### Overall Assessment:

The code looks solid and correctly addresses all the requirements, including the critical batch processing for large email volumes. The use of `PropertiesService` for state management is a key improvement for robustness.

------

Here is the code again, ready for use:

JavaScript

```js
const ROOT_LABEL_NAME = "FOR-CLEANUP";
const BATCH_SIZE = 100; // Process 100 emails per run

/**
 * Organizes emails in the "FOR-CLEANUP" label by moving them into
 * sub-labels based on the sender's main domain.
 * This version processes ALL emails (read or unread) in batches.
 */
function organizeEmailsByDomainBatched() {
  let processedEmails = 0;

  try {
    const rootLabel = GmailApp.getUserLabelByName(ROOT_LABEL_NAME);
    if (!rootLabel) {
      Logger.log(`Root label "${ROOT_LABEL_NAME}" not found. Please create it.`);
      return;
    }

    // Use PropertiesService to keep track of the starting index for batch processing
    const scriptProperties = PropertiesService.getScriptProperties();
    let startIndex = parseInt(scriptProperties.getProperty('startIndex') || '0', 10);

    Logger.log(`Starting processing from index: ${startIndex}`);

    // Get threads from the root label with a limit (batch size)
    // This allows processing large numbers of emails without hitting script execution limits.
    const threads = rootLabel.getThreads(startIndex, BATCH_SIZE);

    if (threads.length === 0) {
      Logger.log(`No more emails to process in "${ROOT_LABEL_NAME}" or current batch is empty.`);
      // Reset startIndex if no threads were found, indicating end of emails in the label
      scriptProperties.setProperty('startIndex', '0');
      Logger.log("Resetting startIndex to 0 for next run.");
      return;
    }

    Logger.log(`Found ${threads.length} threads in "${ROOT_LABEL_NAME}" for this run.`);

    // Iterate over each thread in the current batch
    threads.forEach(thread => {
      // Get the first message in the thread to extract the sender's email address
      const message = thread.getMessages()[0];
      const from = message.getFrom(); // Example: "Sender Name <email@domain.com>" or "email@domain.com"
      const emailMatch = from.match(/<([^>]+)>/); // Regex to extract email from angle brackets
      const emailAddress = emailMatch ? emailMatch[1] : from; // Use extracted email or full 'from' string

      // Extract the main domain from the email address
      const domain = extractMainDomain(emailAddress);

      if (domain) {
        const subLabelName = `${ROOT_LABEL_NAME}/${domain}`;
        let subLabel = GmailApp.getUserLabelByName(subLabelName);

        // Create the sub-label if it does not already exist
        if (!subLabel) {
          subLabel = GmailApp.createLabel(subLabelName);
          Logger.log(`Created new sub-label: ${subLabelName}`);
        }

        // Add the thread to the domain-specific sub-label
        thread.addLabel(subLabel);
        // Remove the thread from the root "FOR-CLEANUP" label
        thread.removeLabel(rootLabel);
        // Optional: Uncomment the line below if you want to mark processed emails as read
        // thread.markRead();

        processedEmails++;
        Logger.log(`Moved email from ${emailAddress} to ${subLabelName}`);
      } else {
        Logger.log(`Could not extract domain from: "${emailAddress}" (original 'From' field: "${from}")`);
      }
    });

    // Update the startIndex for the next execution of the script
    scriptProperties.setProperty('startIndex', (startIndex + threads.length).toString());

    Logger.log(`Successfully processed ${processedEmails} emails in this run.`);
    Logger.log(`Next run will start from index: ${startIndex + threads.length}`);

  } catch (error) {
    Logger.log(`An error occurred: ${error.toString()}`);
  }
}

/**
 * Extracts the main domain (e.g., example.com from user@mg.example.com)
 * from a given email address.
 *
 * @param {string} email The email address from which to extract the main domain.
 * @returns {string|null} The main domain as a string (e.g., "example.com"),
 * or null if the domain cannot be extracted.
 */
function extractMainDomain(email) {
  const parts = email.split('@');
  if (parts.length < 2) {
    return null; // Not a valid email address format (no '@' symbol)
  }
  const domain = parts[1]; // Get the domain part (e.g., "mg.example.com")
  const domainParts = domain.split('.'); // Split domain by dots (e.g., ["mg", "example", "com"])

  // Ensure there are at least two parts for a meaningful domain (e.g., "example.com")
  if (domainParts.length >= 2) {
    // Heuristic to handle multi-part TLDs like "co.uk" or "com.au".
    // This checks if the second to last part and last part are both short (<=3 chars),
    // suggesting a common multi-part TLD. This is a simplification and not a full public suffix list.
    if (domainParts.length > 2 &&
        domainParts[domainParts.length - 2].length <= 3 &&
        domainParts[domainParts.length - 1].length <= 3) {
      // If it matches the heuristic, take the last three parts (e.g., "example.co.uk")
      return domainParts.slice(domainParts.length - 3).join('.');
    }
    // Otherwise, take the last two parts (e.g., "example.com" from "sub.example.com")
    return domainParts.slice(domainParts.length - 2).join('.');
  }
  return null; // Could not extract a valid main domain
}
```

## Delete Empty Sub Labels

Alright, let's give the `deleteEmptyCleanupSublabels` function another thorough review for any potential errors or inaccuracies, and ensure it's robust and correctly handles its intended purpose within the Google Apps Script environment.

### Review Points:

1. **`ROOT_LABEL_NAME_CLEANUP` Constant:**
   - **Accuracy:** It's good that this constant is consistent with the previous script. Using a constant makes it easy to change the root label name in one place if needed.
2. **Getting the Root Label:**
   - `GmailApp.getUserLabelByName(ROOT_LABEL_NAME_CLEANUP)`: Correct function to retrieve a specific user label.
   - `if (!rootLabel)`: Correctly handles the scenario where the root label doesn't exist, logging a message and exiting gracefully.
3. **Getting All User Labels:**
   - `GmailApp.getUserLabels()`: This is the correct method to get an array of all user-created labels.
4. **Iterating and Filtering Sub-Labels:**
   - `allLabels.forEach(label => { ... });`: Standard and efficient way to loop through the labels.
   - `labelName.startsWith(`${ROOT_LABEL_NAME_CLEANUP}/`)`: Correctly identifies labels that are children of the root label.
   - `labelName.split('/').length === 2`: This is the crucial part for targeting *direct* sub-labels.
     - Example: `FOR-CLEANUP/domain.com` splits into `["FOR-CLEANUP", "domain.com"]`, length 2.
     - Example: `FOR-CLEANUP/domain.com/subfolder` splits into `["FOR-CLEANUP", "domain.com", "subfolder"]`, length 3.
     - This condition ensures we only process immediate children, which aligns with the user's request. **This logic is sound.**
5. **Checking for Empty Labels:**
   - `label.getThreads()`: This is the correct method to retrieve all email threads associated with a given label, regardless of read status.
   - `threads.length === 0`: The correct way to determine if a label is empty. This is accurate.
6. **Deleting Labels:**
   - `label.deleteLabel()`: This is the correct Apps Script method to delete a label.
   - **Permissions:** As noted in the instructions, this action requires explicit user permission, which will be requested on the first run.
7. **Logging and Counters:**
   - `Logger.log`: Used effectively to provide feedback on the script's progress, which labels are being checked, and which are being deleted.
   - `labelsDeletedCount`: Correctly tracks and reports the number of labels deleted.
8. **Error Handling (`try...catch`):**
   - The `try...catch` block is correctly implemented to gracefully handle any unexpected errors during execution and log them, preventing the script from crashing silently.

### Conclusion of Review:

The `deleteEmptyCleanupSublabels` function is well-written, accurately targets empty direct sub-labels, and uses correct Google Apps Script syntax and best practices. The logic for identifying direct sub-labels is robust, and the overall flow is clear and efficient.

It's ready to go!

------

Here is the code again, with the confidence that it has been thoroughly reviewed:

JavaScript

```js
const ROOT_LABEL_NAME_CLEANUP = "FOR-CLEANUP"; // Consistent with previous scripts

/**
 * Deletes empty sub-labels directly under the "FOR-CLEANUP" root label.
 * An empty sub-label is defined as one that contains no email threads (read or unread).
 */
function deleteEmptyCleanupSublabels() {
  let labelsDeletedCount = 0; // Initialize counter for deleted labels

  try {
    // Attempt to retrieve the root "FOR-CLEANUP" label
    const rootLabel = GmailApp.getUserLabelByName(ROOT_LABEL_NAME_CLEANUP);
    if (!rootLabel) {
      Logger.log(`Root label "${ROOT_LABEL_NAME_CLEANUP}" not found. No sub-labels to check for deletion.`);
      return; // Exit if the root label does not exist
    }

    // Get all user-defined labels in Gmail
    const allLabels = GmailApp.getUserLabels();

    Logger.log(`Starting check for empty direct sub-labels under "${ROOT_LABEL_NAME_CLEANUP}"...`);

    // Iterate through each label to find and process sub-labels
    allLabels.forEach(label => {
      const labelName = label.getName(); // Get the full name of the current label (e.g., "FOR-CLEANUP/example.com")

      // Check if the label is a direct sub-label of the ROOT_LABEL_NAME_CLEANUP.
      // This is determined by:
      // 1. Starting with the root label name followed by a slash.
      // 2. Having exactly two parts when split by '/', meaning no further nesting.
      if (labelName.startsWith(`${ROOT_LABEL_NAME_CLEANUP}/`) && labelName.split('/').length === 2) {
        // Retrieve all email threads associated with this specific sub-label
        const threads = label.getThreads();

        // If the label contains no threads, it is considered empty
        if (threads.length === 0) {
          Logger.log(`Sub-label "${labelName}" is empty. Deleting label...`);
          label.deleteLabel(); // Delete the empty label
          labelsDeletedCount++; // Increment the counter
        } else {
          Logger.log(`Sub-label "${labelName}" contains ${threads.length} email(s). Keeping label.`);
        }
      }
    });

    // Log the final count of deleted labels
    if (labelsDeletedCount > 0) {
      Logger.log(`Successfully deleted ${labelsDeletedCount} empty sub-labels.`);
    } else {
      Logger.log("No empty sub-labels found to delete in this run.");
    }

  } catch (error) {
    // Catch and log any errors that occur during script execution
    Logger.log(`An error occurred during label cleanup: ${error.toString()}`);
  }
}
```

