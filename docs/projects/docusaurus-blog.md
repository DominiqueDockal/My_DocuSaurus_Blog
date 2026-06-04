# Docusaurus Blog

A personal DevSecOps learning journal and developer blog based on the Developer Akademie Docusaurus starter template.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Quickstart](#quickstart)
- [Usage](#usage)
- [Further References](#further-references)

import GithubLinkAdmonition from '@site/src/components/GithubLinkAdmonition';

<GithubLinkAdmonition
  link="https://github.com/Developer-Akademie-DevSecOpsKurs/dev-blog-template"
  title="GitHub Tip"
  type="tip"
>
Check out the starter template repository to review the original implementation.
</GithubLinkAdmonition>

## Prerequisites

- Git installed locally
- Node.js (version 18 or later)
- npm (or pnpm, if you prefer)
- A GitHub account to host the repository and GitHub Pages

## Quickstart

These steps show how to get the blog running locally with the current configuration:

```bash
# 1. Clone the personalized repository
git clone https://github.com/DominiqueDockal/My_DocuSaurus_Blog.git

# 2. Change into the project directory
cd My_DocuSaurus_Blog

# 3. Install dependencies
npm install

# 4. Start the local development server
npm run start
```

After running `npm run start`, the site will be available in your browser (usually at `http://localhost:3000`) and will reload automatically when you change files.

## Usage

### Local development

- `npm run start`  
  Starts the Docusaurus development server with hot reload for local editing.

### Build and preview

- `npm run build`  
  Creates an optimized production build of the site in the `build` directory.

- `npm run serve`  
  Serves the contents of the `build` directory locally so you can test the production build in your browser.

### Configuration and customization

This project is based on the Developer Akademie Docusaurus starter template and was customized to act as a personal developer blog and learning journal:

- The site **title** and **tagline** were updated to reflect the personal learning journal.
- Deployment-related settings (`url`, `baseUrl`, `organizationName`, `projectName`, `deploymentBranch`) were configured for GitHub Pages.
- The **repository URL** and `editUrl` values were set so docs and blog posts link back to the correct GitHub repository.
- The **navbar** and **footer** were customized to include:
  - a Docs link
  - a Blog link (optional, controlled via configuration)
  - links to the personal repository and the original template repository

### Deployment

- The site is deployed automatically via **GitHub Actions** to **GitHub Pages** whenever changes are merged into the main branch.
- The live site is served from the GitHub Pages URL configured via `url` and `baseUrl` in `docusaurus.config.ts`.

## Further References

- [Docusaurus Documentation](https://docusaurus.io/docs)
- [npm Documentation](https://docs.npmjs.com/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Developer Akademie Dev Blog Template](https://github.com/Developer-Akademie-DevSecOpsKurs/dev-blog-template)