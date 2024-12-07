# UHSDS WordPress Plugin

## Overview

WordPress plugin implementation of the UH Style Design System.

## Features

- Component rendering system
- Token management and application
- Fusion Builder integration
- WordPress admin interface
- Theme customization support

## Installation

1. Install dependencies:
   ```bash
   composer install
   ```
2. Activate the plugin in WordPress admin
3. Configure settings in the UHSDS admin panel

## Development

1. Set up local WordPress environment
2. Create symbolic link to plugin directory
3. Run `composer install` for dependencies
4. Activate plugin in WordPress

## Structure

- `admin/`: WordPress admin interfaces
- `includes/`: Core plugin classes
  - `Components/`: Component implementations
  - `Tokens/`: Token management
  - `Integrations/`: Third-party integrations
- `public/`: Public-facing assets
- `templates/`: Timber templates

## Testing

Run tests with:

```bash
composer test
```

## Documentation

See the main `/docs` directory for comprehensive documentation.
