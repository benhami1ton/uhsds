# Development Guide

## Getting Started

### 1. Environment Setup

```bash
# Clone the repository
git clone https://github.com/your-org/uhsds
cd uhsds

# Install dependencies
composer install
npm install

# Build assets
npm run build
```

### 2. WordPress Configuration

```php
// Add to wp-config.php
define('UHSDS_ENV', 'development');
define('UHSDS_DEBUG', true);
```

## Development Workflow

### 1. Component Development

#### Creating a New Component

```php
class MyComponent extends UHSDS_Component_Base {
    public function __construct() {
        parent::__construct();
        $this->name = 'my-component';
        $this->label = 'My Component';
    }

    public function render($args = [], $content = '') {
        return $this->timber->render('component.twig', [
            'args' => $args,
            'content' => $content
        ]);
    }
}
```

#### Component Structure

```
components/my-component/
├── class-my-component.php
├── templates/
│   └── component.twig
├── assets/
│   ├── js/
│   │   └── component.js
│   └── css/
│       └── component.css
└── tests/
    └── test-my-component.php
```

### 2. Token Development

#### Creating Tokens

```json
{
  "color": {
    "brand": {
      "primary": { "value": "#0066CC" }
    }
  }
}
```

#### Building Tokens

```bash
# Build tokens
npm run build:tokens

# Watch for changes
npm run watch:tokens
```

## Testing

### 1. Unit Tests

```bash
# Run PHP unit tests
composer test

# Run JavaScript tests
npm test
```

### 2. Integration Tests

```bash
# Run WordPress integration tests
composer test:integration

# Run browser tests
npm run test:e2e
```

## Code Standards

### 1. PHP Standards

```bash
# Check PHP code standards
composer check-cs

# Fix PHP code standards
composer fix-cs
```

### 2. JavaScript Standards

```bash
# Check JavaScript code standards
npm run lint

# Fix JavaScript code standards
npm run lint:fix
```

## Building for Production

### 1. Production Build

```bash
# Build for production
npm run build:prod

# Generate documentation
npm run docs
```

### 2. Deployment Checklist

- [ ] Run all tests
- [ ] Build production assets
- [ ] Update version numbers
- [ ] Generate documentation
- [ ] Create release notes

## IDE Integration

### 1. Browser IDE

- Access at `/wp-admin/admin.php?page=uhsds-ide`
- Use token autocomplete with `--uhsds-`
- Live preview updates
- Component testing

### 2. VS Code Setup

```json
{
  "editor.formatOnSave": true,
  "php.suggest.basic": false,
  "php.validate.executablePath": "/usr/local/bin/php"
}
```

## Debugging

### 1. PHP Debugging

```php
// Enable debug mode
define('WP_DEBUG', true);
define('WP_DEBUG_LOG', true);
define('WP_DEBUG_DISPLAY', false);

// Debug logging
uhsds_log('Debug message', 'debug');
```

### 2. JavaScript Debugging

```javascript
// Enable debug mode
window.UHSDS_DEBUG = true;

// Debug logging
uhsds.debug("Debug message");
```

## Performance Optimization

### 1. Asset Optimization

```php
// Optimize asset loading
add_filter('uhsds_optimize_assets', function($assets) {
    // Optimize assets
    return $assets;
});
```

### 2. Caching

```php
// Implement caching
add_filter('uhsds_cache_config', function($config) {
    $config['ttl'] = 3600;
    return $config;
});
```

## Security Best Practices

### 1. Input Validation

```php
// Validate input
$value = sanitize_text_field($_POST['value']);
if (!wp_verify_nonce($_POST['nonce'], 'uhsds_action')) {
    wp_die('Invalid nonce');
}
```

### 2. Output Sanitization

```php
// Sanitize output
echo esc_html($value);
echo wp_kses_post($html);
```

## Contributing

### 1. Pull Request Process

1. Fork the repository
2. Create feature branch
3. Commit changes
4. Push to branch
5. Create Pull Request

