# EzGridView

A crash-safe, self-aware drop-in replacement for Flutter's `GridView` that automatically handles unbounded constraints in `Column`, `Row`, `Flex`, and nested scroll views.

## Why use EzGridView?

Flutter's `GridView` tries to expand to fill all available space in its scroll direction. When placed inside a parent with **unbounded constraints**, it breaks the layout entirely.

**Common crash scenarios:**

*   Placing a vertical grid inside a `Column`
*   Placing a horizontal grid inside a `Row`
*   Nesting it inside another `ListView`, `CustomScrollView`, or `SingleChildScrollView`
*   Using it inside a `Flex` or unconstrained `Card`

Instead of a graceful degradation, standard Flutter throws fatal exceptions:

*   `"Vertical viewport was given unbounded height."`
*   `"Horizontal viewport was given unbounded width."`
*   `"RenderBox was not laid out: RenderViewport... NEEDS-PAINT"`

**EzGridView** is a defensive wrapper that detects these unbounded constraints *before* they cause damage:

*   **Auto-Detection:** Instantly identifies if it's in a `Column`, `Row`, or other unbounded parent.
*   **Crash Prevention:** Automatically applies a safe, bounded size based on available screen space to ensure the widget renders instead of breaking.
*   **Developer Feedback (Debug Mode):** Displays a **red border** and logs a clear warning identifying the exact parent causing the issue.
*   **Silent Fix (Release Mode):** Applies the fix silently so users never see a red screen of death.
*   **100% Drop-in Parity:** Supports all 5 standard constructors:
    * `EzGridView(...)` (children list)
    * `EzGridView.builder(...)` (on-demand item builder)
    * `EzGridView.count(...)` (fixed cross-axis count)
    * `EzGridView.extent(...)` (max cross-axis extent)
    * `EzGridView.custom(...)` (custom delegates)

## Constructors

### 1. Default `EzGridView(...)`
Takes an explicit list of `children` with a custom `gridDelegate`:
```dart
EzGridView(
  gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 2),
  children: const [
    Card(child: Text('1')),
    Card(child: Text('2')),
  ],
)
```

### 2. `EzGridView.builder(...)`
Builds grid tiles on demand using `itemBuilder`:
```dart
EzGridView.builder(
  gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 3),
  itemCount: 30,
  itemBuilder: (context, index) => Card(child: Text('Item $index')),
)
```

### 3. `EzGridView.count(...)`
Convenience constructor creating a grid with a fixed number of tiles in the cross axis:
```dart
EzGridView.count(
  crossAxisCount: 3,
  children: List.generate(9, (index) => Text('Tile $index')),
)
```

### 4. `EzGridView.extent(...)`
Convenience constructor creating a grid where tiles have a maximum cross-axis extent:
```dart
EzGridView.extent(
  maxCrossAxisExtent: 150,
  children: List.generate(12, (index) => Text('Tile $index')),
)
```

### 5. `EzGridView.custom(...)`
Complete control with custom `gridDelegate` and `childrenDelegate`:
```dart
EzGridView.custom(
  gridDelegate: myCustomGridDelegate,
  childrenDelegate: myCustomChildrenDelegate,
)
```

## API Reference

| Property | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `gridDelegate` | `SliverGridDelegate` | *Required* | Controls layout of grid tiles. |
| `childrenDelegate` | `SliverChildDelegate` | *Required* | Provides child widgets for the grid. |
| `scrollDirection` | `Axis` | `Axis.vertical` | Axis along which the grid scrolls. |
| `reverse` | `bool` | `false` | Whether the grid scrolls in reverse direction. |
| `controller` | `ScrollController?` | `null` | Controls the scroll position. |
| `primary` | `bool?` | `null` | Whether this is the primary scroll view. |
| `physics` | `ScrollPhysics?` | `null` | How the scroll view should respond to user input. |
| `shrinkWrap` | `bool` | `false` | Whether scroll view wraps its contents along scroll axis. |
| `padding` | `EdgeInsetsGeometry?` | `null` | Padding around the grid content. |
| `scrollCacheExtent` | `ScrollCacheExtent?` | `null` | Viewport cache area for pre-rendering offscreen tiles. |
| `semanticChildCount` | `int?` | `null` | Number of children for accessibility indexing. |
| `dragStartBehavior` | `DragStartBehavior` | `start` | How drag start behavior is handled. |
| `keyboardDismissBehavior` | `ScrollViewKeyboardDismissBehavior` | `manual` | How keyboard is dismissed on scroll. |
| `restorationId` | `String?` | `null` | State restoration identifier. |
| `clipBehavior` | `Clip` | `Clip.hardEdge` | Clip behavior for content outside view area. |
| `hitTestBehavior` | `HitTestBehavior` | `opaque` | Hit testing behavior. |
| `showDebugIndicator` | `bool` | `true` | Displays red outline border in debug mode when unbounded. |
| `fallbackWidth` | `double?` | `null` | Custom fallback width for unbounded horizontal layout. |
| `fallbackHeight` | `double?` | `null` | Custom fallback height for unbounded vertical layout. |
| `onUnboundedDetected` | `Function?` | `null` | Diagnostic callback invoked when unbounded parent is caught. |

## Usage Examples

### 1. Safe Inside Column (Crash Prevention)

This normally crashes in Flutter, but `EzGridView` handles it safely:

```dart
Column(
  children: [
    const Text('My Gallery'),
    // No crash! EzGridView detects unbounded height and applies a fallback.
    EzGridView.count(
      crossAxisCount: 3,
      mainAxisSpacing: 8,
      crossAxisSpacing: 8,
      children: List.generate(
        9,
        (index) => Container(
          color: Colors.blue,
          child: Center(child: Text('$index')),
        ),
      ),
    ),
  ],
)
```

### 2. The "Correct" Fix (Best Practice)

While `EzGridView` prevents the crash, providing explicit constraints using `Expanded` or `SizedBox` is best practice:

```dart
Column(
  children: [
    const Text('Header'),
    Expanded(
      child: EzGridView.builder(
        gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(
          crossAxisCount: 2,
        ),
        itemCount: 20,
        itemBuilder: (context, index) => Card(
          child: Center(child: Text('Item $index')),
        ),
      ),
    ),
  ],
)
```

### 3. Custom Diagnostics Callback

Collect telemetry or notify developers of layout issues:

```dart
EzGridView.builder(
  gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 2),
  itemCount: 10,
  itemBuilder: (context, index) => Text('Item $index'),
  onUnboundedDetected: ({
    required bool isWidthUnbounded,
    required bool isHeightUnbounded,
    required String culprit,
  }) {
    logger.warning('EzGridView encountered unbounded layout inside $culprit');
  },
)
```
