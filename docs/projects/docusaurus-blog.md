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

This project is based on the Developer Akademie Docusaurus starter template and was customized to create a personal DevSecOps learning journal and portfolio.

#### Site identity

- Replaced the default site title `DSO Live Demo Docs` with `Dominique Dockal Learning Journal`.
- Replaced the default tagline `Dinosaurs are cool` with a project-specific description for my DevSecOps learning journal, notes, and project documentation.
- Updated the navbar title from the generic `My Site` label to my own name, `Dominique Dockal`.
- Adjusted the navbar logo metadata by replacing the default alt text with `Dominique Dockal Logo`.

#### Environment variables and deployment configuration

- Kept the existing environment-based configuration approach from the template and extended it with project-specific default values.
- Added a `GIT_REPOSITORY_URL` entry to `example.env` to document the expected repository variable.
- Introduced a dedicated `gitRepositoryUrl` constant in `docusaurus.config.ts` that reads `process.env.GIT_REPOSITORY_URL` and falls back to my repository URL.
- Configured `url`, `baseUrl`, `organizationName`, `projectName`, and `deploymentBranch` with environment-variable lookups and fallback defaults so the site can build locally and in CI/CD environments such as GitHub Actions even if no `.env` file is loaded automatically.
- Updated the GitHub Pages configuration to match my own repository setup by using `https://dominiquedockal.github.io` as the default deployment URL and `/My_DocuSaurus_Blog/` as the default base path.

#### Repository references and edit links

- Replaced the original hardcoded template repository links with references to my own GitHub repository.
- Updated the docs `editUrl` to use the centralized `gitRepositoryUrl` value.
- Updated the blog `editUrl` to use the same repository reference so edit links stay consistent across docs and blog pages.
- Replaced the navbar GitHub link so it points to my own repository instead of the original template repository.

#### Footer customization

- Extended the **Docs** footer column by adding a project-related navigation entry (`/docs/projects/overview`) in addition to the existing tutorial link.
- Removed the default **Community** footer column from the starter template.
- Reworked the **More** footer column so it now links to my own repository and also includes a `Template` link to the original Developer Akademie starter repository.
- Replaced the original copyright message with a personalized version that references me as the author and adds the note `Extended from the developer-akademie-starter`.

#### Optional blog integration

- Kept the existing `BLOG_ENABLED` environment-based switch from the starter template to control whether the blog feature is enabled.
- Reused the conditional blog configuration so the blog plugin is only active when `BLOG_ENABLED` is set to `true`.
- Adjusted the conditional navigation behavior so the Blog link is added to the navbar when blog support is enabled.
- Moved the conditional footer Blog link into the **Docs** footer section to better match the structure of my customized footer..

### Deployment

- The site is deployed automatically via **GitHub Actions** to **GitHub Pages** whenever changes are merged into the main branch.
- The live site is served from the GitHub Pages URL configured via `url` and `baseUrl` in `docusaurus.config.ts`.

## Further References

- [Docusaurus Documentation](https://docusaurus.io/docs)
- [npm Documentation](https://docs.npmjs.com/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Developer Akademie Dev Blog Template](https://github.com/Developer-Akademie-DevSecOpsKurs/dev-blog-template)