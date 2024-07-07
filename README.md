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

      - name: Check if version exists on PyPI
        id: check-version
        run: |
          response=$(curl -s -o /dev/null -w "%{http_code}" https://pypi.org/project/data-preprocessors/${{ env.version }}/)
          if [ $response -eq 200 ]; then
            echo "Version already exists on PyPI. Incrementing version..."
            new_version=$(python -c "import re; v='${{ env.version }}'; parts = v.split('.'); parts[-1] = str(int(parts[-1]) + 1); print('.'.join(parts))")
            echo "new_version=$new_version" >> $GITHUB_ENV
          else
            echo "Version does not exist. Using provided version."
            echo "new_version=${{ env.version }}" >> $GITHUB_ENV
          fi
        shell: bash
  
        - name: Update version in setup.py if necessary
          run: |
            current_version=$(grep -oP "version='\K[^']+" setup.py)
            if [[ "${{ env.new_version }}" != "${{ current_version }}" ]]; then
              sed -i "s/version='${{ current_version }}'/version='${{ env.new_version }}'/g" setup.py
            fi
          shell: bash

      - name: Run Release Action
        uses: MusfiqDehan/Auto-PyPI-GitHubPackage-Docker-Release@v1.0.4
        with:
          version: ${{ env.version }}
          pypi_token: ${{ secrets.PYPI_API_TOKEN }}
          dockerhub_username: ${{ secrets.DOCKERHUB_USERNAME }}
          dockerhub_password: ${{ secrets.DOCKERHUB_PASSWORD }}
          docker_image_name: 'repo-name-image'
          github_token: ${{ secrets.GITHUB_TOKEN }}
          the_github_username: ${{ secrets.THE_GITHUB_USERNAME }}
          the_github_repo_name: 'repository-name'


```

