# IDE Technical Implementation

## Overview

The in-browser IDE is built using Monaco Editor (the core of VS Code) with custom extensions for design system integration. This document outlines the technical implementation details.

## Core Technologies

### 1. Editor Stack

```javascript
{
  "core": {
    "editor": "Monaco Editor",
    "version": "0.45.0",
    "features": [
      "syntax highlighting",
      "intellisense",
      "code completion",
      "error detection",
      "formatting"
    ]
  },
  "extensions": {
    "html": "@monaco-editor/react",
    "css": "monaco-css",
    "javascript": "monaco-typescript"
  }
}
```

### 2. Real-Time Preview

```javascript
{
  "preview": {
    "engine": "iframe-sandbox",
    "refreshStrategy": "debounced",
    "debounceTime": 300,
    "injectedDependencies": [
      "design-tokens.css",
      "component-base.css",
      "runtime.js"
    ]
  }
}
```

## Token Integration

### 1. Token Provider Implementation

```typescript
interface TokenProvider {
  getTokens(): DesignToken[];
  getCompletionItems(context: CompletionContext): CompletionItem[];
  resolveToken(token: string): TokenValue;
}

class DesignSystemTokenProvider implements TokenProvider {
  private tokenCache: Map<string, TokenValue>;

  constructor(private tokenService: TokenService) {
    this.tokenCache = new Map();
    this.initializeTokens();
  }

  async initializeTokens() {
    const tokens = await this.tokenService.fetchTokens();
    tokens.forEach((token) => {
      this.tokenCache.set(token.name, token.value);
    });
  }

  getCompletionItems(context: CompletionContext): CompletionItem[] {
    return Array.from(this.tokenCache.entries()).map(([name, value]) => ({
      label: name,
      kind: CompletionItemKind.Variable,
      detail: value.toString(),
      documentation: value.description,
    }));
  }
}
```

### 2. Monaco Configuration

```typescript
// Monaco Editor Configuration
const editorConfig = {
  language: "css",
  theme: "vs-dark",
  automaticLayout: true,
  minimap: { enabled: false },
  suggestOnTriggerCharacters: true,
  quickSuggestions: {
    other: true,
    comments: false,
    strings: true,
  },
  wordBasedSuggestions: false,
};

// Token Provider Registration
monaco.languages.registerCompletionItemProvider("css", {
  triggerCharacters: ["--", "{"],
  provideCompletionItems: (model, position) => {
    const word = model.getWordUntilPosition(position);
    const range = {
      startLineNumber: position.lineNumber,
      endLineNumber: position.lineNumber,
      startColumn: word.startColumn,
      endColumn: word.endColumn,
    };

    return {
      suggestions: tokenProvider.getCompletionItems(range),
    };
  },
});
```

## Component State Management

### 1. State Interface

```typescript
interface ComponentState {
  id: string;
  name: string;
  version: string;
  status: "new" | "draft" | "published" | "archived";
  content: {
    html: string;
    css: string;
    javascript: string;
  };
  metadata: {
    author: string;
    created: Date;
    modified: Date;
    published?: Date;
  };
}

class ComponentStateManager {
  private state: ComponentState;
  private history: ComponentState[];

  constructor(initialState: ComponentState) {
    this.state = initialState;
    this.history = [initialState];
  }

  saveState(newContent: Partial<ComponentState["content"]>) {
    const newState = {
      ...this.state,
      content: { ...this.state.content, ...newContent },
      metadata: {
        ...this.state.metadata,
        modified: new Date(),
      },
    };

    this.state = newState;
    this.history.push(newState);
    this.persistState();
  }
}
```

## Preview Engine

### 1. Sandbox Implementation

```typescript
class PreviewSandbox {
  private frame: HTMLIFrameElement;
  private messageChannel: MessageChannel;

  constructor(container: HTMLElement) {
    this.frame = document.createElement("iframe");
    this.frame.sandbox.add("allow-scripts", "allow-same-origin");
    container.appendChild(this.frame);
    this.setupMessageChannel();
  }

  private setupMessageChannel() {
    this.messageChannel = new MessageChannel();
    this.messageChannel.port1.onmessage = this.handleMessage.bind(this);
  }

  updatePreview(content: ComponentState["content"]) {
    const html = this.createPreviewHTML(content);
    this.frame.contentWindow?.postMessage({ type: "update", html }, "*", [
      this.messageChannel.port2,
    ]);
  }

  private createPreviewHTML(content: ComponentState["content"]): string {
    return `
      <!DOCTYPE html>
      <html>
        <head>
          <style>${content.css}</style>
          <link rel="stylesheet" href="/design-tokens.css">
        </head>
        <body>
          ${content.html}
          <script>${content.javascript}</script>
        </body>
      </html>
    `;
  }
}
```

