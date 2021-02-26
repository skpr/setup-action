# Skpr Setup Action

This action sets up the Skpr client. 

It is typically used in conjunction with the following actions:

- [actions/checkout@v2](https://github.com/actions/checkout)
- [skpr/info-action](https://github.com/skpr/info-action)
- [skpr/package-action](https://github.com/skpr/package-action)
- [skpr/deploy-action](https://github.com/skpr/deploy-action)
- [skpr/exec-action](https://github.com/skpr/exec-action)

## Example Workflow Usage
```
name: skpr-deploy
on:
  push:
    branches: [actions-test]
env:
  SKPR_USERNAME: ${{ secrets.SKPR_USERNAME }}
  SKPR_PASSWORD: ${{ secrets.SKPR_PASSWORD }}

jobs:
  deploy:
    runs-on: ubuntu-latest
    env:
      SKPR_ENV: dev
    steps:
      - name: Checkout Code
        uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - name: Skpr Setup
        uses: ./.github/actions/setup
      - name: Skpr Info
        id: skpr-info
        uses: ./.github/actions/info
        with:
          env: ${{ env.SKPR_ENV }}
      - name: Skpr Package
        uses: ./.github/actions/package
        with:
          version: ${{ steps.skpr-info.outputs.version }}
      - name: Skpr Deploy
        uses: ./.github/actions/deploy
        with:
          version: ${{ steps.skpr-info.outputs.version }}
          env: ${{ env.SKPR_ENV }}
      - name: Run Skpr Post-deploy Command
        uses: ./.github/actions/exec
        with:
          env: ${{ env.SKPR_ENV }}
          command: make deploy

```
