# Copilot Instructions for 11ty-to-compute Blog# Copilot Instructions for 11ty-to-compute Blog



This is an **Eleventy (11ty) static site generator** project designed to deploy to **Fastly Compute** for free hosting. The project creates a simple blog with Markdown posts.This is an Eleventy (11ty) static site generator project that builds a blog with deployment to Fastly Compute. The codebase follows a specific content-first architecture designed for GitHub Codespaces.



## Architecture Overview## Architecture Overview



- **Source**: `source/` contains all content and templates**Source-Deploy Pattern**: Content lives in `source/`, builds to `deploy/_site/`

- **Build Output**: `deploy/_site/` (auto-generated, don't edit)- `source/` - All content, layouts, and data files

- **Deployment**: `deploy/_app/` (auto-generated Fastly app)- `deploy/_site/` - Generated static site (git-tracked for Fastly deployment)

- **Assets**: `assets/` for CSS and images (copied to build)- `assets/` - Global stylesheets and static assets (copied during build)



## Key File Structure**Template Hierarchy**: Nunjucks templates in `source/_layouts/`

- `base.njk` - Root template with header/nav/footer

```- `post.njk` - Individual blog post template

source/- `posts.njk` - Blog listing page template

├── _data/metadata.json          # Site metadata (title, description)- `home.njk`, `page.njk` - Other page types

├── _layouts/                    # Nunjucks templates

│   ├── base.njk                # Main HTML wrapper## Key Workflows

│   ├── post.njk                # Individual post template

│   └── posts.njk               # Post listing template**Development**: `npm start` runs Eleventy with live reload on port 8080

├── posts/**Build**: `npm run build` generates static files to `deploy/_site/`

│   ├── posts.json              # Sets "posts" tag for all files**Share Draft**: `./helpers/share.sh` makes Codespace preview publicly accessible

│   └── *.md                    # Individual blog posts**Deploy**: `./helpers/publish.sh` deploys to Fastly Compute using `@fastly/cli`

└── *.md                        # Pages (index, about, posts)

```## Content Patterns



## Content Creation Patterns**Post Structure**: Markdown files in `source/posts/` with frontmatter:

```markdown

### Blog Posts---

- Place in `source/posts/`title: Post Title

- Required frontmatter:description: Meta description

  ```yamldate: 2025-04-01

  ---tags: [posts]

  title: Post Titlelayout: post.njk

  description: Brief description---

  date: YYYY-MM-DD```

  tags: [posts]  # Inherited from posts.json

  layout: post.njk**Posts Collection**: Configured in `.eleventy.js` - auto-sorts by date, adds prev/next post navigation

  ---- All files tagged with `posts` are included

  ```- `source/posts/posts.json` applies the `posts` tag to all files in directory

- Posts auto-sort by date (newest first) via `.eleventy.js` collection

**Global Data**: `source/_data/metadata.json` contains site title and description

### Pages

- Place in `source/` root## Eleventy Configuration (.eleventy.js)

- Use `layout: page.njk` or extend `base.njk`

**Critical Settings**:

## Development Commands- Input: `source/`, Output: `./deploy/_site`

- Template formats: `html`, `njk`, `md`, `css`, `jpeg`, `jpg`, `png`, `svg`

- **Start dev server**: `npm start` (runs `eleventy --serve`)- `addPassthroughCopy("assets")` - copies assets/ to build output

- **Build only**: `npm run build` (outputs to `deploy/_site/`)- Custom `htmlDateString` filter using Luxon for date formatting

- **Deploy**: Click 🚀 Publish button (runs `helpers/publish.sh`)- Navigation plugin enabled for menu generation



## Template System (Nunjucks)**Posts Collection Logic**: Automatically creates prev/next post relationships and sorts by date descending



- **Template inheritance**: `post.njk` extends `base.njk`## Fastly Deployment Integration

- **Global data**: `metadata` object from `source/_data/metadata.json`

- **Post navigation**: `prevPost`/`nextPost` added via `.eleventy.js`**Helper Scripts**: Located in `helpers/` directory

- **Date formatting**: Use `htmlDateString` filter (from Luxon)- `publish.sh` - Full Fastly deployment with service creation/update

- `share.sh` - Makes Codespace preview publicly accessible 

## Critical Configuration Files- `welcome.sh` - Codespace startup message



- **`.eleventy.js`**: Main config, collections, filters, passthrough copy**Deployment Flow**: Uses `@fastly/cli` to deploy `deploy/_site/` contents as a Compute service

- **`source/posts/posts.json`**: Auto-tags all posts in directory- Project name format: `${GITHUB_USER}-${RepositoryName}`

- **`package.json`**: Dependencies and npm scripts- Requires `FASTLY_API_TOKEN` environment variable



## Deployment Workflow## Development Notes



1. Content builds to `deploy/_site/` via Eleventy**Codespace Integration**: Project designed for GitHub Codespaces with automatic preview

2. `helpers/publish.sh` creates Fastly Compute app in `deploy/_app/`**File Structure**: Keep layouts in `source/_layouts/`, posts in `source/posts/`, global styles in `assets/`

3. Requires `FASTLY_API_TOKEN` environment variable**Navigation**: Uses `@11ty/eleventy-navigation` plugin - add `eleventyNavigation` to frontmatter for automatic menu inclusion
4. Service named `${GITHUB_USER}-${RepositoryName}`

## Asset Handling

- CSS/images in `assets/` → copied to build via `addPassthroughCopy`
- Reference assets as `/assets/filename.ext` in templates
- Images example in `source/posts/fourth-post.md`

## Navigation

- Manual nav in `base.njk` header (About link)
- Post navigation auto-generated in `post.njk` template
- Home link always points to `/` (site title)