### 2. Error Handling

```typescript
interface PreviewError {
  type: "html" | "css" | "javascript";
  message: string;
  line?: number;
  column?: number;
}

class ErrorBoundary {
  private errors: PreviewError[] = [];

  catchError(error: Error, errorInfo: any): PreviewError {
    const previewError: PreviewError = {
      type: this.determineErrorType(error),
      message: error.message,
      line: errorInfo?.line,
      column: errorInfo?.column,
    };

    this.errors.push(previewError);
    return previewError;
  }

  private determineErrorType(error: Error): PreviewError["type"] {
    // Error type detection logic
    return "javascript";
  }
}
```

## Performance Optimizations

### 1. Editor Performance

```typescript
interface EditorOptimizations {
  // Debounce configuration for real-time updates
  debounce: {
    preview: number;
    autoComplete: number;
    validation: number;
  };

  // Worker configuration for heavy operations
  workers: {
    validation: Worker;
    formatting: Worker;
    linting: Worker;
  };

  // Cache configuration
  cache: {
    tokens: Map<string, TokenValue>;
    completions: Map<string, CompletionItem[]>;
    validations: Map<string, ValidationResult>;
  };
}
```

### 2. Preview Performance

```typescript
class PreviewOptimizer {
  private lastUpdate: number = 0;
  private updateQueue: ComponentState[] = [];
  private readonly UPDATE_THRESHOLD = 16; // ~60fps

  queueUpdate(state: ComponentState) {
    this.updateQueue.push(state);
    this.processQueue();
  }

  private async processQueue() {
    const now = Date.now();
    if (now - this.lastUpdate < this.UPDATE_THRESHOLD) {
      requestAnimationFrame(() => this.processQueue());
      return;
    }

    const nextState = this.updateQueue.pop();
    if (nextState) {
      this.updateQueue = []; // Clear queue
      await this.updatePreview(nextState);
      this.lastUpdate = Date.now();
    }
  }
}
```

## Integration APIs

### 1. WordPress Integration

```typescript
interface WordPressIntegration {
  // REST API endpoints
  endpoints: {
    save: string;
    load: string;
    publish: string;
    list: string;
  };

  // Nonce handling
  security: {
    nonce: string;
    verify: (response: Response) => boolean;
  };

  // WordPress hooks integration
  hooks: {
    beforeSave: (state: ComponentState) => Promise<void>;
    afterPublish: (state: ComponentState) => Promise<void>;
  };
}
```

### 2. Fusion Builder Integration

```typescript
interface FusionBuilderBridge {
  // Component registration
  register: (component: ComponentState) => void;

  // Live preview handling
  preview: {
    update: (content: string) => void;
    reset: () => void;
  };

  // Property panel integration
  properties: {
    register: (props: PropertyDefinition[]) => void;
    update: (values: PropertyValues) => void;
  };
}
```

## Dynamic Data Integration

### 1. ACF Integration

```typescript
interface ACFIntegration {
  // Field mapping
  fields: {
    getAvailableFields(): Promise<ACFField[]>;
    getFieldValue(fieldKey: string): Promise<any>;
    subscribeToField(fieldKey: string, callback: (value: any) => void): void;
  };

  // Preview support
  preview: {
    mockFieldValue(fieldKey: string, value: any): void;
    resetMocks(): void;
  };

  // Fusion Builder integration
  fusionBuilder: {
    registerDynamicElement(field: ACFField): void;
    getDynamicContent(fieldKey: string): string;
  };
}

interface ACFField {
  key: string;
  name: string;
  type: string;
  label: string;
  value?: any;
  subFields?: ACFField[];
}

// ACF Field Provider Implementation
class ACFFieldProvider {
  async getFieldSuggestions(context: CompletionContext): CompletionItem[] {
    const fields = await this.getAvailableFields();
    return fields.map((field) => ({
      label: field.name,
      kind: CompletionItemKind.Field,
      detail: `ACF Field: ${field.label}`,
      insertText: `{field:${field.key}}`,
      documentation: {
        kind: "markdown",
        value: `**Type:** ${field.type}\n**Key:** ${field.key}`,
      },
    }));
  }
}
```

### 2. Pods Integration

