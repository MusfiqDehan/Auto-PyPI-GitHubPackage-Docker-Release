# Auto-PyPI-GitHubPackage-Docker-Release

Just type "Release [your version number]", this action will automatically publish/release it in PyPI Package, GitHub Package and Docker Hub. All in One.

## Inputs

- `version`: Version number for the release. (required)

## Outputs

- `version`: The version of the release.

## Example Usage

```yaml

name: Release

on:
  push:
    branches:
      - master

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Get the current version
        id: current-version
        run: echo "version=$(python setup.py --version)" >> $GITHUB_ENV
        shell: bash


      - name: Run Release Action
        uses: MusfiqDehan/Auto-PyPI-GitHubPackage-Docker-Release@v1.0.7
        with:
          version: ${{ env.version }}
          pypi_token: ${{ secrets.PYPI_API_TOKEN }}
          dockerhub_username: ${{ secrets.DOCKERHUB_USERNAME }}
          dockerhub_password: ${{ secrets.DOCKERHUB_PASSWORD }}
          dockerhub_pat: ${{ secrets.DOCKERHUB_PAT }}
          docker_image_name: ${{ secrets.DOCKER_IMAGE_NAME }}
          github_token: ${{ secrets.GITHUB_TOKEN }}
          the_github_username: ${{ secrets.THE_GITHUB_USERNAME }}
          the_github_repo_name: ${{ secrets.THE_GITHUB_REPO_NAME }}


```

