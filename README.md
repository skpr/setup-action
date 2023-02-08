# Skpr Setup Action

A [Github Action](https://docs.github.com/en/actions) for automated deployments to [Skpr](https://www.skpr.com.au)

This action sets up the Skpr client. 

It is typically used with the following actions:

- [actions/checkout@v2](https://github.com/actions/checkout)
- [skpr/setup-action@v1](https://github.com/skpr/setup-action)
- [skpr/info-action@v1](https://github.com/skpr/info-action)
- [skpr/package-action@v1](https://github.com/skpr/package-action)
- [skpr/deploy-action@v1](https://github.com/skpr/deploy-action)
- [skpr/exec-action@v1](https://github.com/skpr/exec-action)

## Secrets

You need to set your Skpr credentials by setting the `SKPR_USERNAME` and `SKPR_PASSWORD` secrets.

## Example Workflow Usage
```
name: skpr-deploy
on:
  push:
    branches: [master]
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
        uses: skpr/action-setup@v1
      - name: Skpr Info
        id: skpr-info
        uses: skpr/action-info@v1
        with:
          env: ${{ env.SKPR_ENV }}
      - name: Skpr Package
        uses: skpr/action-package@v1
        with:
          version: ${{ steps.skpr-info.outputs.version }}
      - name: Skpr Deploy
        uses: skpr/action-deploy@v1
        with:
          version: ${{ steps.skpr-info.outputs.version }}
          env: ${{ env.SKPR_ENV }}
      - name: Run Skpr Post-deploy Command
        uses: skpr/action-exec@v1
        with:
          env: ${{ env.SKPR_ENV }}
          command: make deploy

```