```typescript
interface PodsIntegration {
  // Pod configuration
  pods: {
    getAvailablePods(): Promise<Pod[]>;
    getPodFields(podName: string): Promise<PodField[]>;
    getPodValue(podName: string, fieldName: string): Promise<any>;
  };

  // Template tags
  templates: {
    getTemplateTags(): TemplateTag[];
    parseTemplateTag(tag: string): string;
  };

  // Fusion Builder integration
  fusionBuilder: {
    registerPodElement(pod: Pod): void;
    getPodContent(podName: string, fieldName: string): string;
  };
}

interface Pod {
  name: string;
  label: string;
  fields: PodField[];
}

interface PodField {
  name: string;
  type: string;
  label: string;
  options?: Record<string, any>;
}
```

### 3. Timber/PHP Support

```typescript
interface TimberSupport {
  // Timber template compilation
  compiler: {
    parseTwig(template: string): string;
    validateSyntax(template: string): ValidationResult;
    getContext(): Record<string, any>;
  };

  // PHP integration
  php: {
    validatePHP(code: string): ValidationResult;
    getAvailableFunctions(): PHPFunction[];
    getNamespaces(): string[];
  };

  // Preview rendering
  preview: {
    renderWithContext(template: string, context: any): Promise<string>;
    mockTimberContext(context: any): void;
  };
}

// Timber-specific Monaco language configuration
const timberLanguageConfig = {
  id: "twig",
  extensions: [".twig"],
  aliases: ["Twig", "twig"],
  mimetypes: ["text/x-twig"],
  configuration: {
    comments: {
      lineComment: "{#",
      blockComment: ["{#", "#}"],
    },
    brackets: [
      ["{", "}"],
      ["[", "]"],
      ["(", ")"],
    ],
    autoClosingPairs: [
      { open: "{", close: "}" },
      { open: "[", close: "]" },
      { open: "(", close: ")" },
      { open: '"', close: '"' },
      { open: "'", close: "'" },
    ],
  },
};
```

### 4. Dynamic Value Provider

```typescript
interface DynamicValueProvider {
  // Source registration
  sources: {
    register(source: DataSource): void;
    getAvailableSources(): DataSource[];
  };

  // Value resolution
  values: {
    resolve(path: string): Promise<any>;
    preview(path: string, mockValue: any): void;
  };

  // Fusion Builder integration
  fusionBuilder: {
    registerDynamicSource(source: DataSource): void;
    getDynamicValue(path: string): string;
  };
}

interface DataSource {
  id: string;
  name: string;
  type: "acf" | "pods" | "wp" | "custom";
  getValues(): Promise<Record<string, any>>;
  getCompletionItems(context: CompletionContext): CompletionItem[];
}
```

## IDE Extensions for Dynamic Content

### 1. Dynamic Content Completion Provider

```typescript
class DynamicContentCompletionProvider {
  private sources: DataSource[] = [];

  registerSource(source: DataSource) {
    this.sources.push(source);
  }

  async provideCompletionItems(
    model: editor.ITextModel,
    position: Position
  ): Promise<CompletionList> {
    const wordUntilPosition = model.getWordUntilPosition(position);
    const suggestions: CompletionItem[] = [];

    for (const source of this.sources) {
      const items = await source.getCompletionItems({
        position,
        word: wordUntilPosition,
      });
      suggestions.push(...items);
    }

    return { suggestions };
  }
}
```

### 2. Preview Context Manager

```typescript
class PreviewContextManager {
  private context: Record<string, any> = {};
  private providers: Map<string, DataSource> = new Map();

  async updateContext() {
    for (const [id, provider] of this.providers) {
      const values = await provider.getValues();
      this.context[id] = values;
    }
  }

  getPreviewHTML(template: string): string {
    return `
      <!DOCTYPE html>
      <html>
        <head>
          <style>${this.getDesignTokens()}</style>
        </head>
        <body>
          <div id="preview-root">
            ${this.renderTemplate(template, this.context)}
          </div>
          <script>
            window.previewContext = ${JSON.stringify(this.context)};
          </script>
        </body>
      </html>
    `;
  }
}
```

### 3. Template Syntax Validation

```typescript
interface TemplateValidator {
  // Syntax validation
  validate(template: string): ValidationResult[];

  // Context validation
  validateContext(template: string, context: any): ValidationResult[];

  // Dynamic value validation
  validateDynamicValues(template: string): ValidationResult[];
}

class ValidationResult {
  constructor(
    public readonly severity: "error" | "warning" | "info",
    public readonly message: string,
    public readonly range: Range,
    public readonly source?: string
  ) {}
}
```

### 4. PHP/Timber Editor Configuration

