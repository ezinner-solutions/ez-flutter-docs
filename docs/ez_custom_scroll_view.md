# EzCustomScrollView

A defensive, crash-safe drop-in replacement for Flutter's `CustomScrollView` that automatically handles unbounded constraints in `Column`, `Row`, `Flex`, and nested viewports.

## Why use EzCustomScrollView?

Flutter's `CustomScrollView` is a powerful widget for building complex scrollable layouts with slivers. However, because scroll views expand to fill available space along their scrolling axis, placing one inside an **unbounded parent** causes a fatal layout error that crashes the app with a red screen:

*   Placing a vertical scroll view inside a `Column` or `Flex` without `Expanded`
*   Placing a horizontal scroll view inside a `Row` or `Flex` without `Expanded`
*   Nesting inside an `UnconstrainedBox` or another unconstrained viewport

In standard Flutter, this produces confusing assertion failures and renders a broken screen:
*   `"Vertical viewport was given unbounded height."`
*   `"Horizontal viewport was given unbounded width."`
*   `"RenderBox was not laid out: RenderViewport... NEEDS-PAINT"`

**EzCustomScrollView** intercepts these constraint violations before they can crash your app:

*   **Crash Prevention:** Automatically detects unbounded dimensions and applies a safe, bounded size (e.g. 50% of available screen height/width or custom dimensions).
*   **Developer Diagnostics (Debug Mode):** Displays a red outline border on the offending widget and logs a structured, actionable error identifying the exact parent widget (e.g., `Column`, `Row`) causing the violation.
*   **Silent Protection (Release Mode):** Automatically applies the fallback size without crashing or displaying borders, ensuring end users never see a broken screen.
*   **100% Drop-in Parity:** Supports all parameters from standard `CustomScrollView` with identical behavior under normal bounded constraints.

---

## API Reference

| Property | Type | Description |
| :--- | :--- | :--- |
| `slivers` | `List<Widget>` | **Required.** The list of slivers inside the scroll view (e.g., `SliverList`, `SliverGrid`, `SliverAppBar`). |
| `scrollDirection` | `Axis` | The scrolling axis. Defaults to `Axis.vertical`. |
| `reverse` | `bool` | Whether the scroll view scrolls in the reverse reading direction. Defaults to `false`. |
| `controller` | `ScrollController?` | Controller to inspect or manipulate the scroll offset. |
| `primary` | `bool?` | Whether this is the primary scroll view associated with the parent `PrimaryScrollController`. |
| `physics` | `ScrollPhysics?` | How the scroll view should respond to user input. |
| `shrinkWrap` | `bool` | Whether the extent in `scrollDirection` should be determined by content size. Defaults to `false`. |
| `center` | `Key?` | The key of the sliver that should be centered in the viewport. |
| `anchor` | `double` | The relative position of the zero scroll offset. Defaults to `0.0`. |
| `scrollCacheExtent` | `double?` | Pixels to cache ahead/behind the visible viewport area (Flutter 3.41+). |
| `cacheExtent` | `double?` | Deprecated viewport cache extent. Falls back to `scrollCacheExtent`. |
| `semanticChildCount` | `int?` | Number of children contributing semantic accessibility information. |
| `dragStartBehavior` | `DragStartBehavior` | Determines how drag start behavior is handled. Defaults to `DragStartBehavior.start`. |
| `keyboardDismissBehavior` | `ScrollViewKeyboardDismissBehavior` | How the keyboard dismisses when scrolling. Defaults to `ScrollViewKeyboardDismissBehavior.manual`. |
| `restorationId` | `String?` | ID to persist and restore scroll position across app restarts. |
| `clipBehavior` | `Clip` | Viewport content clip behavior. Defaults to `Clip.hardEdge`. |
| `hitTestBehavior` | `HitTestBehavior` | How to behave during hit testing. Defaults to `HitTestBehavior.opaque`. |
| `showDebugIndicator` | `bool` | Whether to show a red border in debug mode when unbounded constraints are detected. Defaults to `true`. |
| `fallbackHeight` | `double?` | Custom fallback height when unbounded height is detected. If null, defaults to 50% screen height. |
| `fallbackWidth` | `double?` | Custom fallback width when unbounded width is detected. If null, defaults to 50% screen width. |
| `onUnboundedDetected` | `Function?` | Optional diagnostic callback invoked with `isWidthUnbounded`, `isHeightUnbounded`, and `culprit`. |

---

## Usage Examples

### 1. Safe Inside Column (Crash Prevention)

In standard Flutter, this crashes immediately with *"Vertical viewport was given unbounded height"*. With `EzCustomScrollView`, it safely renders and logs an actionable warning:

```dart
Column(
  children: [
    const Text('Header'),
    EzCustomScrollView(
      slivers: [
        SliverAppBar(
          title: const Text('Safe Sliver App Bar'),
          floating: true,
        ),
        SliverList.builder(
          itemCount: 20,
          itemBuilder: (context, index) => ListTile(title: Text('Item $index')),
        ),
      ],
    ),
  ],
)
```

### 2. Horizontal Scroll Inside Row

Safe inside horizontal flex containers without crashing with *"Horizontal viewport was given unbounded width"*:

```dart
Row(
  children: [
    const Text('Sidebar Label'),
    EzCustomScrollView(
      scrollDirection: Axis.horizontal,
      slivers: [
        SliverList.builder(
          itemCount: 10,
          itemBuilder: (context, index) => Container(
            width: 120,
            margin: const EdgeInsets.all(4),
            color: Colors.amber,
            alignment: Alignment.center,
            child: Text('Card $index'),
          ),
        ),
      ],
    ),
  ],
)
```

### 3. Recommended Production Pattern

While `EzCustomScrollView` prevents crashes during rapid development, the best architectural practice is to provide bounded constraints using `Expanded`:

```dart
Column(
  children: [
    const Text('Header'),
    Expanded(
      child: EzCustomScrollView(
        slivers: [
          SliverGrid.count(
            crossAxisCount: 2,
            children: List.generate(
              50,
              (index) => Card(child: Center(child: Text('$index'))),
            ),
          ),
        ],
      ),
    ),
  ],
)
```

### 4. Custom Fallback Sizing & Diagnostic Telemetry

Customize the fallback dimensions and listen for constraint violations:

```dart
EzCustomScrollView(
  fallbackHeight: 320.0,
  fallbackWidth: 280.0,
  showDebugIndicator: true,
  onUnboundedDetected: ({required isWidthUnbounded, required isHeightUnbounded, required culprit}) {
    debugPrint('Layout alert: Unbounded constraint inside $culprit');
  },
  slivers: [
    SliverToBoxAdapter(
      child: const Text('Custom fallback container'),
    ),
  ],
)
```
