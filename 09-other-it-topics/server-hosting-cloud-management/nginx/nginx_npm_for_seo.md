# Nginx Settings to handle SEO

You can execute the 301 redirect in Nginx Proxy Manager by creating a **custom location** rule. This rule will use a regular expression to match the old URL format and permanently redirect it to the new, shortened URL.

------

### Step-by-Step Guide for Nginx Proxy Manager

1. **Log in to your Nginx Proxy Manager dashboard.**

2. Navigate to the **"Proxy Hosts"** section and either **edit the existing proxy host** for your `healthspace.ph` domain or create a new one if it doesn't exist.

3. Go to the **"Custom Locations"** tab. This is where you can add specific redirect rules for different URL patterns.

4. **Click "Add Location"** to create a new rule.

5. In the new rule, you will set the following:

   - **Location:** This field should contain the regular expression that matches your old URL format. Use a regex to capture the variable part of the URL (the facility name and code). The correct regex for your case is:

Code snippet

     ```
     ^/facility/(.*)/details$
     ```

The `(.*)` part is a capture group that grabs everything between `/facility/` and `/details`. This is what we'll use to rebuild the new URL.

   - **Scheme:** Set this to `http` or `https`, depending on your setup. This is a common requirement in Nginx Proxy Manager's configuration.

   - **Forward Hostname / IP:** This field can typically be left blank or set to a placeholder, as the redirect will handle the destination.

   - **Forward Port:** Can also be left blank.

   - **Advanced:** In the "Advanced" section, you will add the Nginx redirect directive. This is the most crucial part. You'll need to write the `rewrite` rule that performs the 301 redirect using the captured group from your regex. The rule should be:

Nginx

     ```
     rewrite ^/facility/(.*)/details$ /facility/$1 permanent;
     ```

     - `rewrite`: The Nginx directive for URL rewriting.
     - `^/facility/(.*)/details$`: Your regex pattern.
     - `/facility/$1`: The new destination URL. `$1` refers to the content captured by the `(.*)` group (e.g., `a-dela-cruz-maternity-hospital-FCD00708`).
     - `permanent`: This specifies that it is a **301 Moved Permanently** redirect, which is essential for SEO.

6. **Click "Save"** to apply the changes. Nginx Proxy Manager will now automatically handle all requests to your old URLs by permanently redirecting them to the new, cleaner format.

------

### What's Happening Behind the Scenes

When a user or a search engine bot requests `https://healthspace.ph/facility/a-dela-cruz-maternity-hospital-FCD00708/details`, Nginx Proxy Manager intercepts the request. It checks its rules and finds the `rewrite` directive you created. The directive matches the URL, captures the facility identifier, and sends a **301 response header** to the client. This header tells the browser/bot to immediately navigate to `https://healthspace.ph/facility/a-dela-cruz-maternity-hospital-FCD00708` instead. This process is seamless and preserves the SEO value of the old URLs.

---

I will provide you with a step-by-step guide to set up the 301 redirect in Nginx Proxy Manager.

**Before you begin:**

- Ensure you have access to your Nginx Proxy Manager dashboard.
- Log in to your Nginx Proxy Manager interface.

**Step 1: Navigate to Proxy Hosts**

- In the Nginx Proxy Manager sidebar, click on **"Proxy Hosts"**. This section lists all the domains you are managing.

**Step 2: Edit Your Domain's Proxy Host**

- Find the entry for your domain, `healthspace.ph`, in the list.
- Click the **pencil icon (edit)** next to it to open the configuration for that proxy host.

**Step 3: Go to the Custom Locations Tab**

- Within the proxy host settings, you will see several tabs at the top. Click on the **"Custom Locations"** tab.

**Step 4: Add a New Location Rule**

- Click the **"Add Location"** button. This will open a new section to define a specific rule.
- You will see a few fields to fill out.

**Step 5: Configure the Redirect Rule**

- **Location:** In this field, enter the regular expression that matches the old URL structure. This is what Nginx will look for.
  - Enter: `^/facility/(.*)/details$`
  - *Explanation:*
    - `^`: The start of the URL path.
    - `/facility/`: Matches the literal `/facility/`.
    - `(.*)`: This is the capture group. It matches and saves **any character (`.`)** occurring **zero or more times (`\*`)**. This will capture `a-dela-cruz-maternity-hospital-FCD00708` or any other facility name.
    - `/details$`: Matches the literal `/details` at the **end of the URL path (`$`)**.
- **Scheme, Forward Hostname, and Forward Port:** For a redirect, you can typically leave these blank. The advanced rule you're about to add will handle the entire redirect.
- **Advanced:** In the "Advanced" text area, you will enter the Nginx `rewrite` directive. This tells Nginx exactly what to do.
  - Enter: `rewrite ^/facility/(.*)/details$ /facility/$1 permanent;`
  - *Explanation:*
    - `rewrite`: The command to tell Nginx to rewrite the URL.
    - `^/facility/(.*)/details$`: This is the same regular expression from the "Location" field, telling Nginx which URLs to target.
    - `/facility/$1`: This is the new destination. The `$1` is a backreference that uses the text captured by the `(.*)` group in the regex.
    - `permanent`: This is the crucial part that specifies a **301 Moved Permanently** status code, which is what search engines need to preserve your SEO value.

**Step 6: Save and Test**

- Click the **"Save"** button at the bottom of the page to apply the changes to your proxy host.
- Nginx Proxy Manager will reload the Nginx configuration, and your new redirect will be active.
- **Test it immediately:** Open a new browser tab and try to visit one of your old URLs, such as `https://healthspace.ph/facility/a-dela-cruz-maternity-hospital-FCD00708/details`. You should be automatically redirected to `https://healthspace.ph/facility/a-dela-cruz-maternity-hospital-FCD00708` without seeing a 404 error page.

By following these steps, you will have successfully implemented a server-side 301 redirect for all your facility detail pages using Nginx Proxy Manager.