```typescript
const phpEditorConfig = {
  ...editorConfig,
  language: "php",
  phpVersion: "7.4",
  suggestions: {
    enabled: true,
    includeWords: true,
    includeSnippets: true,
  },
  format: {
    enable: true,
  },
  analysis: {
    autoImport: true,
    autoComplete: true,
  },
};

const twigEditorConfig = {
  ...editorConfig,
  language: "twig",
  contextAware: true,
  suggestions: {
    timber: true,
    wordpress: true,
    acf: true,
    pods: true,
  },
};
```

## Dynamic Content Preview System

### 1. Preview Context Management

```typescript
interface PreviewContextManager {
  // Context sources
  sources: {
    acf: ACFPreviewSource;
    pods: PodsPreviewSource;
    wp: WordPressPreviewSource;
    timber: TimberPreviewSource;
  };

  // Mock data management
  mocks: {
    register(path: string, value: any): void;
    clear(): void;
    getMockValue(path: string): any;
  };

  // Context state
  state: {
    current: Record<string, any>;
    history: Array<Record<string, any>>;
    snapshot(): void;
    restore(index: number): void;
  };
}

// Implementation example
class DynamicPreviewManager implements PreviewContextManager {
  private mockData: Map<string, any> = new Map();
  private contextHistory: Array<Record<string, any>> = [];

  async refreshPreview(template: string): Promise<string> {
    const context = await this.buildContext();
    return this.renderWithContext(template, context);
  }

  private async buildContext(): Promise<Record<string, any>> {
    const context = {};

    // Gather ACF fields
    const acfFields = await this.sources.acf.getFields();
    context.acf = this.applyMocks("acf", acfFields);

    // Gather Pods data
    const podsData = await this.sources.pods.getData();
    context.pods = this.applyMocks("pods", podsData);

    // Gather WordPress data
    const wpData = await this.sources.wp.getData();
    context.wp = this.applyMocks("wp", wpData);

    return context;
  }

  private applyMocks(namespace: string, realData: any): any {
    const mocks = Array.from(this.mockData.entries()).filter(([key]) =>
      key.startsWith(namespace)
    );

    return mocks.reduce((data, [key, value]) => {
      set(data, key.slice(namespace.length + 1), value);
      return data;
    }, cloneDeep(realData));
  }
}
```

### 2. Live Preview Updates

```typescript
class LivePreviewEngine {
  private debounceTimeout: number | null = null;
  private readonly DEBOUNCE_MS = 150;

  constructor(
    private previewFrame: HTMLIFrameElement,
    private contextManager: PreviewContextManager
  ) {
    this.setupMessageChannel();
  }

  async updatePreview(changes: ComponentChanges) {
    if (this.debounceTimeout) {
      clearTimeout(this.debounceTimeout);
    }

    this.debounceTimeout = window.setTimeout(async () => {
      const preview = await this.generatePreview(changes);
      this.sendPreviewUpdate(preview);
    }, this.DEBOUNCE_MS);
  }

  private async generatePreview(
    changes: ComponentChanges
  ): Promise<PreviewContent> {
    const context = await this.contextManager.buildContext();

    return {
      html: this.processTemplate(changes.template, context),
      styles: this.processStyles(changes.styles, context),
      scripts: this.processScripts(changes.scripts, context),
      context: context,
    };
  }

  private sendPreviewUpdate(preview: PreviewContent) {
    this.previewFrame.contentWindow?.postMessage(
      {
        type: "preview-update",
        content: preview,
      },
      "*"
    );
  }
}
```

### 3. Dynamic Data Visualization

```typescript
interface DynamicDataVisualizer {
  // Data inspector
  inspector: {
    showValue(path: string): void;
    trackChanges(path: string): void;
    highlightUsage(path: string): void;
  };

  // Visual indicators
  indicators: {
    addDynamicIndicator(element: HTMLElement, source: string): void;
    showLoadingState(element: HTMLElement): void;
    showErrorState(element: HTMLElement, error: Error): void;
  };
}

class PreviewDataVisualizer implements DynamicDataVisualizer {
  private indicators: Map<HTMLElement, Indicator> = new Map();

  highlightDynamicContent() {
    const dynamicElements = this.findDynamicElements();
    dynamicElements.forEach((element) => {
      const source = this.getDynamicSource(element);
      this.indicators.addDynamicIndicator(element, source);
    });
  }

  showDataFlow(path: string) {
    const affectedElements = this.findElementsUsingData(path);
    this.createDataFlowVisualization(affectedElements);
  }
}
```

## Dynamic Content Usage Examples

### 1. Basic ACF Field Usage

