# Docpress action 🤖

Automate your portfolio or documentation website with [Docpress](https://github.com/this-is-tobi/docpress). This GitHub Action collects the documentation of your repositories from GitHub or GitLab, builds the website with Vitepress, and can deploy it to GitHub Pages.

## Usage

The following workflow builds the website of the repository owner on each push to `main`, with a contributions page, and deploys it to GitHub Pages:

```yml
name: Website

on:
  push:
    branches:
    - main
  workflow_dispatch:

jobs:
  website:
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.docpress.outputs.page_url }}
    steps:
    - name: Checkout repository
      uses: actions/checkout@v7

    - name: Build and deploy the website
      id: docpress
      uses: this-is-tobi/docpress-action@v0
      with:
        contributions: true
        deploy: true
```

> [!TIP]
> GitHub Pages has to deploy from GitHub Actions: in the repository settings, under *Pages*, set *Source* to *GitHub Actions*.

Without `deploy: true`, the website is still built in `docpress/.vitepress/dist` and uploaded as a GitHub Pages artifact, for the next steps or for a later job using [actions/deploy-pages](https://github.com/actions/deploy-pages).

The action runs the [docpress image](https://github.com/this-is-tobi/docpress/pkgs/container/docpress), so it needs a Linux runner with Docker, such as `ubuntu-latest`.

## Inputs

Every docpress command line option has an input, named like the matching key of the [configuration file](#configuration-file). Only the inputs that are set are passed to docpress.

| Input | Description | Default |
| --- | --- | --- |
| `acronyms` | Comma-separated words kept exactly as written in generated sidebar labels and home page titles, such as `CLI` or `API`. | - |
| `branch` | Branch used to collect Git provider data. Defaults to each repository's own default branch. | - |
| `config` | Path to the docpress configuration file, relative to the repository root. Only the inputs that are set are passed to docpress, and they take precedence over the file. | - |
| `contributions` | Set to `true` to create the contributions page, listing the public repositories owned by others where the user's pull requests were merged. | - |
| `contributionsFilter` | Comma-separated owners (`owner`) or repositories (`owner/repo`) shown on the contributions page. Prefix an entry with `!` to hide it instead. | - |
| `extraHeaderPages` | Comma-separated list of additional files or directories for Vitepress header pages. | - |
| `extraPublicContent` | Comma-separated list of additional files or directories for Vitepress public folder. | - |
| `extraTheme` | Comma-separated list of additional files or directories for Vitepress theme. | - |
| `forks` | Deprecated alias of contributions. | - |
| `gitProvider` | Git provider used to retrieve data, `github` or `gitlab`. Defaults to `github`. | - |
| `lastUpdated` | Set to `true` to show the date of each page's last Git commit. | - |
| `logLevel` | Verbosity of the docpress output, among `error`, `warn`, `info`, `trace` and `debug`. Defaults to `info`. | - |
| `reposFilter` | Comma-separated repositories to retrieve from Git provider. Defaults to all user's public repositories. | - |
| `sidebarCollapsed` | Collapse behaviour of the generated sidebar groups. `true` collapses them, `false` expands them, `null` makes them non-collapsible. | - |
| `sidebarMode` | Shape of the generated sidebar. `single` lists every repository in one sidebar, `multi` gives each repository its own. | - |
| `token` | Token used to collect data from the Git provider. Defaults to the workflow token for GitHub. | - |
| `usernames` | Comma-separated list of git provider usernames to collect data. Defaults to the repository owner, unless a configuration file is given. | - |
| `vitepressConfig` | Path to the Vitepress configuration file. | - |
| `websiteTagline` | Website tagline. | - |
| `websiteTitle` | Website title. | - |
| `version` | Docpress version, a tag of the ghcr.io/this-is-tobi/docpress image. Defaults to the latest 0.x release. | `0` |
| `deploy` | Whether or not to deploy generated website to GitHub Pages. | `false` |
| `artifactName` | Name of the uploaded GitHub Pages artifact, to tell apart several builds in one workflow run. | `github-pages` |

## Outputs

| Output | Description |
| --- | --- |
| `page_url` | URL of the deployed website, when deploy is `true`. |

## Configuration file

A [docpress configuration file](https://github.com/this-is-tobi/docpress/blob/main/docs/03-advanced-usage.md#docpress-configuration) holds the same options and more, such as the home page hero or the Vitepress configuration. Give its path with `config`:

```yml
- name: Build and deploy the website
  id: docpress
  uses: this-is-tobi/docpress-action@v0
  with:
    config: ./docpress.config.json
    deploy: true
```

- Paths, in the inputs and in the file, are relative to the repository root.
- Inputs that are set take precedence over the file, so keep each option in one place.
- With a configuration file, the usernames no longer default to the repository owner: set them in the file or with `usernames`.

## Token

The action passes docpress the workflow token, so its GitHub API requests are authenticated. Set `token` to use another one, such as a personal access token for a higher rate limit, or a GitLab token: GitLab never receives the workflow token. docpress reads the token from the environment since version 0.10.2, so an older `version` builds without one.

## Manual and scheduled runs

```yml
name: Website

on:
  schedule:
  - cron: "0 2 * * *"
  workflow_dispatch:
    inputs:
      deploy:
        description: Deploy the website to GitHub Pages
        type: boolean
        default: false

jobs:
  website:
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.docpress.outputs.page_url }}
    steps:
    - name: Checkout repository
      uses: actions/checkout@v7

    - name: Build the website
      id: docpress
      uses: this-is-tobi/docpress-action@v0
      with:
        config: ./docpress.config.json
        # Scheduled runs always deploy, manual runs only when asked
        deploy: ${{ github.event_name == 'schedule' || inputs.deploy }}
```

## Troubleshooting

- `Cannot read config file`: the path is relative to the repository root, and the repository has to be checked out before the action runs.
- The deployment fails: the job needs the `pages: write` and `id-token: write` permissions and the `github-pages` environment, and GitHub Pages has to deploy from GitHub Actions.
- A repository is missing: docpress only lists public repositories, and reads each one from its own default branch unless `branch` says otherwise.

## Contributing

Contributions are welcome! If you have a feature or bugfix to propose, please create an issue or submit a pull request.

## Links

- [Docpress](https://this-is-tobi.com/docpress/introduction)
- [Vitepress](https://vitepress.dev/)
