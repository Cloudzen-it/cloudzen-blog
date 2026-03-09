# Cloudzen DevOps Blog

Technical blog about DevOps, cloud infrastructure, Kubernetes, CI/CD, and platform engineering.

**Live:** [blog.cloudzendevops.com](https://blog.cloudzendevops.com)

## Stack

- [Astro](https://astro.build) — static site generator
- [Cloudflare Pages](https://pages.cloudflare.com) — hosting & CDN
- GitHub Actions — CI/CD (auto-deploy on push to `main`)

## Development

```sh
npm install
npm run dev       # localhost:4321
npm run build     # production build → dist/
```

## Adding posts

Create a `.md` or `.mdx` file in `src/content/blog/`:

```md
---
title: "Your Post Title"
description: "Brief description"
pubDate: "2026-03-09"
heroImage: "/images/your-image.jpg"
---

Your content here.
```

## Contact

**Daniel Jeshkov** — DevOps & Cloud Engineer

[![LinkedIn](https://img.shields.io/badge/LinkedIn-daniel--jeshkov--devops-blue?logo=linkedin)](https://linkedin.com/in/daniel-jeshkov-devops/)
[![Website](https://img.shields.io/badge/Web-cloudzendevops.com-purple)](https://cloudzendevops.com)
