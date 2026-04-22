# Claude Code Setup Instructions

You are helping set up a brand-new static website project called **Aethelia** — an interactive 3D space simulator at `aethelia.space`. The user has already extracted a zip containing all the necessary files into the current working directory. Your job is to initialize the project, verify files, and push it to a GitHub repository.

## 0. Verify the files

The current directory should contain:

```
index.html
manifest.json
robots.txt
sitemap.xml
README.md
LICENSE
.gitignore
assets/
  favicon.ico
  favicon-16.png
  favicon-32.png
  icon-192.png
  icon-512.png
  apple-touch-icon.png
  logo-compact.png
  logo-wide.png
  logo-square.png
  og-image.png
```

Run `ls -la` and `ls -la assets/` to verify. If anything is missing, stop and tell the user.

## 1. Quick local test

Serve the site locally and confirm it loads without errors:

```bash
python3 -m http.server 8000
```

Tell the user to open `http://localhost:8000` in their browser. They should see the AETHELIA logo in the top-left, our Solar System rendered in 3D, and a gallery at the bottom with star systems and cosmic anomalies.

If the user reports an error, check the browser console. The most likely issue would be a CDN import failure — re-check the `importmap` in `index.html`.

Stop the server with `Ctrl+C` when they confirm it works.

## 2. Initialize Git

```bash
git init
git branch -M main
git add .
git status
```

Show the user the staged file list and confirm before committing.

```bash
git commit -m "Initial commit — Aethelia launch"
```

## 3. Create GitHub repo and push

Ask the user which approach they prefer:

**Option A — GitHub CLI (if installed):**
```bash
gh repo create aethelia --public --source=. --remote=origin --push
```

**Option B — Manual:** ask the user to create an empty repo at github.com named `aethelia` (no README, no license, no .gitignore — we already have them), then:
```bash
git remote add origin git@github.com:<USERNAME>/aethelia.git
git push -u origin main
```

Ask the user for their GitHub username before running this. Replace `<USERNAME>` in the command.

## 4. Deploy to a host

After the repo is on GitHub, ask the user which host they want:

1. **Cloudflare Pages** (recommended — fastest CDN, free, includes analytics)
2. **Netlify** (great DX, easy forms/functions later)
3. **Vercel** (can do via `vercel --prod` right from terminal)
4. **GitHub Pages** (simplest, but slower)

Based on their choice, walk them through the steps from the README (`Deployment` section). For Vercel, you can run `npx vercel --prod` directly if they want and they will be prompted to log in.

## 5. Connect the domain

Once the site is live on a temporary URL, tell the user:

> "Now go to your domain registrar (where you bought `aethelia.space`) and add the DNS records your host just showed you. Cloudflare Pages typically asks for two CNAME records; Netlify gives you an A record + CNAME; Vercel asks for a single A record. It can take anywhere from a few minutes to 24 hours to propagate, but usually under an hour."

## 6. Verify final deployment

After the user reports DNS has propagated:

```bash
curl -I https://aethelia.space
```

Should return `200 OK`. Also run `curl -s https://aethelia.space | head -20` to check the HTML is served correctly.

Tell them to:
- Visit `https://aethelia.space` in an incognito window
- Test a Twitter share preview at https://cards-dev.twitter.com/validator
- Test Facebook's sharer at https://developers.facebook.com/tools/debug/
- Check that the favicon shows up in the browser tab

## Notes

- `index.html` is the entire app — one single file with all CSS, JS (module), and shader code inline. Do not split it.
- All assets live in `/assets` and are referenced with absolute paths (e.g. `/assets/favicon.ico`). If you deploy to a subpath like `<user>.github.io/aethelia/`, those paths will break — prefer a custom domain or a host that serves from root.
- The site uses ES modules via an importmap (Three.js from cdnjs). It works in all modern browsers (Chrome 89+, Firefox 108+, Safari 16.4+).
- No build step. No `npm install`. No `package.json`. Pure static site.

When everything is done, congratulate the user and show them the live URL.
