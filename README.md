# Publishing adimedtrans.bg on Cloudflare Pages

## Read this first — one thing will break if you skip it

The page currently loads its photos from `adimedtrans.bg/wp-content/uploads/...` — i.e. from the **old WordPress site**. The moment you point `adimedtrans.bg` at the new site, those URLs stop existing and **every photo goes blank**.

So the order matters:

1. Photos into `/images/` first
2. Deploy and test on the free `.pages.dev` URL
3. Only then move the domain

---

## Step 1 — Photos

Download the images you want from the current site (right-click → Save image), or use better originals if you have them. Save them into the `images/` folder using the names listed in `images/README.txt`:

| File | Where it appears | Ideal size |
|---|---|---|
| `hero-ambulance.jpg` | Main hero photo | 1600×1200+ landscape |
| `about.jpg` | "За нас" section | square-ish |
| `gallery-1.jpg` … `gallery-7.jpg` | Gallery grid | 1200×900 landscape |
| `favicon.png` | Browser tab icon | 512×512 |

Send them to me and I'll compress them and rewire the HTML — or drop them in yourself and change the `src="https://adimedtrans.bg/wp-content/..."` paths in `index.html` to `src="/images/hero-ambulance.jpg"` etc.

Keep each photo under ~400 KB. Large phone photos (5 MB each) will make the site feel slow.

---

## Step 2 — Back up the old site

Before anything else, in your WordPress admin: install a backup plugin (UpdraftPlus, All-in-One WP Migration) and download a full backup. Keep the hosting active for at least a month after switching. This is your undo button.

---

## Step 3 — Deploy to Cloudflare Pages

1. Create a free account at **dash.cloudflare.com** (or log in).
2. Left sidebar → **Workers & Pages** → **Create** → **Pages** tab → **Upload assets**.
3. Project name: `adimedtrans` → **Create project**.
4. Drag the **whole `adimedtrans-site` folder** into the upload box (not just index.html — the `images/`, `_headers` and `_redirects` files matter).
5. **Deploy site**.

You now have a live URL: `https://adimedtrans.pages.dev`

Open it on your phone and on a desktop. Check the photos, the call buttons, the menu. Fix anything now, while the real domain is untouched — re-uploading is the same drag-and-drop.

---

## Step 4 — Add adimedtrans.bg to Cloudflare

This is the part that moves the domain. Cloudflare needs to run your DNS before it can serve the domain from Pages.

1. Dashboard → **Add a domain** → type `adimedtrans.bg` → choose the **Free** plan.
2. Cloudflare scans your existing DNS records and imports them. **Check the list carefully** — especially any `MX` records. Your contact address is Gmail, so email should be unaffected, but if anyone uses an `@adimedtrans.bg` mailbox, those MX records must survive the move.
3. Cloudflare shows you **two nameservers**, e.g. `xxx.ns.cloudflare.com` and `yyy.ns.cloudflare.com`. Copy them.

### Changing nameservers on a .bg domain

`.bg` domains are managed through Register.BG or a Bulgarian reseller (SuperHosting, ICN.bg, Host.bg, Jump.bg — whoever you pay for the domain).

1. Log into that registrar's control panel.
2. Find the domain → **DNS / Nameservers / Сървъри за имена**.
3. **Turn DNSSEC off first** if it's enabled. Changing nameservers with DNSSEC active takes the domain offline entirely.
4. Replace the existing nameservers with the two Cloudflare ones. Remove any others.
5. Save. Some Bulgarian registrars process `.bg` nameserver changes manually and may email you a confirmation form — watch your inbox.

Propagation takes anywhere from 15 minutes to 24 hours. Cloudflare emails you when the domain is active.

---

## Step 5 — Attach the domain to the Pages project

Once Cloudflare says the domain is **Active**:

1. **Workers & Pages** → your `adimedtrans` project → **Custom domains** → **Set up a domain**.
2. Enter `adimedtrans.bg` → Cloudflare creates the DNS record for you automatically (CNAME flattening handles the apex domain).
3. Repeat for `www.adimedtrans.bg`.

Do **not** hand-create the CNAME yourself before doing this — that's the classic cause of a 522 error.

HTTPS certificates are issued automatically, usually within a few minutes.

---

## Step 6 — Verify

- `https://adimedtrans.bg` loads the new green site
- `https://www.adimedtrans.bg` also works
- `https://adimedtrans.bg/za-nas/` redirects to the About section (that's the `_redirects` file preserving your old Google links)
- Photos load, phone links dial on mobile, email link opens a mail client
- Test on a phone with mobile data, not just wifi

Then in **Google Search Console**, submit `https://adimedtrans.bg/sitemap.xml` so Google re-crawls.

---

## If something goes wrong

Change the nameservers back at your registrar. Within a few hours the old WordPress site is live again. Nothing you do in Cloudflare Pages is destructive to the WordPress install itself — it stays on its hosting until you cancel it.

---

## Cost

Cloudflare Pages free tier: 500 builds/month, unlimited bandwidth, free SSL. For a site like this, permanently free. You keep paying only for the domain registration; the WordPress hosting can be cancelled once you're confident.

## Updating the site later

Workers & Pages → project → **Create deployment** → drag the updated folder. Live in under a minute. Keep this folder somewhere safe — it *is* your website.

---

Sources: [Cloudflare Pages custom domains](https://developers.cloudflare.com/pages/configuration/custom-domains/), [Onboard a domain to Cloudflare](https://developers.cloudflare.com/fundamentals/manage-domains/add-site/), [CNAME flattening](https://developers.cloudflare.com/dns/cname-flattening/)
