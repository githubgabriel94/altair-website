# Altair Solutions Inc. — website

The public website for **Altair Solutions Inc.**, a small independent studio making apps for Apple platforms.
Plain HTML and CSS, no build step, hosted on GitHub Pages.

```
index.html            Home: the studio, the Apps section, company facts
support/index.html    Support & contact  → use as the App Store "Support URL"
privacy/index.html    Privacy Policy     → use as the App Store "Privacy Policy URL"
404.html              "Page not found"
styles.css            All styles (colors are tokens at the top; dark mode included)
favicon.svg           Site icon
_templates/app.html   Template for one page per app (not published)
_config.yml           Keeps README.md and _templates out of the published site
```

To preview it, open `index.html` in a browser. Or serve the folder (closer to how GitHub Pages behaves):

```bash
python3 -m http.server 8000
```

then visit http://localhost:8000.

## Before going live: fill in the placeholders

Every spot to edit is marked with an `EDIT` comment. List them with:

```bash
grep -rn "EDIT\|YOURDOMAIN\|\[DATE\]" --include=*.html .
```

1. **Domain**: replace `YOURDOMAIN` everywhere (canonical links and the email address).
2. **Support email**: replace `support@YOURDOMAIN` on the home, Support and Privacy pages with the address you set up (step D below).
3. **App names and descriptions** on the home page, when you're ready to announce them.
4. **Privacy Policy**: it's a **draft**, not legal advice. Review it (ideally with a qualified adviser), make it match what each app really does, then remove the "Draft" notice and set the effective date. Add one section per app as it launches; a template is in the HTML comments.

## A. Create the GitHub repository and turn on Pages

1. Sign in at github.com and create a **new repository** (for example `altair-website`). It can be public or private; GitHub Pages on a private repository needs a paid plan (GitHub Pro/Team).
   Don't add a README or license: this folder already has its files.
2. Connect this folder and push (replace `YOUR-USER/altair-website` with your repository):

   ```bash
   git remote add origin https://github.com/YOUR-USER/altair-website.git
   ```

   ```bash
   git push -u origin main
   ```

3. In the repository, open **Settings → Pages**.
   Under **Build and deployment**, choose **Source: Deploy from a branch**, **Branch: `main`**, folder **`/ (root)`**, and click **Save**.
4. After a minute the site is live at `https://YOUR-USER.github.io/altair-website/`.
   (At that address links between pages work, but the 404 page's styling only works once the custom domain is set.)

## B. Buy the domain and point it at GitHub Pages

1. Pick a domain and buy it at a registrar (Cloudflare Registrar, Porkbun, Namecheap, Squarespace Domains, etc.).
   Candidates that looked unregistered in a quick DNS check (confirm availability at the registrar):
   `altairsolutionsinc.com`, `altairsolutions.app`, `altairsolutions.dev`, `altairsolutions.co`.
   Note: `.app` and `.dev` require HTTPS for every site (GitHub Pages provides it, so that's fine).
2. **Verify the domain with GitHub first** (protects it from being claimed by someone else's Pages site):
   GitHub → your profile picture → **Settings → Pages → Add a domain**, enter the domain, and add the `TXT` record GitHub shows at your registrar's DNS settings. Click **Verify**.
3. At your registrar's DNS settings, add these records for the root domain (often written as `@`):

   | Type | Name  | Value             |
   |------|-------|-------------------|
   | A    | @     | 185.199.108.153   |
   | A    | @     | 185.199.109.153   |
   | A    | @     | 185.199.110.153   |
   | A    | @     | 185.199.111.153   |
   | AAAA | @     | 2606:50c0:8000::153 |
   | AAAA | @     | 2606:50c0:8001::153 |
   | AAAA | @     | 2606:50c0:8002::153 |
   | AAAA | @     | 2606:50c0:8003::153 |
   | CNAME| www   | YOUR-USER.github.io |

   Remove any other `A`/`AAAA` records for `@` that the registrar added by default (parking pages).
   If you use Cloudflare DNS, set these records to **DNS only** (grey cloud) until HTTPS is working.
   The addresses above are GitHub's published Pages IPs; double-check them in GitHub's docs
   ("Managing a custom domain for your GitHub Pages site") when you set this up.
4. Check that DNS has updated (can take from minutes to a few hours):

   ```bash
   dig +short YOURDOMAIN
   ```

   It should list the four `185.199.x.153` addresses.

## C. Add the CNAME file and turn on HTTPS

1. In this folder, create a file named `CNAME` (no extension) that contains just the domain on one line, e.g.:

   ```bash
   echo "altairsolutions.app" > CNAME
   ```

2. Replace `YOURDOMAIN` in the HTML files (see "Before going live"), then commit and push:

   ```bash
   git add -A && git commit -m "Set custom domain" && git push
   ```

3. In the repository's **Settings → Pages**, the **Custom domain** field should now show your domain (enter it and **Save** if it doesn't). Wait for the DNS check to pass.
4. Tick **Enforce HTTPS**. If it's greyed out, GitHub is still issuing the certificate; wait up to an hour and refresh.
5. Visit `https://YOURDOMAIN` and `https://www.YOURDOMAIN` (www redirects to the main domain).

## D. Set up an email address on the domain

Apple recommends enrolling with an email on your company domain, and the site shows a support address. Options:

- **Google Workspace** or **Microsoft 365** (paid, full mailboxes), or **iCloud+ Custom Email Domain** (included with iCloud+; Settings → [your name] → iCloud → Custom Email Domain on iPhone, or icloud.com).
- **Free forwarding only** (e.g. Cloudflare Email Routing, or forwarding from your registrar): mail to `support@YOURDOMAIN` arrives in an inbox you already have. Fine for receiving; to reply *from* the domain address you'll usually want a real mailbox.

Steps are similar everywhere:

1. Add the domain in the email provider and follow its setup wizard.
2. Add the DNS records it gives you at your registrar: `MX` records (where mail is delivered), a `TXT` SPF record, and DKIM records (`TXT` or `CNAME`). Also add a DMARC record, for example
   `TXT  _dmarc  v=DMARC1; p=none; rua=mailto:you@YOURDOMAIN`.
   These don't conflict with the GitHub Pages `A`/`CNAME` records above.
3. Send a test message to the new address and reply from it.
4. Update `support@YOURDOMAIN` on the site and push.

Suggested addresses: `support@` (shown on the site), and an address for yourself (e.g. `firstname@`) to use for the Apple Developer account and the D-U-N-S profile.

## Adding an app page

1. Copy the template into a new folder:

   ```bash
   mkdir -p apps/coffee-app && cp _templates/app.html apps/coffee-app/index.html
   ```

2. Edit `apps/coffee-app/index.html`: replace `APP NAME`, `APP-SLUG` and the bracketed text.
3. On the home page, wrap the app's card title in a link (`<a href="apps/coffee-app/index.html">…</a>`) and change its pill to "On the App Store".
4. Add the app's section to the Privacy Policy.
5. In App Store Connect, use:
   - Support URL: `https://YOURDOMAIN/apps/coffee-app/` (or `https://YOURDOMAIN/support/`)
   - Privacy Policy URL: `https://YOURDOMAIN/privacy/`

## Updating the site

Edit the files, preview locally, then:

```bash
git add -A && git commit -m "Describe the change" && git push
```

GitHub Pages republishes within a minute or two.
