# my digital garden

This digital garden uses the MkDocs template [![Built with Material for MkDocs](https://img.shields.io/badge/Material_for_MkDocs-526CFE?style=for-the-badge&logo=MaterialForMkDocs&logoColor=white)](https://squidfunk.github.io/mkdocs-material/) and is built with Jobin John's [obsidian-publish-mkdocs](https://github.com/jobindjohn/obsidian-publish-mkdocs) drawing inspiration from [the Blue Book](https://lyz-code.github.io/blue-book/) at [github](https://github.com/lyz-code/blue-book/blob/master/mkdocs.yml) and similar others.

## Categories

Every post has **exactly one** category — it's the single shelf a post lives on, driving the sidebar list and its `/blog/category/<name>/` page. Current categories:

- `philosophy` — speculative essays on mind, sentience, and the future of AI
- `generative ai` — the 2024 generative-modelling series (autoregressive, VAEs, flows, GANs, diffusion, evaluation)
- `representations` — the mechanistic-interpretability / representation-learning series (VAEs, SimCLR, SimSiam, Barlow Twins/VICReg)
- `dataviz` — D3.js and visualization posts
- `machine learning` — classical ML (bias-variance, boosting, trees, SVM, SGD)
- `gis` — mapping/geographic posts
- `engineering` — applied systems posts (tax copilot, enterprise knowledge fabric)
- `cryptography`

If a post's topic doesn't fit its category cleanly, use a **tag** instead of adding a second category (e.g. a philosophy post that touches on genai gets the `genai` tag, not a second category).

## Tags

Tags are free-form and multiple-per-post, browsable at `/blog/tags/`. They cover cross-cutting topics that don't warrant their own category — e.g. `sentience`, `genai`, and `engineering` all appear as tags on posts whose primary category is something else.

## Admonitions

Material for MkDocs supports 12 admonition types (`mkdocs.yml` already themes icons for all of them): `note`, `abstract`, `info`, `tip`, `success`, `question`, `warning`, `failure`, `danger`, `bug`, `example`, `quote`. Use `!!! type "Title"` for an always-expanded box, `???` for collapsed, `???+` for expanded-but-collapsible.

So far only `info` is used, for asides and sanity-check callouts. Other types worth reaching for:

- `warning` / `danger` — a real gotcha (numerical instability, common mistake)
- `tip` — a suggestion or best practice
- `question` — posing something before answering it
- `quote` (alias `cite`) — a primary reference: the paper or talk a post is built on
