# Theme Development Guide

## Overview

This guide covers the process of creating, customizing, and managing themes within the UHSDS framework.

## Theme Structure

### 1. Basic Theme Structure

```
theme/
├── tokens/
│   ├── colors.json
│   ├── typography.json
│   └── spacing.json
├── overrides/
│   ├── components/
│   └── layouts/
├── assets/
│   ├── fonts/
│   └── images/
└── theme.json
```

### 2. Theme Configuration

```json
{
  "name": "Custom Theme",
  "version": "1.0.0",
  "description": "Custom theme implementation",
  "tokens": {
    "extends": "base",
    "overrides": ["./tokens/*.json"]
  },
  "components": {
    "overrides": ["./overrides/components/*.json"]
  }
}
```

## Token Customization

### 1. Color Tokens

```json
{
  "color": {
    "brand": {
      "primary": { "value": "#0066CC" },
      "secondary": { "value": "#FF4400" }
    },
    "semantic": {
      "success": { "value": "{color.brand.primary}" },
      "error": { "value": "#FF0000" }
    }
  }
}
```

### 2. Typography Tokens

```json
{
  "typography": {
    "family": {
      "base": { "value": "Public Sans" },
      "heading": { "value": "Merriweather" }
    },
    "size": {
      "base": { "value": "16px" },
      "h1": { "value": "2.5rem" }
    }
  }
}
```

## Component Customization

### 1. Component Overrides

```json
{
  "button": {
    "primary": {
      "background": { "value": "{color.brand.primary}" },
      "text": { "value": "{color.neutral.white}" }
    },
    "border": {
      "radius": { "value": "{radius.medium}" }
    }
  }
}
```

### 2. Layout Overrides

```json
{
  "grid": {
    "container": {
      "max-width": { "value": "1200px" },
      "padding": { "value": "{spacing.medium}" }
    }
  }
}
```

## Theme Development

### 1. Local Development

```bash
# Start theme development
npm run theme:dev

# Build theme
npm run theme:build

# Test theme
npm run theme:test
```

### 2. Theme Testing

```php
class ThemeTest extends WP_UnitTestCase {
    public function testTokenOverrides() {
        $theme = new Theme('custom');
        $this->assertEquals('#0066CC', $theme->getToken('color.brand.primary'));
    }
}
```

## Theme Deployment

### 1. Build Process

```bash
# Build theme for production
npm run theme:build:prod

# Package theme
npm run theme:package
```

### 2. Deployment Checklist

- [ ] Validate all token values
- [ ] Test component overrides
- [ ] Check responsive layouts
- [ ] Verify asset paths
- [ ] Create documentation

## Theme Management

### 1. Version Control

```json
{
  "version_history": [
    {
      "version": "1.0.0",
      "changes": ["Initial theme release"],
      "date": "2024-01-20"
    }
  ]
}
```

### 2. Theme Updates

```php
class ThemeManager {
    public function updateTheme(string $name): void {
        $this->backupCurrentTheme();
        $this->applyUpdates();
        $this->validateTheme();
    }
}
```

## Theme API

### 1. Theme Registration

```php
add_action('init', function() {
    uhsds_register_theme([
        'name' => 'Custom Theme',
        'version' => '1.0.0',
        'path' => plugin_dir_path(__FILE__)
    ]);
});
```

### 2. Theme Hooks

```php
// Modify theme tokens
add_filter('uhsds_theme_tokens', function($tokens, $theme_name) {
    // Modify tokens
    return $tokens;
}, 10, 2);

// Theme activation
add_action('uhsds_theme_activated', function($theme_name) {
    // Handle theme activation
});
```

## Best Practices

### 1. Token Organization

- Use semantic naming
- Maintain hierarchy
- Document changes
- Test variations

### 2. Performance

- Optimize assets
- Minimize overrides
- Cache effectively
- Lazy load when possible

## Troubleshooting

### 1. Common Issues

```php
// Check theme status
$status = UHSDS_Theme_Status::get_instance();
$issues = $status->validate_theme('custom');
```

### 2. Debug Mode

```php
// Enable theme debug mode
define('UHSDS_THEME_DEBUG', true);

// Log theme issues
uhsds_theme_log('Debug message', 'debug');
```

---

## Agent Review Notes

### Design Token Agent Review ("The Architect")

1. **Token Inheritance System**

   - Need explicit token override resolution strategy
   - Add token inheritance visualization
   - Implement token conflict detection

   ```json
   {
     "token_inheritance": {
       "strategy": "deep-merge",
       "conflict_resolution": "explicit",
       "visualization": true
     }
   }
   ```

2. **Theme Token Validation**

   - Add semantic validation rules
   - Implement cross-theme token analysis
   - Token usage impact assessment

   ```php
   interface ThemeTokenValidator {
       public function validateSemantics(): ValidationResult;
       public function analyzeTokenImpact(): ImpactReport;
       public function checkCrossThemeConsistency(): void;
   }
   ```

3. **Token Documentation**
   - Generate token relationship graphs
   - Add token usage examples
   - Include migration guides
   ```yaml
   token_docs:
     relationships: true
     examples: true
     migrations: true
     visual_reference: true
   ```

### Component Development Agent Review ("The Craftsperson")

1. **Component Theme Integration**

   - Add component theme preview system
   - Implement theme switching in component playground
   - Better theme override debugging

   ```typescript
   class ComponentThemeManager {
     previewWithTheme(component: string, theme: string): void;
     debugThemeOverrides(component: string): ThemeDebugInfo;
     validateThemeCompatibility(): ValidationResult;
   }
   ```

2. **Theme Testing Framework**

   - Add visual regression across themes
   - Implement theme transition testing
   - Cross-browser theme validation

   ```yaml
   theme_testing:
     visual:
       cross_theme: true
       transitions: true
     compatibility:
       browsers: true
       devices: true
   ```

3. **Development Tools**
   - Theme hot reload system
   - Theme migration assistant
   - Theme validation CLI
   ```bash
   uhsds theme:validate
   uhsds theme:migrate
   uhsds theme:analyze
   ```

### System Integration Agent Review ("The Integrator")

1. **Theme Distribution System**

   - Add theme package management
   - Implement theme dependency resolution
   - Version control integration

   ```php
   interface ThemeDistribution {
       public function packageTheme(string $name): Package;
       public function resolveThemeDependencies(): void;
       public function integrateWithVCS(): void;
   }
   ```

2. **Multi-site Theme Management**

   - Theme synchronization across sites
   - Network-wide theme settings
   - Theme activation hooks

   ```php
   class NetworkThemeManager {
       public function syncThemes(): void;
       public function manageNetworkSettings(): void;
       public function handleActivation(): void;
   }
   ```

3. **Performance Optimization**
   - Theme asset optimization
   - Selective theme loading
   - Theme caching strategy
   ```yaml
   theme_optimization:
     assets:
       bundling: true
       compression: true
     loading:
       selective: true
       lazy: true
     caching:
       strategy: "hierarchical"
       invalidation: "smart"
   ```

### Collective Recommendations

1. **Documentation Enhancement**

   - Add theme migration guides
   - Include theme troubleshooting flows
   - Better theme API documentation

2. **Development Experience**

   - Create theme development toolkit
   - Implement theme testing dashboard
   - Add theme analytics

3. **Next Steps**
   - Enhance theme validation system
   - Improve theme development workflow
   - Create theme management tools
