# ethicos.co.uk — site for Ethicos consultancy

Single-file static site, deploys to GitHub Pages with `ethicos.co.uk` as the custom domain.

## Files
- `index.html` — the whole site.
- `CNAME` — set to `www.ethicos.co.uk`. (See note below on www vs apex for this domain.)
- `README.md` — this file.

---

## ⚠️ READ THIS FIRST — your email lives on the same DNS as the website

`cos@ethicos.co.uk` is set up via MX records on the same domain. **If you wipe or replace those MX records when adding the website's A records, your email will stop working.** You will not get a warning — incoming mail will just bounce, silently.

The rule is simple: **only ADD records for the website. Do NOT delete or replace any existing MX, TXT, or SPF/DKIM records.**

Before you change anything: take a screenshot of the current DNS panel so you can put it back if anything goes wrong.

---

## Where is your DNS actually managed?

You said you bought ethicos.co.uk via Google Domains. Two scenarios:

### Scenario A: It's now Squarespace Domains
Google Domains was sold to Squarespace in 2023. Most Google Domains customers have been migrated. Check by signing in at **domains.squarespace.com** with the Google account you used for Google Domains. If your domain is there, this is your DNS panel.

### Scenario B: You moved it elsewhere (Namecheap, GoDaddy, etc.)
If you transferred to another registrar, that's where DNS lives.

### How to check definitively
Open a terminal and run:
```
dig NS ethicos.co.uk +short
```
- If it shows `ns-cloud-*.googledomains.com` → still on Google's nameservers (now Squarespace)
- If it shows Squarespace nameservers → Squarespace
- Anything else → wherever the registrar is

---

## Deployment — step by step

### 1. Create a new GitHub repo
Same as `cbadea-website`. Public, named e.g. `ethicos-website`. Don't add a README during creation.

### 2. Upload the three files
Drag `index.html`, `CNAME`, `README.md` from the `ethicos-website/` folder into the repo's "uploading an existing file" page. Commit.

### 3. Turn on GitHub Pages
Repo → Settings → Pages → Source: `main` branch, `/` (root). Save.

The site will be live at `https://<your-username>.github.io/ethicos-website/` within a minute. The Custom domain field should auto-populate with `www.ethicos.co.uk` from the CNAME file.

### 4. Update DNS — carefully

Sign in to your DNS provider (Squarespace Domains for the Google Domains successor, or wherever you transferred it).

**Step A — preserve email.** Look at the existing DNS records. You should see:
- A few MX records (mail.google.com, alt1.aspmx.l.google.com, etc., or your email provider's MX servers)
- A TXT record starting with `v=spf1`
- Possibly DKIM/DMARC TXT records

**Take a screenshot of all of these. Do not touch them.**

**Step B — add the website records.** You need to add five new records:

| Type   | Host / Name | Value                          | TTL       |
|--------|-------------|--------------------------------|-----------|
| A      | @           | 185.199.108.153                | Auto      |
| A      | @           | 185.199.109.153                | Auto      |
| A      | @           | 185.199.110.153                | Auto      |
| A      | @           | 185.199.111.153                | Auto      |
| CNAME  | www         | <your-github-username>.github.io. | Auto |

Notes:
- `@` means the bare domain (`ethicos.co.uk`).
- The CNAME `www` value is your GitHub username + `.github.io`. If your GitHub username is `cb2610`, this would be `cb2610.github.io`.
- Do NOT delete the existing MX or TXT records. Only ADD these five new rows.

If your DNS provider doesn't allow A records at the apex `@` (some don't), use ALIAS or ANAME records instead with the same four GitHub Pages IPs, OR set up only the `www.ethicos.co.uk` CNAME and configure ethicos.co.uk → www.ethicos.co.uk redirect at the DNS provider level (Squarespace supports this).

### 5. Configure GitHub Pages custom domain
Back in GitHub repo → Settings → Pages:
- Custom domain field: `www.ethicos.co.uk` (CNAME already says this)
- Save.
- Wait for the DNS check (yellow → green).
- Once green, tick **Enforce HTTPS**.

### 6. Verify email still works
Send yourself a test email to `cos@ethicos.co.uk` from a different account. Check it arrives. If it doesn't, restore the MX records from your screenshot immediately.

### 7. Verify the site loads
Visit `https://www.ethicos.co.uk` and `https://ethicos.co.uk`. Both should resolve to the site.

DNS propagation: 10 minutes to 24 hours. GitHub HTTPS enforcement provisioning: another few minutes after the DNS goes green.

---

## Troubleshooting

**Site loads but email broken.** Check that all original MX and TXT records are still in place. Restore from your pre-change screenshot.

**Site doesn't load, error "improperly configured".** Same as cbadea.com — DNS hasn't propagated yet, or one of the five A/CNAME records didn't save. Run `dig www.ethicos.co.uk +short` and `dig ethicos.co.uk +short` to check.

**Both work but no HTTPS.** Wait. GitHub provisions the cert in the background; can take an hour after DNS goes green.

---

## Updating later
Edit `index.html` directly in GitHub's web editor (pencil icon). Commit. Live in a minute.

## Future additions worth doing
- A photo of you for the About section
- One or two case studies (anonymised) once Ethicos has shipped engagements you can talk about
- Add `<a href="cv.pdf">Download CV</a>` if useful for prospects (drop the PDF in the repo)
