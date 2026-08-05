# kogantisharma.github.io

Personal portfolio site for **Sri Koganti** — Senior Cloud & MLOps Engineer, Cork, Ireland.

Built with [Jekyll](https://jekyllrb.com) and the [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) theme. Deployed automatically to GitHub Pages via GitHub Actions.

---

## Local Development

**Prerequisites:** Ruby 3.3+, Bundler

```bash
# 1. Install dependencies
bundle install

# 2. Serve locally with live reload
bundle exec jekyll serve --livereload

# 3. Browse to http://localhost:4000
```

## Deployment

Every push to `main` triggers the GitHub Actions workflow in `.github/workflows/pages.yml`,
which builds the Jekyll site with `JEKYLL_ENV=production` and deploys to GitHub Pages.

No manual steps required.

## Project Structure

```
├── _config.yml              # Site configuration
├── Gemfile                  # Ruby dependencies
├── index.md                 # Home / splash page
├── _pages/
│   ├── about.md
│   ├── cv.md
│   ├── projects.md
│   └── contact.md
├── _projects/               # MLOps / AI project pages (collection)
│   ├── rag-career-assistant.md
│   ├── mlops-aws-pipeline.md
│   └── cloud-ai-platform.md
├── _data/
│   └── navigation.yml       # Top nav links
├── assets/
│   ├── css/main.scss        # Custom styles
│   ├── images/              # Profile photo, project thumbnails, banners
│   └── files/               # CV PDF
└── .github/
    └── workflows/
        └── pages.yml        # CI/CD — build & deploy to GitHub Pages
```

## Customisation Checklist

Before going live, update the following placeholders:

- [ ] `_config.yml` — `email`, `google_site_verification`, analytics tracking ID
- [ ] `_pages/cv.md` — Fill in real job dates, university, certifications
- [ ] `_pages/contact.md` — Review role preferences and outreach details
- [ ] `assets/images/` — Add `profile.jpg`, `hero-bg.jpg`, project thumbnails
- [ ] `assets/files/Sri_Koganti_Resume__AI.pdf` — Add PDF CV
- [ ] `_projects/*.md` — Update GitHub repo links once repos are public

## License

Content © Sri Koganti. Theme ([Minimal Mistakes](https://github.com/mmistakes/minimal-mistakes)) © Michael Rose, MIT License.
