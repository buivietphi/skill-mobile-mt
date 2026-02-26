# UI/UX Mobile — Screen Design & Implementation

> Use when: "create screen X", "build UI for Y", "design this layout", "demo screen".
> Covers: screen templates, design tokens, component patterns, dark mode, animations, accessibility.

---

## Design Tokens (Setup First)

### React Native (with StyleSheet or styled-components)

```typescript
// theme/tokens.ts
export const colors = {
  primary: '#007AFF',       // iOS blue / customize per brand
  secondary: '#5856D6',
  background: '#FFFFFF',
  surface: '#F2F2F7',
  text: '#000000',
  textSecondary: '#8E8E93',
  error: '#FF3B30',
  success: '#34C759',
  border: '#E5E5EA',
  // Dark mode
  dark: {
    background: '#000000',
    surface: '#1C1C1E',
    text: '#FFFFFF',
    textSecondary: '#8E8E93',
    border: '#38383A',
  },
};

export const spacing = {
  xs: 4, sm: 8, md: 16, lg: 24, xl: 32, xxl: 48,
};

export const radius = {
  sm: 8, md: 12, lg: 16, xl: 24, full: 9999,
};

export const fontSize = {
  caption: 12, body: 16, title: 20, heading: 28, hero: 34,
};

export const shadow = {
  sm: { shadowOffset: { width: 0, height: 1 }, shadowOpacity: 0.1, shadowRadius: 2, elevation: 2 },
  md: { shadowOffset: { width: 0, height: 4 }, shadowOpacity: 0.15, shadowRadius: 8, elevation: 4 },
  lg: { shadowOffset: { width: 0, height: 8 }, shadowOpacity: 0.2, shadowRadius: 16, elevation: 8 },
};
```

### Flutter

```dart
// theme/app_theme.dart
class AppTheme {
  static ThemeData light() => ThemeData(
    colorScheme: ColorScheme.fromSeed(seedColor: const Color(0xFF007AFF)),
    useMaterial3: true,
    textTheme: const TextTheme(
      headlineLarge: TextStyle(fontSize: 34, fontWeight: FontWeight.bold),
      titleLarge: TextStyle(fontSize: 20, fontWeight: FontWeight.w600),
      bodyLarge: TextStyle(fontSize: 16),
      bodySmall: TextStyle(fontSize: 12, color: Color(0xFF8E8E93)),
    ),
  );

  static ThemeData dark() => ThemeData(
    colorScheme: ColorScheme.fromSeed(
      seedColor: const Color(0xFF007AFF),
      brightness: Brightness.dark,
    ),
    useMaterial3: true,
  );
}
```

---

## Screen Templates

### 1. Login Screen

```
┌─────────────────────────┐
│                         │
│         [Logo]          │
│     App Name / Tagline  │
│                         │
│  ┌───────────────────┐  │
│  │ Email             │  │
│  └───────────────────┘  │
│  ┌───────────────────┐  │
│  │ Password      👁  │  │
│  └───────────────────┘  │
│                         │
│  [  Forgot password?  ] │
│                         │
│  ┌───────────────────┐  │
│  │     Sign In       │  │  ← Primary CTA (full width)
│  └───────────────────┘  │
│                         │
│  ── or continue with ── │
│  [Google] [Apple] [FB]  │  ← Social login row
│                         │
│  Don't have account?    │
│  [Sign Up]              │
└─────────────────────────┘
```

**Key rules:**
- Logo + tagline at top (not center-screen — keyboard pushes up)
- `KeyboardAvoidingView` (RN) or `SingleChildScrollView` (Flutter)
- Password toggle (show/hide)
- Primary button = full width, prominent color
- Social login below divider
- Register link at bottom
- Wrap in `ScrollView` so content doesn't get cut by keyboard

### 2. Home / Feed Screen

