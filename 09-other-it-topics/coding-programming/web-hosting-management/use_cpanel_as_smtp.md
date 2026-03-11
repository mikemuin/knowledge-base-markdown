# How to Use cPanel as the SMTP Server

Here is a clean, reusable **step-by-step guide** you can apply to *any* domain + Cloudflare + cPanel hosting when your objective is:

## Scenario

You have:

- A **domain** (e.g., pinoy.doctor)
- DNS managed on **Cloudflare**
- A **shared hosting account (cPanel)** at a provider (e.g., GreenGeeks)
- A **separate server hosting the website** (e.g., Elestio / Discourse)
- You want to use **your cPanel server as your SMTP mail server**
- Your SMTP username may be **different** from the “From” address

This guide ensures DNS, SPF, DKIM, DMARC, and SMTP authentication all work correctly.

---

## Objective

1. Host website on Server A
2. Host **email + SMTP** on Server B (cPanel)
3. Use Cloudflare DNS
4. Send emails **FROM any address under the domain**, even if SMTP user is different
5. Achieve full authentication (SPF, DKIM, DMARC)
6. Ensure deliverability (MailChannels or similar outbound filter)

---

## Step-by-Step Setup (Reusable Checklist)

### 1. Set DNS Records in Cloudflare

Even if your website is on a different server, your email must point to the cPanel server.

#### Required records

#### A Record

```
A mail → <cPanel server IP>  (DNS Only / grey cloud)
A @    → <website server IP> (proxy OK)
A www  → <website server IP>
```

#### MX Record

```
MX @ → mail.yourdomain.com  (priority 10)
```

#### SPF (TXT Record)

Use the provider’s recommended include. For most cPanel hosts (GreenGeeks, NameHero, A2, etc.):

```
v=spf1 a mx ip4:<cPanel server IP> include:<provider-include-domain> ~all
```

Example for GreenGeeks cluster:

```
v=spf1 a mx ip4:23.106.55.199 include:spf.websitehostserver.net ~all
```

#### DKIM (TXT Record)

Copy from:

**cPanel → Email Deliverability → DKIM**

Enter it in Cloudflare as:

```
Name: default._domainkey
Value: <full DKIM key as one line>
```

(Cloudflare supports multiline automatically.)

#### DMARC (TXT Record)

Recommended starter policy:

```
Name: _dmarc
Value: v=DMARC1; p=none; rua=mailto:postmaster@yourdomain.com;
```

#### MailChannels/Auth Record

If your hosting uses MailChannels (GreenGeeks does):

```
Name: _mailchannels
Value: v=mc1 auth=<provider>
```

---

### 2. Create Email Accounts in cPanel

Go to:

**cPanel → Email → Email Accounts → Create**

Examples:

- `mailer@yourdomain.com` → SMTP authentication
- `admin@yourdomain.com` → public-facing From address

---

### 3. Check Email Deliverability in cPanel

Go to:

**cPanel → Email Deliverability**

You should see **Valid** for:

- SPF
- DKIM
- MX

If not, copy the suggested fixes into Cloudflare.

---

### 4. Test the Mailbox

Login via Webmail:

```
https://yourdomain.com:2096
```

Confirm the mailbox works.

---

### 5. Configure SMTP in Your Application

Use:

```
SMTP Host: mail.yourdomain.com
SMTP Port: 465 (SSL) or 587 (TLS)
Username: mailer@yourdomain.com  (or the account you created)
Password: <mailbox password>
Auth: normal password
```

---

### 6. Use a Different FROM Address Than the SMTP User

This is allowed as long as:

- Both emails exist in cPanel
- Domain is authenticated (SPF/DKIM)
- Both emails are under the same domain
- App allows setting custom “From” header

### Example:

```
SMTP user = mailer@yourdomain.com
FROM      = admin@yourdomain.com
```

This is valid and widely used.

---

### 7. Test Deliverability via Mail-Tester

Go to:

https://www.mail-tester.com/

Steps:

1. Get the temporary email shown
2. Send an email from your domain to it
3. Click “Check your score”
4. Review DKIM, SPF, DMARC, SpamAssassin, MailChannels reputation

Aim for **8–10/10**.

---

### 8. Fix Any Issues Mail-Tester Reports

Common fixes:

- SPF simplified
- Add missing DKIM
- Remove duplicate SPF records
- Ensure mail-related DNS is NOT proxied in Cloudflare

---

## End Result

After following this process, you get:

Website hosted anywhere
Cloudflare DNS fully working
cPanel handles email + SMTP
SPF, DKIM, DMARC all valid
MailChannels authorizes your domain
SMTP user and From address can differ
Inbox placement is excellent
Works with WordPress, Discourse, SaaS apps, custom code
