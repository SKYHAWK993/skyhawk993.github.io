# tj.wiki — a Wikipedia-style personal blog

A minimal, fast, Wikipedia-inspired blog built with [Astro](https://astro.build).
Posts are plain Markdown files with LaTeX (KaTeX) and image support.
No database, no CMS, no admin panel — writing a post is creating one file.

## Run locally

```bash
npm install
npm run dev        # http://localhost:4321
```

## Write a post

1. Create `src/content/blog/your-post.md`:

   ```markdown
   ---
   title: "Your post title"
   date: 2026-06-15
   description: "One-line summary shown on the home page."   # optional
   tags: ["pricing", "ml"]                                    # optional
   ---

   Your content. Inline math $e^{i\pi}+1=0$ and display math:

   $$
   \hat\beta = (X^\top X)^{-1} X^\top y
   $$
   ```

2. The filename becomes the URL: `your-post.md` → `/blog/your-post/`.

### Images

Put files in `public/images/` and reference them:

```markdown
![Alt text](/images/my-figure.png)
```

### Coming from LaTeX or Jupyter?

- A `.tex` document can be converted to Markdown with pandoc:
  `pandoc paper.tex -o post.md` — math survives the conversion.
- A notebook converts with:
  `jupyter nbconvert --to markdown analysis.ipynb`
  (generated images land in a folder; move them to `public/images/`).

## Edit your profile

Open `src/pages/about.astro` — the infobox and bio are plain HTML.
Update the contact links (email / GitHub / LinkedIn placeholders).

## Deploy (Vercel, recommended)

1. Push this folder to a new GitHub repository:

   ```bash
   git init && git add . && git commit -m "initial site"
   git remote add origin https://github.com/<you>/<repo>.git
   git push -u origin main
   ```

2. Go to [vercel.com](https://vercel.com) → **Add New Project** → import the repo.
   Vercel auto-detects Astro; click **Deploy**. Done.

3. From now on, every `git push` redeploys the site automatically.

4. Optional: set your custom domain in Vercel → Project → Settings → Domains,
   then update `site` in `astro.config.mjs`.

### Alternative: GitHub Pages

Add the official Astro deploy workflow
(`withastro/action`) under `.github/workflows/deploy.yml` and enable Pages
in the repo settings — see https://docs.astro.build/en/guides/deploy/github/

## Structure

```
src/content/blog/   ← your posts (.md)        ← the only folder you touch day-to-day
public/images/      ← your images
src/pages/about.astro   ← profile page
src/styles/global.css   ← all styling
```