### 2. Code Review Guidelines

- Follow coding standards
- Include tests
- Update documentation
- Add changelog entry

## Troubleshooting

### 1. Common Issues

```php
// Check system status
$status = UHSDS_System_Status::get_instance();
$issues = $status->check_all();
```

### 2. Error Handling

```php
try {
    // Risky operation
} catch (UHSDS_Exception $e) {
    uhsds_log($e->getMessage(), 'error');
    wp_die($e->getMessage());
}
```

---

## Agent Review Notes

### Design Token Agent Review ("The Architect")

1. **Token Development Workflow**

   - Need explicit validation steps in token build process
   - Missing token versioning and deprecation workflow
   - Add token documentation generation step

   ```json
   {
     "build:tokens": {
       "validate": true,
       "version": "semantic",
       "docs": true,
       "platforms": ["css", "js", "php"]
     }
   }
   ```

2. **Token Testing Requirements**

   - Add token validation tests
   - Include contrast ratio testing
   - Implement token usage analytics

   ```php
   class TokenValidator {
       public function validateConstraints(): void;
       public function checkAccessibility(): void;
       public function analyzeUsage(): array;
   }
   ```

3. **IDE Integration for Tokens**
   - Enhance token autocomplete with metadata
   - Add token preview in IDE
   - Implement token usage hints

### Component Development Agent Review ("The Craftsperson")

1. **Component Development Flow**

   - Add component scaffolding CLI tool
   - Implement component documentation generator
   - Enhanced testing framework needed

   ```bash
   # Proposed CLI commands
   uhsds component:create
   uhsds component:test
   uhsds component:document
   ```

2. **Development Environment**

   - Add hot module replacement
   - Implement component playground
   - Better error boundary handling

   ```javascript
   class DevEnvironment {
       setupHMR();
       initializePlayground();
       configureErrorBoundaries();
   }
   ```

3. **Testing Enhancement**
   - Add visual regression testing
   - Implement accessibility testing
   - Add performance benchmarking
   ```yaml
   testing:
     visual:
       snapshots: true
       diffing: true
     a11y:
       wcag2.1: true
       automated: true
     performance:
       metrics: true
       thresholds: true
   ```

### System Integration Agent Review ("The Integrator")

1. **WordPress Integration**

   - Add multisite setup instructions
   - Include plugin dependency management
   - Better error handling across WordPress hooks

   ```php
   class WordPressIntegration {
       public function setupMultisite(): void;
       public function manageDependencies(): void;
       public function handleHookErrors(): void;
   }
   ```

2. **Build Process**

   - Add integration testing pipeline
   - Implement cross-system validation
   - Enhanced deployment workflow

   ```yaml
   build:
     integration_tests:
       - wordpress
       - fusion_builder
       - acf
     validation:
       - cross_system
       - boundaries
     deployment:
       - staging
       - production
   ```

3. **Security Enhancements**
   - Implement role-based access control
   - Add API authentication flow
   - Enhanced security logging
   ```php
   interface SecurityManager {
       public function validateAccess(): bool;
       public function authenticateAPI(): string;
       public function logSecurityEvent(string $event): void;
   }
   ```

### Collective Recommendations

1. **Documentation**

   - Add interactive examples
   - Include troubleshooting guides
   - Better API references

2. **Development Tools**

   - Create unified CLI tool
   - Implement shared development container
   - Add automated QA tools

3. **Next Steps**
   - Update development environment setup
   - Enhance testing infrastructure
   - Improve documentation tooling

---

## Testing & QA Agent Review Notes ("The Investigator")

### Testing Infrastructure Concerns

1. **Test Coverage Framework**

   ```mermaid
   graph TD
       A[Source Code] -->|Analyze| B[Coverage Analysis]
       B -->|Generate| C[Coverage Report]
       C -->|Identify| D[Coverage Gaps]

       E[Test Types] -->|Unit| F[Component Tests]
       E -->|Integration| G[System Tests]
       E -->|E2E| H[Browser Tests]

       I[Quality Gates] -->|Enforce| J[Coverage Thresholds]
       I -->|Monitor| K[Test Health]
   ```