```html
<!-- Component Template -->
<div class="uhsds-card">
  <div class="uhsds-card__header">{field:header_image type="image"}</div>
  <div class="uhsds-card__body">
    <h2>{field:title type="text"}</h2>
    <div class="uhsds-card__content">{field:content type="wysiwyg"}</div>
  </div>
  <div class="uhsds-card__footer">{field:cta_button type="link"}</div>
</div>

<!-- Preview Context -->
{ "acf": { "header_image": { "url": "/path/to/image.jpg", "alt": "Header Image"
}, "title": "Dynamic Card Title", "content": "
<p>Rich text content here</p>
", "cta_button": { "url": "#", "title": "Learn More", "target": "_blank" } } }
```

### 2. Pods with Timber Integration

```twig
{# Component Template #}
{% set pod = pods('product') %}

<div class="uhsds-product">
  <div class="uhsds-product__gallery">
    {% for image in pod.field('gallery') %}
      <img src="{{ image.guid }}" alt="{{ image.post_title }}">
    {% endfor %}
  </div>

  <div class="uhsds-product__info">
    <h1>{{ pod.field('name') }}</h1>
    <div class="uhsds-product__price">
      {{ pod.field('price')|currency }}
    </div>

    {% if pod.field('variations') %}
      <div class="uhsds-product__variations">
        {% for variation in pod.field('variations') %}
          {{ include('variation-selector.twig', { variation: variation }) }}
        {% endfor %}
      </div>
    {% endif %}
  </div>
</div>

{# Preview Context #}
{
  "pods": {
    "product": {
      "gallery": [
        {
          "guid": "/path/to/image1.jpg",
          "post_title": "Product Image 1"
        },
        {
          "guid": "/path/to/image2.jpg",
          "post_title": "Product Image 2"
        }
      ],
      "name": "Sample Product",
      "price": 99.99,
      "variations": [
        {
          "name": "Size",
          "options": ["S", "M", "L"]
        },
        {
          "name": "Color",
          "options": ["Red", "Blue", "Green"]
        }
      ]
    }
  }
}
```

### 3. Mixed Dynamic Content Sources

```html
<!-- Component Template -->
<div class="uhsds-team-member">
  <div class="uhsds-team-member__header">
    {field:profile_image type="image"}
    <h3>{field:name type="text"}</h3>
    <div class="uhsds-team-member__title">{pod:employee.position}</div>
  </div>

  <div class="uhsds-team-member__bio">{field:biography type="wysiwyg"}</div>

  <div class="uhsds-team-member__projects">
    <h4>Current Projects</h4>
    {% for project in pods.employee.projects %}
    <div class="uhsds-project-card">
      <h5>{{ project.title }}</h5>
      <p>{{ project.description }}</p>
      <div class="uhsds-project-card__status">Status: {{ project.status }}</div>
    </div>
    {% endfor %}
  </div>
</div>

<!-- Preview Context -->
{ "acf": { "profile_image": { "url": "/path/to/profile.jpg", "alt": "Team Member
Profile" }, "name": "John Doe", "biography": "
<p>Experienced team member...</p>
" }, "pods": { "employee": { "position": "Senior Developer", "projects": [ {
"title": "Project Alpha", "description": "Key initiative...", "status": "In
Progress" }, { "title": "Project Beta", "description": "Secondary project...",
"status": "Planning" } ] } } }
```

### 4. Dynamic Content with Conditional Logic

```html
<!-- Component Template -->
<div class="uhsds-feature-section">
  {% if field:layout == 'grid' %}
  <div class="uhsds-feature-grid">
    {% else %}
    <div class="uhsds-feature-list">
      {% endif %} {% for feature in field:features %}
      <div class="uhsds-feature-item">
        {% if feature.icon %}
        <div class="uhsds-feature-item__icon">
          {field:feature.icon type="image"}
        </div>
        {% endif %}

        <div class="uhsds-feature-item__content">
          <h3>{field:feature.title}</h3>
          {% if feature.description %}
          <p>{field:feature.description}</p>
          {% endif %} {% if feature.link %}
          <a
            href="{field:feature.link.url}"
            class="uhsds-button"
            target="{field:feature.link.target}"
          >
            {field:feature.link.title}
          </a>
          {% endif %}
        </div>
      </div>
      {% endfor %}
    </div>
  </div>

  <!-- Preview Context -->
  { "acf": { "layout": "grid", "features": [ { "icon": { "url":
  "/path/to/icon1.svg", "alt": "Feature 1" }, "title": "Feature One",
  "description": "Description text...", "link": { "url": "#feature-1", "title":
  "Learn More", "target": "_self" } }, { "icon": { "url": "/path/to/icon2.svg",
  "alt": "Feature 2" }, "title": "Feature Two", "description": "Another
  description...", "link": null } ] } }
</div>
```