```
┌─────────────────────────┐
│  [☰]   Home    [🔔][👤]│  ← Header: menu, title, actions
├─────────────────────────┤
│  ┌───────────────────┐  │
│  │ 🔍 Search...      │  │  ← Search bar (optional)
│  └───────────────────┘  │
│                         │
│  [Filter chips scrollH] │  ← Horizontal scroll filter
│                         │
│  ┌───────────────────┐  │
│  │  Card 1           │  │
│  │  Image + Title    │  │  ← FlatList / ListView items
│  │  Subtitle + Meta  │  │
│  └───────────────────┘  │
│  ┌───────────────────┐  │
│  │  Card 2           │  │
│  └───────────────────┘  │
│         ...             │
├─────────────────────────┤
│  [🏠] [🔍] [➕] [💬] [👤]│  ← Bottom tab bar
└─────────────────────────┘
```

**Key rules:**
- `FlatList` (RN) / `ListView.builder` (Flutter) — NEVER `ScrollView` for dynamic lists
- Pull-to-refresh
- Empty state, loading skeleton, error state
- Pagination (infinite scroll or load more button)
- `key` / `Key` on each list item
- Header collapses on scroll (optional)

### 3. Detail Screen

```
┌─────────────────────────┐
│  [←]   Detail    [⋮]   │  ← Back button + overflow menu
├─────────────────────────┤
│  ┌───────────────────┐  │
│  │                   │  │
│  │   Hero Image      │  │  ← Full width, 16:9 or cover
│  │                   │  │
│  └───────────────────┘  │
│                         │
│  Title (heading)        │
│  Subtitle / category    │
│  ★★★★☆  4.2 (128)      │  ← Rating row
│                         │
│  Description text here  │
│  that can be multiple   │
│  lines long...          │
│                         │
│  ── Related ──────────  │
│  [Card] [Card] [Card]→  │  ← Horizontal scroll
│                         │
├─────────────────────────┤
│  [$29.99]  [Add to Cart]│  ← Sticky bottom CTA
└─────────────────────────┘
```

**Key rules:**
- `ScrollView` OK here (single item, not list)
- Hero image with `aspectRatio`
- Sticky bottom bar for CTA (outside scroll)
- Safe area padding at bottom
- Share / bookmark in header actions

### 4. Profile / Settings Screen

```
┌─────────────────────────┐
│  [←]   Profile   [Edit] │
├─────────────────────────┤
│       ┌─────┐           │
│       │ 👤  │           │  ← Avatar (circular)
│       └─────┘           │
│     John Doe            │
│     john@email.com      │
│                         │
│  ┌───────────────────┐  │
│  │ 📱 Account        > │  │
│  ├───────────────────┤  │
│  │ 🔔 Notifications  > │  │  ← Section list with chevrons
│  ├───────────────────┤  │
│  │ 🎨 Appearance     > │  │
│  ├───────────────────┤  │
│  │ 🔒 Privacy        > │  │
│  └───────────────────┘  │
│                         │
│  ┌───────────────────┐  │
│  │ 📄 Terms           > │  │
│  ├───────────────────┤  │
│  │ ❓ Help            > │  │
│  └───────────────────┘  │
│                         │
│  [     Sign Out       ] │  ← Destructive action (red)
│                         │
│  App v2.1.0             │  ← Version at bottom
└─────────────────────────┘
```

### 5. Onboarding (Swipeable)

```
┌─────────────────────────┐
│                  [Skip] │
│                         │
│     ┌───────────┐       │
│     │ Illustration │    │
│     └───────────┘       │
│                         │
│    Welcome to AppName   │
│    Short description    │
│    of this feature.     │
│                         │
│       ● ○ ○             │  ← Page indicator dots
│                         │
│  ┌───────────────────┐  │
│  │    Get Started     │  │  ← Last page: primary CTA
│  └───────────────────┘  │
└─────────────────────────┘
```

**Key rules:**
- 3-5 pages max
- Skip button always visible
- Last page = CTA (no Next button)
- Page indicator dots
- `PagerView` (RN) / `PageView` (Flutter)
- Store `hasSeenOnboarding` in AsyncStorage/SharedPreferences

---

## Component Patterns

### Bottom Sheet

```typescript
// RN: @gorhom/bottom-sheet
<BottomSheet snapPoints={['25%', '50%', '90%']}>
  <BottomSheetView>{/* content */}</BottomSheetView>
</BottomSheet>

// Flutter: showModalBottomSheet
showModalBottomSheet(
  context: context,
  isScrollControlled: true, // for tall content
  builder: (context) => DraggableScrollableSheet(...),
);
```

### Empty State

