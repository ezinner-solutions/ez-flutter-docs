# EzListView

A defensive, crash-safe drop-in replacement for Flutter's `ListView` that automatically handles unbounded constraints in `Column`, `Row`, `Flex`, and nested viewports.

## Why use EzListView?

Flutter's `ListView` is the fundamental widget for scrollable linear arrays of widgets. However, because scroll views expand to fill available space along their scrolling axis, placing a `ListView` inside an **unbounded parent** causes a fatal layout error that crashes the app with a red screen:

*   Placing a vertical list inside a `Column` or `Flex` without `Expanded` or `Flexible`
*   Placing a horizontal list inside a `Row` or `Flex` without `Expanded` or `Flexible`
*   Nesting inside an `UnconstrainedBox` or directly inside another scroll view (`CustomScrollView`, `SingleChildScrollView`) without `shrinkWrap: true` or explicit height
*   Using inside an unconstrained `Card` or unpositioned `Stack`

In standard Flutter, this produces confusing assertion failures and renders a broken screen:
*   `"Vertical viewport was given unbounded height."`
*   `"Horizontal viewport was given unbounded width."`
*   `"RenderBox was not laid out: RenderViewport... NEEDS-PAINT"`

**EzListView** intercepts these constraint violations before they can crash your app:

*   **Crash Prevention:** Automatically detects unbounded dimensions and applies a safe, bounded size (50% of available screen height/width or custom dimensions).
*   **Developer Diagnostics (Debug Mode):** Displays a red outline border on the offending widget and logs a structured, actionable error identifying the exact parent widget (e.g., `Column`, `Row`) causing the violation.
*   **Silent Protection (Release Mode):** Automatically applies the fallback size without crashing or displaying borders, ensuring end users never see a broken screen.
*   **100% Drop-in Parity:** Supports all four standard constructors from Flutter's `ListView`:
    *   `EzListView.new` (explicit children list)
    *   `EzListView.builder` (on-demand item creation)
    *   `EzListView.separated` (items with separators)
    *   `EzListView.custom` (custom `SliverChildDelegate`)

---

## Constructors

### 1. `EzListView` (Default)
Takes a static list of child widgets. Ideal for small, fixed collections of items:

```dart
EzListView(
  children: const [
    ListTile(title: Text('Account')),
    ListTile(title: Text('Notifications')),
    ListTile(title: Text('Security')),
  ],
)
```

### 2. `EzListView.builder`
Builds items on demand as they scroll into view. Ideal for long or infinite lists:

```dart
EzListView.builder(
  itemCount: 100,
  itemBuilder: (context, index) => ListTile(
    title: Text('Item $index'),
  ),
)
```

### 3. `EzListView.separated`
Builds items and alternating separators on demand. Ideal for divider-separated lists:

```dart
EzListView.separated(
  itemCount: 20,
  itemBuilder: (context, index) => ListTile(
    title: Text('Contact $index'),
  ),
  separatorBuilder: (context, index) => const Divider(height: 1),
)
```

### 4. `EzListView.custom`
Takes an explicit `SliverChildDelegate` for advanced custom scrolling scenarios:

```dart
EzListView.custom(
  childrenDelegate: SliverChildBuilderDelegate(
    (context, index) => Text('Custom item $index'),
    childCount: 50,
  ),
)
```

---

## API Reference

| Property | Type | Description |
| :--- | :--- | :--- |
| `children` | `List<Widget>` | Used in `EzListView.new`. The list of explicit children. |
| `itemBuilder` | `NullableIndexedWidgetBuilder?` | Used in `.builder` and `.separated`. Callback returning the widget for an index. |
| `itemCount` | `int?` | Number of items in the list. |
| `separatorBuilder` | `IndexedWidgetBuilder` | Used in `.separated`. Callback returning the separator widget. |
| `childrenDelegate` | `SliverChildDelegate` | Delegate that produces children for the list. |
| `scrollDirection` | `Axis` | Scrolling axis (`Axis.vertical` or `Axis.horizontal`). Defaults to `Axis.vertical`. |
| `reverse` | `bool` | Whether to scroll in the reverse direction. Defaults to `false`. |
| `controller` | `ScrollController?` | Controller for scroll offset manipulation and listening. |
| `primary` | `bool?` | Whether this is the primary scroll view for the surrounding route. |
| `physics` | `ScrollPhysics?` | How the scroll view should respond to user touches. |
| `shrinkWrap` | `bool` | Whether the extent in `scrollDirection` should wrap content. Defaults to `false`. |
| `padding` | `EdgeInsetsGeometry?` | Outer padding around the children. |
| `itemExtent` | `double?` | If non-null, forces children to have the given extent in the scroll direction. |
| `prototypeItem` | `Widget?` | If non-null, forces children to have the same extent as this prototype widget. |
| `scrollCacheExtent` | `double?` | Pixels to cache ahead/behind the visible viewport area (Flutter 3.41+). |
| `cacheExtent` | `double?` | Deprecated viewport cache extent. Falls back to `scrollCacheExtent`. |
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

In standard Flutter, this crashes immediately with *"Vertical viewport was given unbounded height"*. With `EzListView`, it safely renders and logs an actionable warning:

```dart
Column(
  children: [
    const Text('User Feed'),
    EzListView.builder(
      itemCount: 20,
      itemBuilder: (context, index) => ListTile(
        title: Text('Post $index'),
        leading: const Icon(Icons.feed),
      ),
    ),
  ],
)
```

### 2. Horizontal List Inside Row

Safe inside horizontal flex containers without crashing with *"Horizontal viewport was given unbounded width"*:

```dart
Row(
  children: [
    const Text('Categories:'),
    EzListView.builder(
      scrollDirection: Axis.horizontal,
      itemCount: 10,
      itemBuilder: (context, index) => Chip(
        label: Text('Tag #$index'),
      ),
    ),
  ],
)
```

### 3. Custom Fallback & Telemetry

Override the default 50% screen fallback and track layout issues in production telemetry:

```dart
EzListView.builder(
  itemCount: 30,
  itemBuilder: (context, index) => Text('Row $index'),
  fallbackHeight: 280,
  showDebugIndicator: false, // Disables red border in debug mode
  onUnboundedDetected: ({
    required bool isWidthUnbounded,
    required bool isHeightUnbounded,
    required String? culprit,
  }) {
    AnalyticsService.logLayoutError(
      widget: 'EzListView',
      culprit: culprit,
      isWidthUnbounded: isWidthUnbounded,
      isHeightUnbounded: isHeightUnbounded,
    );
  },
)
```

---

## Best Practices: Applying the Permanent Fix

While `EzListView` prevents application crashes and keeps the UI responsive, best practice in Flutter is to explicitly constrain scrollables. When `EzListView` flags an issue in debug mode:

```dart
// Option A: Wrap in Expanded or Flexible inside a Column/Row
Column(
  children: [
    const Text('Header'),
    Expanded(
      child: EzListView.builder(...),
    ),
  ],
)

// Option B: Provide explicit dimensions with SizedBox
SizedBox(
  height: 350,
  child: EzListView.builder(...),
)

// Option C: Use shrinkWrap if the list is small and finite
EzListView.builder(
  shrinkWrap: true,
  physics: const NeverScrollableScrollPhysics(),
  ...
)
```
