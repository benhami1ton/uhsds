# GitHub Workflows Configuration

## Directory Structure

```
.github/
└── workflows/
    ├── token-pipeline.yml
    ├── component-pipeline.yml
    ├── plugin-deployment.yml
    ├── test-suite.yml
    └── documentation.yml
```

## Token Pipeline Workflow

```yaml
# .github/workflows/token-pipeline.yml
name: Design Token Pipeline

on:
  push:
    paths:
      - "tokens/**"
      - "tokens-config.json"
    branches:
      - main
      - "dev/**"
  pull_request:
    paths:
      - "tokens/**"

env:
  NODE_VERSION: "16"
  STYLE_DICTIONARY_CONFIG: "tokens-config.json"

jobs:
  validate_tokens:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}

      - name: Install Dependencies
        run: |
          npm ci
          npm install -g style-dictionary

      - name: Validate Token Structure
        run: node scripts/validate-tokens.js

      - name: Transform Tokens
        run: style-dictionary build --config ${{ env.STYLE_DICTIONARY_CONFIG }}

      - name: Run W3C Validation
        run: node scripts/validate-w3c.js

  build_css:
    needs: validate_tokens
    runs-on: ubuntu-latest
    steps:
      - name: Generate CSS Variables
        run: node scripts/generate-css.js

      - name: Upload CSS Artifacts
        uses: actions/upload-artifact@v3
        with:
          name: css-variables
          path: dist/css/

  create_release:
    if: github.ref == 'refs/heads/main'
    needs: build_css
    runs-on: ubuntu-latest
    steps:
      - name: Create Release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Component Pipeline Workflow

```yaml
# .github/workflows/component-pipeline.yml
name: Component Build Pipeline

on:
  push:
    paths:
      - "components/**"
    branches:
      - main
      - "feature/**"
  pull_request:
    paths:
      - "components/**"

env:
  PHP_VERSION: "8.1"
  COMPOSER_VERSION: "2"

jobs:
  validate_components:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ env.PHP_VERSION }}
          tools: composer:v${{ env.COMPOSER_VERSION }}

      - name: Install Dependencies
        run: composer install --prefer-dist

      - name: Validate Component Structure
        run: php scripts/validate-components.php

      - name: Run PHP Linting
        run: composer run-script lint

  run_tests:
    needs: validate_components
    runs-on: ubuntu-latest
    steps:
      - name: Run Unit Tests
        run: vendor/bin/phpunit --testsuite unit

      - name: Run Integration Tests
        run: vendor/bin/phpunit --testsuite integration

      - name: Generate Test Report
        run: php scripts/generate-test-report.php

  build_components:
    needs: run_tests
    runs-on: ubuntu-latest
    steps:
      - name: Build Components
        run: php scripts/build-components.php

      - name: Upload Component Artifacts
        uses: actions/upload-artifact@v3
        with:
          name: built-components
          path: dist/components/
```

## Plugin Deployment Workflow

```yaml
# .github/workflows/plugin-deployment.yml
name: WordPress Plugin Deployment

on:
  workflow_run:
    workflows:
      - "Design Token Pipeline"
      - "Component Build Pipeline"
    types:
      - completed
    branches:
      - main

jobs:
  prepare_deployment:
    runs-on: ubuntu-latest
    steps:
      - name: Download Artifacts
        uses: actions/download-artifact@v3

      - name: Prepare Plugin Package
        run: php scripts/prepare-plugin.php

  deploy_staging:
    needs: prepare_deployment
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - name: Deploy to Staging
        run: php scripts/deploy-wordpress.php
        env:
          WORDPRESS_URL: ${{ secrets.STAGING_URL }}
          DEPLOY_KEY: ${{ secrets.STAGING_DEPLOY_KEY }}

      - name: Run Health Checks
        run: php scripts/health-check.php

  deploy_production:
    needs: deploy_staging
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Deploy to Production
        if: github.event.workflow_run.conclusion == 'success'
        run: php scripts/deploy-wordpress.php
        env:
          WORDPRESS_URL: ${{ secrets.PRODUCTION_URL }}
          DEPLOY_KEY: ${{ secrets.PRODUCTION_DEPLOY_KEY }}
```

## Documentation Workflow

```yaml
# .github/workflows/documentation.yml
name: Documentation Generation

on:
  push:
    paths:
      - "components/**"
      - "tokens/**"
      - "docs/**"
    branches:
      - main

jobs:
  generate_docs:
    runs-on: ubuntu-latest
    steps:
      - name: Generate API Documentation
        run: php scripts/generate-api-docs.php

      - name: Generate Component Catalog
        run: php scripts/generate-catalog.php

      - name: Generate Token Inventory
        run: node scripts/generate-token-docs.js

      - name: Deploy Documentation
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./docs
```

## Environment Configuration

### 1. Repository Secrets

```yaml
secrets:
  STAGING_URL: "https://staging.example.com"
  PRODUCTION_URL: "https://production.example.com"
  STAGING_DEPLOY_KEY: "staging-deploy-key"
  PRODUCTION_DEPLOY_KEY: "production-deploy-key"
  GITHUB_TOKEN: "github-token"
```

### 2. Environment Variables

```yaml
env:
  NODE_VERSION: "16"
  PHP_VERSION: "8.1"
  COMPOSER_VERSION: "2"
  STYLE_DICTIONARY_CONFIG: "tokens-config.json"
```

## Branch Protection Rules

```yaml
branch_protection:
  main:
    required_status_checks:
      - "validate_tokens"
      - "validate_components"
      - "run_tests"
    required_pull_request_reviews: true
    enforce_admins: true
  dev:
    required_status_checks:
      - "validate_tokens"
      - "validate_components"
    required_pull_request_reviews: true
```
