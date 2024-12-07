# UHSDS Data Package

## Overview

Platform-agnostic design system data including tokens, components, patterns, and text content.

## Structure

### Tokens

- `tokens/src/`: Source token definitions
  - `base/`: Core design values
  - `aliases/`: Semantic mappings
  - `themes/`: Theme-specific tokens
- `tokens/dist/`: Token distributions
  - `css/`: CSS custom properties
  - `scss/`: SCSS variables
  - `json/`: Raw JSON tokens
  - `php/`: PHP constants

### Components

- `components/src/`: Component definitions
  - `atoms/`: Basic building blocks
  - `molecules/`: Combinations of atoms
  - `organisms/`: Complex compositions
- `components/dist/`: Platform-specific implementations
  - `timber/`: WordPress/Timber templates
  - `react/`: React components
  - `vue/`: Vue components
  - `metadata/`: Component documentation

### Patterns

- `patterns/src/`: Pattern definitions
  - `layouts/`: Layout patterns
  - `templates/`: Page templates
  - `pages/`: Full page patterns
- `patterns/dist/`: Pattern implementations
  - `timber/`: WordPress templates
  - `html/`: Static HTML
  - `metadata/`: Pattern documentation

### Text Content

- `text/src/`: Source content
  - `microcopy/`: UI text and labels
  - `documentation/`: Usage guidelines
  - `marketing/`: Marketing content
- `text/dist/`: Content distributions
  - `json/`: Structured content
  - `markdown/`: Documentation
  - `html/`: Formatted content

## Development

### Building Tokens

```bash
cd tokens
yarn build
```

### Building Components

```bash
cd components
yarn build
```

### Building Patterns

```bash
cd patterns
yarn build
```

### Building Text Content

```bash
cd text
yarn build
```

## Usage

### Consuming Tokens

```js
// CSS
@import '@uhsds/data/tokens/dist/css/tokens.css';

// SCSS
@import '@uhsds/data/tokens/dist/scss/tokens.scss';

// PHP
require_once '@uhsds/data/tokens/dist/php/tokens.php';
```

### Using Components

```php
// Timber
Timber::render('@uhsds/data/components/dist/timber/button.twig');

// React
import { Button } from '@uhsds/data/components/dist/react';

// Vue
import { Button } from '@uhsds/data/components/dist/vue';
```

## Documentation

Each domain contains its own documentation in its respective `dist/metadata` directory.
