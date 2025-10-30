# Copilot Instructions for 11ty-to-compute Blog

This is an **Eleventy (11ty) static site generator** project that builds a blog with deployment to **Fastly Compute**. The codebase follows a source-deploy pattern designed for GitHub Codespaces.

## Architecture Overview

**Source-Deploy Pattern**: Content lives in `source/`, builds to `deploy/_site/`
- `source/` - All content, layouts, and data files
- `deploy/_site/` - Generated static site (git-tracked for Fastly deployment)  
- `assets/` - Global stylesheets and static assets (copied during build)

**Template Hierarchy**: Nunjucks templates in `source/_layouts/`
- `base.njk` - Root template with header/nav/footer
- `post.njk` - Individual blog post template
- `posts.njk` - Blog listing page template
- `home.njk`, `page.njk` - Other page types

## Key Workflows

**Development**: `npm start` runs Eleventy with live reload on port 8080
**Build**: `npm run build` generates static files to `deploy/_site/`
**Share Draft**: `./helpers/share.sh` makes Codespace preview publicly accessible
**Deploy**: `./helpers/publish.sh` deploys to Fastly Compute using `@fastly/cli`

## Content Patterns

**Post Structure**: Markdown files in `source/posts/` with frontmatter:
```markdown
---
title: Post Title
description: Meta description
date: 2025-04-01
tags: [posts]
layout: post.njk
---
```

**Posts Collection**: Configured in `.eleventy.js` - auto-sorts by date, adds prev/next post navigation
- All files tagged with `posts` are included
- `source/posts/posts.json` applies the `posts` tag to all files in directory

**Global Data**: `source/_data/metadata.json` contains site title and description

## Eleventy Configuration (.eleventy.js)

**Critical Settings**:
- Input: `source/`, Output: `./deploy/_site`
- Template formats: `html`, `njk`, `md`, `css`, `jpeg`, `jpg`, `png`, `svg`
- `addPassthroughCopy("assets")` - copies assets/ to build output
- Custom `htmlDateString` filter using Luxon for date formatting

- Navigation plugin enabled for menu generation

**Posts Collection Logic**: Automatically creates prev/next post relationships and sorts by date descending

## Template System (Nunjucks)

- **Template inheritance**: `post.njk` extends `base.njk`
- **Global data**: `metadata` object from `source/_data/metadata.json`
- **Post navigation**: `prevPost`/`nextPost` added via `.eleventy.js`
- **Date formatting**: Use `htmlDateString` filter (from Luxon)

## Fastly Deployment Integration

**Helper Scripts**: Located in `helpers/` directory
- `publish.sh` - Full Fastly deployment with service creation/update
- `share.sh` - Makes Codespace preview publicly accessible 
- `welcome.sh` - Codespace startup message

**Deployment Flow**: Uses `@fastly/cli` to deploy `deploy/_site/` contents as a Compute service
- Project name format: `${GITHUB_USER}-${RepositoryName}`
- Requires `FASTLY_API_TOKEN` environment variable

## Development Notes

**Codespace Integration**: Project designed for GitHub Codespaces with automatic preview
**File Structure**: Keep layouts in `source/_layouts/`, posts in `source/posts/`, global styles in `assets/`

## Asset Handling

- CSS/images in `assets/` → copied to build via `addPassthroughCopy`
- Reference assets as `/assets/filename.ext` in templates
- Images example in `source/posts/fourth-post.md`

## Navigation

- Manual nav in `base.njk` header (About link)
- Post navigation auto-generated in `post.njk` template
- Home link always points to `/` (site title)
- Uses `@11ty/eleventy-navigation` plugin - add `eleventyNavigation` to frontmatter for automatic menu inclusion