2. **Test Suite Organization**
   ```yaml
   test_suites:
     unit:
       component:
         - structure_tests
         - rendering_tests
         - state_tests
       token:
         - validation_tests
         - transformation_tests
         - usage_tests
       integration:
         - registration_tests
         - compatibility_tests
         - performance_tests
     e2e:
       user_flows:
         - component_creation
         - theme_customization
         - content_editing
       browser_compatibility:
         - cross_browser
         - responsive_design
         - accessibility
     performance:
       metrics:
         - load_time
         - memory_usage
         - rendering_speed
   ```

### Critical Testing Gaps

1. **Component Testing**

   ```php
   interface ComponentTestSuite {
       // Structure validation
       public function testComponentStructure(): void;

       // Rendering verification
       public function testComponentRendering(): void;

       // State management
       public function testComponentState(): void;

       // Event handling
       public function testComponentEvents(): void;

       // Error scenarios
       public function testErrorBoundaries(): void;
   }
   ```

2. **Integration Testing**

   ```typescript
   interface IntegrationTestSuite {
     // System integration
     testSystemIntegration(): Promise<TestResult>;

     // Cross-component interaction
     testComponentInteraction(): Promise<TestResult>;

     // Theme compatibility
     testThemeCompatibility(): Promise<TestResult>;

     // Plugin compatibility
     testPluginCompatibility(): Promise<TestResult>;
   }
   ```

3. **Performance Testing**
   ```php
   class PerformanceTestSuite {
       // Load testing
       public function testLoadPerformance(): void;

       // Memory profiling
       public function testMemoryUsage(): void;

       // Rendering benchmarks
       public function testRenderingSpeed(): void;

       // Asset optimization
       public function testAssetLoading(): void;
   }
   ```

### Quality Assurance Processes

1. **Automated Testing Pipeline**

   ```yaml
   qa_pipeline:
     stages:
       static_analysis:
         - php_stan
         - eslint
         - stylelint
       unit_testing:
         - phpunit
         - jest
         - mocha
       integration_testing:
         - cypress
         - playwright
         - selenium
       performance_testing:
         - lighthouse
         - webpagetest
         - jmeter
     reporting:
       - test_results
       - coverage_reports
       - performance_metrics
   ```

2. **Quality Gates**
   ```typescript
   interface QualityGate {
     // Coverage thresholds
     validateCoverage(metrics: CoverageMetrics): boolean;

     // Performance thresholds
     validatePerformance(metrics: PerformanceMetrics): boolean;

     // Code quality
     validateCodeQuality(metrics: CodeMetrics): boolean;

     // Security scanning
     validateSecurity(metrics: SecurityMetrics): boolean;
   }
   ```

### Testing Tools Enhancement

1. **Test Data Management**

   ```php
   interface TestDataManager {
       // Data generation
       public function generateTestData(): TestData;

       // Data cleanup
       public function cleanupTestData(): void;

       // State management
       public function manageTestState(): void;

       // Fixtures handling
       public function handleFixtures(): void;
   }
   ```

2. **Test Environment Management**
   ```yaml
   test_environments:
     local:
       setup:
         - docker_compose
         - wp_cli
         - test_data
       configuration:
         - php_settings
         - wp_config
         - test_config
     ci:
       setup:
         - github_actions
         - test_runners
         - dependencies
       configuration:
         - env_variables
         - test_matrix
         - parallel_execution
   ```

### Next Steps

1. **Immediate Actions**

   - Implement comprehensive test suites
   - Set up automated testing pipeline
   - Create test data management system

2. **Short-term Goals**

   - Enhance coverage reporting
   - Implement performance benchmarking
   - Create testing documentation

3. **Long-term Vision**
   - Implement AI-assisted testing
   - Create automated test generation
   - Develop predictive quality analysis
