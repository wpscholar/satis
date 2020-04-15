# WP Scholar Composer Repository

## Usage

Add this to your `composer.json` file:

```
{
  "repositories": [
		{
    		"type": "composer",
    		"url": "https://wpscholar.github.io/satis/"
  		}
	]
}
```

Then, head over to https://wpscholar.github.io/satis/ to browse available packages.

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
        event-type: trigger_satis_build
        client-payload: >-
          {
            "vendor": "${{ github.repository_owner }}",
            "package": "${{ steps.package.outputs.PACKAGE }}",
            "version": "${{ steps.tag.outputs.VERSION }}"
          }

```
