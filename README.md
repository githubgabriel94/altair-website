# Altair Solutions Inc. — website

The public website for **Altair Solutions Inc.**, a small independent studio making apps for Apple platforms,
at **https://altairsolutionsinc.com**. Plain HTML and CSS, no build step.

```
index.html            Home: the studio, the Apps section, company facts
support/index.html    Support & contact  → App Store "Support URL"
privacy/index.html    Privacy Policy     → App Store "Privacy Policy URL"
404.html              "Page not found"
styles.css            All styles (colors are tokens at the top; dark mode included)
favicon.svg           Site icon
CNAME                 Custom domain for GitHub Pages (ignored by Cloudflare Pages)
_templates/app.html   Template for one page per app
_config.yml           GitHub Pages only: keeps README.md and _templates off the site
```

Preview: open `index.html` in a browser, or serve the folder with

```bash
python3 -m http.server 8000
```

and visit http://localhost:8000.

Domain: registered at **Cloudflare Registrar**; DNS is on Cloudflare.
Emails: `support@altairsolutionsinc.com` (shown on the site) and `developer@altairsolutionsinc.com`
(the company's Apple Account), both forwarded by Cloudflare Email Routing.

## Still to do on the site

Spots to edit are marked with `EDIT` comments:

```bash
grep -rn "EDIT\|\[DATE\]" index.html support privacy
```

- **App names and descriptions** on the home page, when you're ready to announce them.
- **Privacy Policy**: it's a **draft**, not legal advice. Review it (ideally with a qualified adviser), make it
  match what each app really does, then remove the "Draft" notice and set the effective date.

## 0. Verify the domain registration (within 15 days)

Cloudflare emails the registrant contact a verification link after you register. **Click it within 15 days**,
or the registry suspends the domain. Check spam if you can't find it. In the Cloudflare dashboard,
**Domain Registration → Manage Domains** shows whether the contact is verified, and can resend the email.

## 1. Put the code on GitHub

1. On github.com, create a **new repository** (e.g. `altair-website`). Don't add a README, .gitignore or license.
   - With **Cloudflare Pages** (recommended below) it can be **private** on any plan.
   - With **GitHub Pages**, a free GitHub account needs it **public**; private needs GitHub Pro or Team.
2. Push this folder (replace `YOUR-GITHUB-USER`):

   ```bash
   git remote add origin https://github.com/YOUR-GITHUB-USER/altair-website.git
   ```

   ```bash
   git push -u origin main
   ```

## 2. Publish it — choose ONE

### Option A (recommended): Cloudflare Pages

Your domain and DNS are already on Cloudflare, so this keeps everything in one dashboard. It sets up
DNS and HTTPS for you, and works with a private repository for free.

1. Cloudflare dashboard → **Workers & Pages** → **Create** → **Pages** tab →
   **Import an existing Git repository** (if the dashboard suggests a Worker instead, pick the Pages option).
2. **Connect GitHub**, and allow Cloudflare access to only the `altair-website` repository.
3. Settings:
   - Project name: `altair-website`
   - Production branch: `main`
   - Framework preset: **None**
   - Build command: *(leave empty)*
   - Build output directory: `/`
4. **Save and Deploy.** A minute later the site is live at `https://altair-website.pages.dev` (or similar).
5. In the project, open **Custom domains** → **Set up a custom domain** → `altairsolutionsinc.com` → **Activate domain**.
   Cloudflare adds the DNS record itself. Repeat for `www.altairsolutionsinc.com`.
6. Wait for both to show **Active** (usually a few minutes; HTTPS certificate included).
7. Optional: send `www` to the main domain with a **Redirect Rule** (domain → **Rules** → **Redirect Rules** →
   template "Redirect from WWW to root").

From then on, every `git push` to `main` republishes the site automatically.

Note: Cloudflare Pages publishes every file in the repository, so `README.md` will also be reachable on the site.
It contains nothing private; keep it that way.

### Option B: GitHub Pages

1. Make the repository **public** (or have GitHub Pro/Team).
2. Repository → **Settings → Pages** → Source: **Deploy from a branch**, Branch: **`main`**, folder **`/ (root)`** → **Save**.
3. Verify the domain with GitHub: your profile picture → **Settings → Pages → Add a domain** → enter
   `altairsolutionsinc.com` → add the `TXT` record GitHub shows in Cloudflare (**DNS → Records**) → **Verify**.
4. In Cloudflare **DNS → Records**, add these, each with **Proxy status: DNS only (grey cloud)**:

   | Type  | Name | Content                    |
   |-------|------|----------------------------|
   | A     | @    | 185.199.108.153            |
   | A     | @    | 185.199.109.153            |
   | A     | @    | 185.199.110.153            |
   | A     | @    | 185.199.111.153            |
   | AAAA  | @    | 2606:50c0:8000::153        |
   | AAAA  | @    | 2606:50c0:8001::153        |
   | AAAA  | @    | 2606:50c0:8002::153        |
   | AAAA  | @    | 2606:50c0:8003::153        |
   | CNAME | www  | YOUR-GITHUB-USER.github.io |

   (These are GitHub's published Pages addresses; double-check them in GitHub's docs, "Managing a custom
   domain for your GitHub Pages site".)
5. The `CNAME` file in this repository already names the domain. In **Settings → Pages**, wait for the DNS check
   to pass, then tick **Enforce HTTPS** (it can take up to an hour to become available).

## 3. Email: forward support@ and developer@ to your inbox (Cloudflare Email Routing)

1. Cloudflare dashboard → select **altairsolutionsinc.com** → **Email** → **Email Routing** → **Get started / Enable**.
2. When asked, let Cloudflare **add the DNS records** (MX and TXT). Don't delete them later.
3. **Destination addresses** → **Add destination address** → enter the company inbox (e.g. a Gmail address).
   Cloudflare sends a verification email there: **click the link**.
4. **Routing rules** → **Create address**:
   - Custom address `support` → Action **Send to an email** → the company inbox → **Save**.
   - Custom address `developer` → same destination → **Save**.
5. Test: from a *different* email account, send a message to each address and check it arrives
   (look in Spam the first time and mark it "Not spam").

Limits to know:
- Email Routing **only receives and forwards**. Replies go out from the Gmail address, not from `support@`.
  To send as `support@` later, add a real mailbox (iCloud+ Custom Email Domain, Google Workspace, etc.).
- `developer@` is fine for the company's Apple Account: Apple's verification codes and enrollment emails will be
  forwarded to the inbox. Keep the forwarding working for as long as the account exists.

## Adding an app page

1. Copy the template:

   ```bash
   mkdir -p apps/coffee-app && cp _templates/app.html apps/coffee-app/index.html
   ```

2. Edit `apps/coffee-app/index.html`: replace `APP NAME`, `APP-SLUG` and the bracketed text.
3. On the home page, link the app's card to its page and change its pill to "On the App Store".
4. Add the app's section to the Privacy Policy.
5. In App Store Connect use:
   - Support URL: `https://altairsolutionsinc.com/apps/coffee-app/` (or `https://altairsolutionsinc.com/support/`)
   - Privacy Policy URL: `https://altairsolutionsinc.com/privacy/`

## Updating the site

```bash
git add -A && git commit -m "Describe the change" && git push
```

The site republishes within a minute or two.
