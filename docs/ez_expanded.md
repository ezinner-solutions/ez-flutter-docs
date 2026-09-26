# EzExpanded

A defensive, self-aware drop-in replacement for Flutter's `Expanded` that provides true flex-based expansion inside bounded `Row`, `Column`, and `Flex` widgets, while preventing layout crashes from unbounded constraints or invalid parent widget hierarchy.

## Why use EzExpanded?

Developers frequently use `Expanded` to make widgets fill the available space. However, Flutter's `Expanded` widget has strict layout constraints that can break an app with fatal exceptions:

1.  **Must be a direct child of a Flex:** Placing an `Expanded` outside of a `Row`, `Column`, or `Flex` (e.g. inside `Stack`, `Container`, or directly in `Scaffold` body) causes a fatal `ParentDataWidget` exception:
    *   `"Incorrect use of ParentDataWidget. Expanded widgets must be placed inside Flex widgets."`
2.  **Parent Flex must be bounded:** When a `Column` or `Row` is placed inside an unbounded scroll view (`SingleChildScrollView`, `ListView`, etc.), using `Expanded` inside that flex causes a fatal layout exception:
    *   `"RenderFlex children have non-zero flex but incoming height/width constraints are unbounded."`

**EzExpanded** intercepts both error conditions defensively:

*   **True Flex Expansion:** When placed inside a valid, bounded `Row`, `Column`, or `Flex`, it behaves as a native `Expanded` (or `Flexible`), accurately dividing available space according to `flex` factors.
*   **Invalid Parent Protection:** Detects if placed outside a `Flex` and safely renders the child without throwing `ParentDataWidget` errors.
*   **Unbounded Scroll Protection:** Detects if the enclosing `Flex` is inside an unconstrained scrollable and applies a sensible, responsive fallback size (50% screen height/width or custom dimensions).
*   **Developer Diagnostics (Debug Mode):** Highlights problematic usage with a red border and reports a detailed `FlutterError` diagnosing the exact parent culprit (e.g. `SingleChildScrollView`, `RenderStack`).
*   **Silent Fix (Release Mode):** Automatically applies the fallback layout so your users never see a crash or red screen.

---

## API Reference

| Property | Type | Description |
| :--- | :--- | :--- |
| `child` | `Widget` | **Required.** The widget below this widget in the tree. |
| `flex` | `int` | The flex factor to use for this child when inside a bounded `Flex`. Defaults to `1`. |
| `fit` | `FlexFit` | How a flexible child is inscribed into the available space. Defaults to `FlexFit.tight`. |
| `showDebugIndicator` | `bool` | Whether to display a red border indicator in debug mode when unbounded constraints or an invalid parent hierarchy is detected. Defaults to `true`. |
| `fallbackHeight` | `double?` | Custom fallback height to use when unbounded height or an invalid parent is detected. If null, defaults to 50% screen height. |
| `fallbackWidth` | `double?` | Custom fallback width to use when unbounded width or an invalid parent is detected. If null, defaults to 50% screen width. |
| `onUnboundedDetected` | `Function?` | Optional callback invoked with `isInvalidParent`, `isUnbounded`, and `culprit`. |

*See [Expanded](https://api.flutter.dev/flutter/widgets/Expanded-class.html) and [Flexible](https://api.flutter.dev/flutter/widgets/Flexible-class.html) for standard Flutter flex concepts.*

---

## Usage Examples

### 1. Safe Inside SingleChildScrollView (Crash Prevention)

This would normally crash with *"RenderFlex children have non-zero flex but incoming height constraints are unbounded"*, but `EzExpanded` handles it gracefully:

```dart
SingleChildScrollView(
  child: Column(
    children: [
      const Text('Header'),
      // Won't crash! Safely sized with a red debug border and diagnostic logging.
      EzExpanded(
        child: Container(
          color: Colors.blue,
          child: const Center(child: Text('Safe Expansion')),
        ),
      ),
      const Text('Footer'),
    ],
  ),
)
```

### 2. Correct Usage in Bounded Constraints (True Flex Expansion)

When used in a properly constrained context, it behaves exactly like native `Expanded`:

```dart
SizedBox(
  height: 400,
  child: Column(
    children: [
      const Text('Fixed Header'),
      EzExpanded(
        child: Container(
          color: Colors.green,
          child: const Center(child: Text('Expands to Fill Remaining Height')),
        ),
      ),
    ],
  ),
)
```

### 3. Multiple Flex Factors in a Row

Control the proportional width division between multiple children:

```dart
Row(
  children: [
    EzExpanded(
      flex: 2,
      child: Container(color: Colors.red, child: const Text('Takes 2/3 width')),
    ),
    EzExpanded(
      flex: 1,
      child: Container(color: Colors.blue, child: const Text('Takes 1/3 width')),
    ),
  ],
)
```

### 4. Custom Fallback & Telemetry

```dart
EzExpanded(
  fallbackHeight: 250,
  showDebugIndicator: false, // Disables red border in debug mode
  onUnboundedDetected: ({
    required bool isInvalidParent,
    required bool isUnbounded,
    required String? culprit,
  }) {
    AnalyticsService.logLayoutIssue(
      widget: 'EzExpanded',
      culprit: culprit,
      isInvalidParent: isInvalidParent,
      isUnbounded: isUnbounded,
    );
  },
  child: Container(color: Colors.teal),
)
```
