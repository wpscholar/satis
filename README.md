# WP Scholar Composer Repository

[Satis](https://composer.github.io/satis/) is an open-source [Composer](https://getcomposer.org/) repository generator. This repository serves as Micahs's static file-based version of [Packagist](https://packagist.org/) and hosts the metadata of his packages.

## Usage

- Run `composer config repositories.wpscholar composer https://wpscholar.github.io/satis`
- Head over to https://wpscholar.github.io/satis/ to browse available packages.

## Adding A Repo

- Clone this repository: `git clone git@github.com:wpscholar/satis.git`
- Run `composer install`
- Run `composer satis add <url>` where `<url>` is the URL for the Git repo.
- Commit changes and push.
- In the remote repo, setup the following GitHub Action so that new releases will trigger Satis to rebuild.

```
name: Trigger Satis Build

on:
  release:
    types:
      - created

jobs:
  webhook:
    name: Send Webhook
    runs-on: ubuntu-latest
    steps:

    - name: Set Package
      id: package
      env:
        REPO: ${{ github.repository }}
      run: echo ::set-output name=PACKAGE::${REPO##*/}

    - name: Set Version
      id: tag
      run: echo ::set-output name=VERSION::${GITHUB_REF##*/}

    - name: Repository Dispatch
      uses: peter-evans/repository-dispatch@v1
      with:
        token: ${{ secrets.WEBHOOK_TOKEN }}
        repository: wpscholar/satis
        event-type: 'Trigger Satis Build'
        client-payload: >-
          {
            "vendor": "${{ github.repository_owner }}",
            "package": "${{ steps.package.outputs.PACKAGE }}",
            "version": "${{ steps.tag.outputs.VERSION }}"
          }

```

You must create a [personal access token](https://github.com/settings/tokens) with `repo` access and set it as the `WEBHOOK_TOKEN` secret on the remote repository. Also, make sure that GitHub Actions is enabled for the repository.
