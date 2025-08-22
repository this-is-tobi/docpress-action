# Docpress action 🤖

Automate your portfolio or documentation website using [Docpress](https://github.com/this-is-tobi/docpress). This GitHub Action collects data from GitHub, builds the site with Vitepress, and deploys it to GitHub Pages.

## Description

This GitHub Action is designed to simplify the process of creating a portfolio or documentation website. It integrates seamlessly with Docpress and Vitepress to generate a beautiful, customizable site that showcases your work.

### Usage

Here is a basic example of how to use the GitHub Action:

```yml
name: My Website
on:
  workflow_dispatch:
    inputs:
      deploy: true

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Install and run Docpress
      uses: this-is-tobi/docpress-action@v0
      with:
        deploy: true
```

> [!TIP]
> If you want to deploy your site to GitHub Pages, set the `deploy` input to `true`, like in the example above.

## Inputs

| Input                | Description                                                                                                                                                                            | Default Value      |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| `branch`             | The branch used to collect Git provider data                                                                                                                                           | `main`             |
| `extraPublicContent` | Comma-separated list of additional files or directories for Vitepress public folder. This can be useful for adding custom content, such as images or videos, to your site.             | -                  |
| `forks`              | Create a dedicated fork page for external contributions. When set to true, the Action will generate a separate page listing all contributors and their respective contributions.       | `false`            |
| `extraHeaderPages`   | Comma-separated list of additional files for Vitepress header pages. This can be used to add custom navigation or other dynamic elements to your site's headers.                       | -                  |
| `reposFilter`        | Comma-separated repositories to retrieve from Git provider. Defaults to all user's public repositories, but you can filter the results by specifying specific repository names or IDs. | -                  |
| `extraTheme`         | Comma-separated list of additional files or directories for Vitepress theme. This allows you to customize the look and feel of your site with ease.                                    | -                  |
| `vitepressConfig`    | Path to the Vitepress configuration file. By default, the Action will use the default vitepress configuration. If you need to specify a custom config, provide its path here.          | -                  |
| `usernames`          | Comma-separated list of git provider usernames to collect data. This can be used to filter results by user or to retrieve specific repositories from a shared account.                 | `<your_usernames>` |
| `token`              | GitHub token to collect data. Make sure to set this securely, as it will grant access to your repositories and settings.                                                               | -                  |
| `config`             | Path to the docpress configuration file. This allows you to customize the Action's behavior by specifying a custom config file.                                                        | -                  |
| `version`            | Docpress version. You can specify a specific version or use 'latest' for the most recent one.                                                                                          | `latest`           |
| `deploy`             | Whether or not to deploy generated website to GitHub Pages. Set this to true if you want your site to be publicly accessible.                                                          | `false`            |

## Advanced usage

With a manual trigger :

```yml
name: My Website with Custom Configuration

on:
  workflow_dispatch:
    inputs:
      branch:
        description: The branch used to collect Git provider data
        default: main
        required: true
        type: string
      extraPublicContent:
        description: Comma-separated list of additional files or directories for Vitepress public folder
        default: ""
        required: false
        type: string
      forks:
        description: Create a dedicated fork page for external contributions
        default: true
        required: false
        type: boolean
      extraHeaderPages:
        description: Comma-separated list of additional files for Vitepress header pages
        default: ""
        required: false
        type: string
      reposFilter:
        description: Comma-separated repositories to retrieve from Git provider (defaults to all user\'s public repositories)
        default: ""
        required: false
        type: string
      extraTheme:
        description: Comma-separated list of additional files or directories for Vitepress theme
        default: ""
        required: false
        type: string
      vitepressConfig:
        description: Path to the Vitepress configuration file (default is .vitepress.config.json)
        default: ""
        required: false
        type: string
      version:
        description: Docpress version (use \'latest\' for the most recent one)
        default: latest
        required: false
        type: string
      deploy:
        description: Whether or not to deploy generated website to GitHub Pages
        default: false
        required: false
        type: boolean

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Deploy website to GitHub Pages
      uses: this-is-tobi/docpress-action@v0
      with:
        branch: ${{ inputs.branch }}
        extraPublicContent: ${{ inputs.extraPublicContent }}
        forks: ${{ inputs.forks }}
        extraHeaderPages: ${{ inputs.extraHeaderPages }}
        reposFilter: ${{ inputs.reposFilter }}
        extraTheme: ${{ inputs.extraTheme }}
        vitepressConfig: ${{ inputs.vitepressConfig }}
        version: ${{ inputs.version }}
        deploy: ${{ inputs.deploy }}
```

With schedule :

```yaml
name: My Website with Custom Configuration

on:
  schedule:
  - cron: "0 2 * * *"

env:
  branch: main
  extraPublicContent: ./extras/public
  forks: true
  extraHeaderPages: ./extras/pages
  reposFilter: repo1,repo2,repo3
  extraTheme: ./extras/theme
  vitepressConfig: ./extras/.vitepress.config.json
  version: latest
  deploy: false

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Deploy website to GitHub Pages
      uses: this-is-tobi/docpress-action@v0
      with:
        branch: ${{ env.branch }}
        extraPublicContent: ${{ env.extraPublicContent }}
        forks: ${{ env.forks }}
        extraHeaderPages: ${{ env.extraHeaderPages }}
        reposFilter: ${{ env.reposFilter }}
        extraTheme: ${{ env.extraTheme }}
        vitepressConfig: ${{ env.vitepressConfig }}
        version: ${{ env.version }}
        deploy: ${{ env.deploy }}
```

With [Docpress config](https://github.com/this-is-tobi/docpress/blob/main/docs/03-advanced-usage.md#docpress-configuration) :

```yaml
name: My Website with Custom Configuration

on:
  workflow_dispatch:

env:
  config: ./docpress.config.json

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Deploy website to GitHub Pages
      uses: this-is-tobi/docpress-action@v0
      with:
        config: ${{ env.config }}
```

## Contributing

Contributions are welcome! If you have a feature or bugfix to propose, please create an issue or submit a pull request. We're always happy to discuss new ideas and collaborate with other developers.

## Troubleshooting

*   Make sure the Action is configured correctly, especially when specifying custom configurations for Vitepress.
*   Verify that your GitHub token has the necessary permissions to access the repositories and settings you need.
*   If deploying to GitHub Pages fails, check if there are any errors in the deployment process or if the site has been deployed successfully.

## Links to Relevant Documentation

For more information about Docpress and Vitepress, please refer to their respective documentation:

*   [Docpress](https://this-is-tobi.com/docpress/introduction)
*   [Vitepress](https://vitepress.dev/)

I hope this updated version of your README file is helpful in communicating the features and usage of your GitHub Action!