```
┌─────────────────────────┐
│                         │
│     [Illustration]      │
│                         │
│    No items yet         │  ← Clear title
│    Add your first item  │  ← Helpful subtitle
│    to get started.      │
│                         │
│  [  + Add Item  ]       │  ← Action button
│                         │
└─────────────────────────┘
```

**Rule:** Every list screen MUST have an empty state. Never show blank screen.

### Loading Skeleton

```typescript
// RN: react-native-skeleton-placeholder
// Flutter: shimmer package
// Rule: skeleton shape matches final UI layout
// Show 3-5 skeleton items, then replace with real data
```

### Toast / Snackbar

```
Position: bottom (Android) or top (iOS)
Duration: 3 seconds (info), 5 seconds (error), persistent (action required)
Actions: max 1 button ("Undo", "Retry")
Never: block UI or require dismiss for non-critical info
```

---

## Touch Targets

| Platform | Minimum | Recommended |
|----------|---------|-------------|
| iOS (HIG) | 44×44 pt | 48×48 pt |
| Android (Material) | 48×48 dp | 48×48 dp |
| Between targets | 8 dp minimum gap | |

**Rule:** Never make a tappable element smaller than 44pt/48dp. Use `hitSlop` (RN) if visual size must be smaller.

---

## Dark Mode Implementation

```typescript
// RN: useColorScheme() hook
const scheme = useColorScheme(); // 'light' | 'dark'
const bg = scheme === 'dark' ? colors.dark.background : colors.background;

// Flutter: Theme.of(context).brightness
final isDark = Theme.of(context).brightness == Brightness.dark;
```

**Rules:**
- NEVER hardcode colors — always use theme tokens
- Test: white text on dark bg, dark text on light bg
- Images: add dark variants or use opacity overlay
- Shadows: reduce in dark mode (barely visible on dark)
- Borders: lighter in dark mode (#38383A not #E5E5EA)

---

## Animation Guidelines

| Animation | Duration | Easing |
|-----------|----------|--------|
| Button press | 100ms | ease-out |
| Screen transition | 300ms | ease-in-out |
| Bottom sheet open | 250ms | spring (damping 0.8) |
| Fade in content | 200ms | ease-in |
| List item appear | 150ms stagger | ease-out |

**Rules:**
- `useNativeDriver: true` (RN) — always for transforms and opacity
- 60 FPS target — no layout animations on main thread
- Reduce motion: respect `AccessibilityInfo.isReduceMotionEnabled` (RN) / `MediaQuery.disableAnimations` (Flutter)
- No animation > 500ms (feels sluggish)

---

## Accessibility Checklist

| Check | RN | Flutter |
|-------|-----|---------|
| Screen reader label | `accessibilityLabel` | `Semantics(label:)` |
| Button role | `accessibilityRole="button"` | `Semantics(button: true)` |
| Image alt text | `accessible={true} accessibilityLabel` | `Semantics(image: true, label:)` |
| Focus order | `accessibilityElementsHidden` | `ExcludeSemantics` |
| Color contrast | 4.5:1 (text), 3:1 (large) | Same ratios |
| Font scaling | Support Dynamic Type | `MediaQuery.textScaleFactor` |

**Rules:**
- Every interactive element needs a label
- Every image needs alt text (or `decorative` flag)
- Never rely on color alone to convey meaning
- Test with VoiceOver (iOS) and TalkBack (Android)

---

## Screen Sizing Reference

| Device | Width (pt/dp) | Safe Area Top | Safe Area Bottom |
|--------|--------------|---------------|-----------------|
| iPhone SE | 375 | 20 | 0 |
| iPhone 15 | 393 | 59 | 34 |
| iPhone 15 Pro Max | 430 | 59 | 34 |
| Android small | 360 | 24 (status) | 48 (nav bar) |
| Android large | 412 | 24 | 48 |
| iPad | 768-1024 | 24 | 20 |

**Rules:**
- Always use `SafeAreaView` (RN) / `SafeArea` (Flutter)
- Design for 375pt width (smallest common), scale up
- Test on smallest AND largest device
- Scrollable content — never assume fixed heights

---

> Screen design: tokens first, 4 states always (loading/error/empty/success),
> platform touch targets, dark mode via theme, accessibility labels on everything.
