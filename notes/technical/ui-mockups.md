# UI Mockups & Interaction Patterns

## Component Browser Interface

### 1. Main Component Browser

```
┌─ UHSDS Components ──────────────────────────────────┐
│ ┌─Search─────────┐ ┌─Filter──┐ ┌─Sort──┐ ┌─View──┐ │
│ │Search...       │ │Category ▼│ │Name  ▼│ │Grid  ▼│ │
│ └───────────────┘ └─────────┘ └───────┘ └───────┘ │
│ ┌─Component Card─────────┐ ┌─Component Card───────┐ │
│ │ ┌─Preview───────────┐  │ │ ┌─Preview─────────┐  │ │
│ │ │                   │  │ │ │                 │  │ │
│ │ │                   │  │ │ │                 │  │ │
│ │ └───────────────────┘  │ │ └─────────────────┘  │ │
│ │ Hero Component         │ │ Card Component        │ │
│ │ v1.2.0 • Updated 2d   │ │ v2.0.1 • Updated 1w   │ │
│ │ [Preview] [Edit]      │ │ [Preview] [Edit]      │ │
│ └──────────────────────┘ └────────────────────────┘ │
└──────────────────────────────────────────────────────┘
```

### 2. Component Details View

```
┌─ Hero Component ───────────────────────────────────┐
│ ┌─Preview──────────────────────────────────────┐   │
│ │                                              │   │
│ │                                              │   │
│ │                                              │   │
│ └──────────────────────────────────────────────┘   │
│ ┌─Details─────────────────┐ ┌─Actions──────────┐   │
│ │ Version: 1.2.0         │ │ [Edit]           │   │
│ │ Updated: 2 days ago    │ │ [Preview]        │   │
│ │ Author: Design Team    │ │ [Disable]        │   │
│ └─────────────────────────┘ └─────────────────┘   │
│ ┌─Documentation────────────────────────────────┐   │
│ │ Usage guidelines and examples...             │   │
│ └──────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────┘
```

## Token Management Interface

### 1. Token Explorer

```
┌─ Theme Tokens ────────────────────────────────────┐
│ ┌─Categories─┐ ┌─Token List──────────────────────┐│
│ │ Colors    ▶│ │ Primary                        ││
│ │ Typography │ │ ├─ Main: #007AFF               ││
│ │ Spacing    │ │ ├─ Light: #4DA2FF             ││
│ │ Shadows    │ │ └─ Dark: #0055B3              ││
│ │           │ │                                ││
│ │           │ │ Secondary                      ││
│ │           │ │ ├─ Main: #00C7BE              ││
│ │           │ │ ├─ Light: #4DDDD7             ││
│ │           │ │ └─ Dark: #008B84              ││
│ └───────────┘ └────────────────────────────────┘│
└──────────────────────────────────────────────────┘
```

### 2. Token Editor

```
┌─ Edit Color Token ─────────────────────────────────┐
│ Token: Primary/Main                                │
│ ┌─Color Picker──────────┐ ┌─Preview─────────────┐ │
│ │     [Color Wheel]     │ │ Text Preview        │ │
│ │                       │ │ Button Preview      │ │
│ │ #007AFF              │ │ Background Preview   │ │
│ └───────────────────────┘ └───────────────────────┘│
│ ┌─Token Information─────────────────────────────┐  │
│ │ Used in: 24 components                        │  │
│ │ Dependencies: None                            │  │
│ └────────────────────────────────────────────────┘ │
│ [Cancel]                                [Save]     │
└──────────────────────────────────────────────────────┘
```

## Fusion Builder Integration

### 1. UHSDS Components Tab

```
┌─ Fusion Builder ───────────────────────────────────┐
│ [Elements] [UHSDS Components] [Library] [Templates]│
│ ┌─Component List────────────────────────────────┐  │
│ │ ┌─Category: Content─────┐ ┌─Category: Layout┐ │  │
│ │ │ ● Hero               │ │ ● Grid          │ │  │
│ │ │ ● Card               │ │ ● Container     │ │  │
│ │ │ ● Testimonial        │ │ ● Section       │ │  │
│ │ └─────────────────────┘ └────────────────┘ │  │
│ └────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────┘
```

### 2. Component Options

```
┌─ Hero Component Options ────────────────────────────┐
│ ┌─Content───────────────┐ ┌─Design──────────────┐  │
│ │ Title                 │ │ Background          │  │
│ │ [Input field]         │ │ [Token picker]      │  │
│ │                       │ │                     │  │
│ │ Description          │ │ Typography          │  │
│ │ [Rich text editor]    │ │ [Token picker]      │  │
│ │                       │ │                     │  │
│ │ CTA Button           │ │ Spacing             │  │
│ │ [Input field]         │ │ [Token picker]      │  │
│ └───────────────────────┘ └─────────���───────────┘  │
│ [Cancel]            [Preview]            [Apply]    │
└──────────────────────────────────────────────────────┘
```

## Settings Interface

### 1. Global Settings

```
┌─ UHSDS Settings ───────────────────────────────────┐
│ ┌─Navigation────┐ ┌─Settings Panel───────────────┐ │
│ │ ● General     │ │ GitHub Integration           │ │
│ │ ● GitHub      │ │ ┌─Repository─────────────┐   │ │
│ │ ● Components  │ │ │ URL: [Input field]     │   │ │
│ │ ● Tokens      │ │ │ Branch: [Dropdown]     │   │ │
│ │ ● Cache       │ │ │ Webhook: [Input field] │   │ │
│ │              │ │ └─────────────────────────┘   │ │
│ └──────────────┘ │ [Test Connection] [Save]      │ │
│                  └─────────────────────────────────┘│
└──────────────────────────────────────────────────────┘
```

### 2. Environment Switcher

```
┌─ Environment Selection ────────────────────────────┐
│ Current: Production                               │
│ ┌─Environment Options────────────────────────────┐ │
│ │ ○ Production                                  │ │
│ │   Main branch, Stable components              │ │
│ │                                               │ │
│ │ ○ Staging                                    │ │
│ │   Dev branch, Beta components                 │ │
│ │                                               │ │
│ │ ○ Development                                │ │
│ │   Local branch, Development components        │ │
│ └───────────────────────────────────────────────┘ │
│ [Cancel]                              [Switch]    │
└──────────────────────────────────────────────────┘
```

## Interactive Elements

### 1. Token Picker

```
┌─ Token Picker ─────────────────────────────────────┐
│ ┌─Search──────────┐ ┌─Category Filter────────────┐ │
│ │ Search tokens...│ │ Colors ▼                   │ │
│ └────────────────┘ └──────────────────────────────┘│
│ ┌─Token List──────────────────────────────────────┐│
│ │ ● Primary                                      ││
│ │   └─ [#007AFF] Main                           ││
│ │   └─ [#4DA2FF] Light                          ││
│ │   └─ [#0055B3] Dark                           ││
│ │                                                ││
│ │ ● Secondary                                    ││
│ │   └─ [#00C7BE] Main                           ││
│ └────────────────────────────────────────────────┘│
│ [Cancel]                              [Select]    │
└──────────────────────────────────────────────────┘
```

### 2. Preview Panel

```
┌─ Live Preview ─────────────────────────────────────┐
│ ┌─Device Selection─────┐ ┌─Theme Selection───────┐ │
│ │ [Desktop ▼]          │ │ [Light Mode ▼]        │ │
│ └────────────────────┘ └─────────────────────────┘│
│ ┌─Preview Window────────────────────────────────┐ │
│ │                                              │ │
│ │                                              │ │
│ │                                              │ │
│ │                                              │ │
│ └──────────────────────────────────────────────┘ │
│ ┌─Responsive Controls────────────────────────────┐│
│ │ Width: 1200px  Height: 800px  Scale: 100%    ││
│ └──────────────────────────────────────────────┘│
└──────────────────────────────────────────────────┘
```

## Notification Patterns

### 1. Success Message

```
┌─ Success ───────────────────────────────────────┐
│ ✓ Component successfully updated                │
│                                   [Dismiss ×]   │
└──────────────────────────────────────────────────┘
```

### 2. Error Message

```
┌─ Error ─────────────────────────────────────────┐
│ ⚠ Failed to sync with GitHub repository         │
│ Details: Connection timeout                     │
│                                   [Dismiss ×]   │
└──────────────────────────────────────────────────┘
```

## Loading States

### 1. Component Card Skeleton

```
┌─ Loading Component ─────────────────────────────┐
│ ┌─────────────────┐                            │
│ │                 │                            │
│ │    [Loading]    │                            │
│ │                 │                            │
│ └─────────────────┘                            │
│ ████████                                       │
│ ███████                                        │
└──────────────────────────────────────────────────┘
```

### 2. Progressive Loading

```
┌─ Loading Content ───────────────────────────────┐
│ [●●●] Loading components...                     │
│ └─Progress: 60%─────────────────────────────┘   │
│                                                 │
│ Component 1 ✓                                   │
│ Component 2 ✓                                   │
│ Component 3 ⋯                                   │
└──────────────────────────────────────────────────┘
```
