# EzContactCard

A **customizable, composition-based** Flutter contact card widget with built-in **Material 3 design enforcement**, **automatic avatar generation**, and **drop-in ListTile compatibility**.

## Why use EzContactCard?

Building list items or contact cards often results in repetitive, messy boilerplate code. Standard `ListTile` or custom `Row` compositions often suffer from:

*   **Duplicate Layouts:** You constantly rewrite the same `Card` -> `InkWell` -> `Row` -> `Avatar` -> `Column` -> `Text` hierarchy across screens.
*   **Inconsistent Styling:** Without manual decoration, cards look transparent or flat, leading to fragmented card designs across the app.
*   **Avatar Overhead:** Having to manually configure an avatar widget with initials extraction, contrast calculation, and deterministic coloring for every contact card is tedious.
*   **Handling Overflows:** Forgetting to wrap labels in `Expanded` with text ellipsis breaks layouts on narrow screens with long contact names.
*   **ListTile Inflexibility:** Standard `ListTile` is rigid to theme as a modern card and lacks automated avatar generation or modern Material 3 card container tokens.

**EzContactCard** solves these problems with a single, production-ready widget:

*   **Enforced Design Solutions:** Built-in Material 3 card styling variants (`elevated`, `filled`, `outlined`, `none`) that automatically apply surface container colors, borders, and elevation from your app's theme.
*   **Zero-Config Smart Avatar:** Omitting `avatar` automatically instantiates an `EzCircleAvatar` generated directly from `name` with initials and deterministic colors.
*   **Drop-in ListTile Compatibility:** Uses standard Flutter property aliases (`leading`, `trailing`, `title`, `dense`, `enabled`) so you can replace existing `ListTile` or `Card` widgets with zero friction.
*   **Defensive Layout:** Automatically handles text truncation, ensuring your UI never breaks when users have long names or titles.
*   **Accessible by Default:** Automatic screen-reader `Semantics` label calculation provides seamless accessibility out of the box.
*   **Total Styling Control:** Keep opinionated defaults or override any visual property (decoration, borders, shadows, margins, text styles).

---

## API Reference

### Core & Content Properties

| Property | Type | Description |
| :--- | :--- | :--- |
| `name` | `String` | The headline text. Used by `EzCircleAvatar` to generate initials and color when `avatar` is omitted. Defaults to `''`. |
| `subtitle` | `String?` | Optional secondary text displayed below the headline. |
| `avatar` | `Widget?` | The widget on the left. If omitted (and `leading` is null), defaults to auto-generating `EzCircleAvatar(name: name)`. |
| `leading` | `Widget?` | Drop-in alias for `avatar` matching standard `ListTile` naming. |
| `tail` | `Widget?` | Optional action widget displayed on the right edge (e.g., an icon, button, or badge). |
| `trailing` | `Widget?` | Drop-in alias for `tail` matching standard `ListTile` naming. |
| `title` | `Widget?` | Custom widget to replace the default headline `Text(name)` widget. |
| `showAvatar` | `bool` | Whether to display an avatar when `avatar` and `leading` are omitted. Defaults to `true`. |

### Design & Behavior

| Property | Type | Description |
| :--- | :--- | :--- |
| `variant` | `EzContactCardVariant` | The visual design variant: `elevated`, `filled`, `outlined`, or `none`. Defaults to `EzContactCardVariant.elevated`. |
| `dense` | `bool` | Whether this card uses compact padding (`12.0` horizontal, `8.0` vertical) and smaller gaps (`12.0`). Defaults to `false`. |
| `enabled` | `bool` | Whether interaction is enabled. When `false`, gestures are blocked and visual presentation is dimmed. Defaults to `true`. |
| `semanticLabel` | `String?` | Accessibility label for screen readers. If omitted, automatically resolves to `"$name, $subtitle"`. |

### Container Styling & Overrides

| Property | Type | Description |
| :--- | :--- | :--- |
| `variant` | `EzContactCardVariant` | Pre-configured Material 3 card styling. |
| `decoration` | `BoxDecoration?` | Custom decoration behind the card (background, border, radius, shadow). Overrides all variant and shorthand properties. |
| `backgroundColor` | `Color?` | Custom background color. Overrides the variant's default background color. |
| `border` | `Border?` | Custom border. Overrides the variant's default border. |
| `borderRadius` | `BorderRadiusGeometry?` | Custom corner radius. Defaults to `BorderRadius.circular(12.0)`. |
| `elevation` | `double?` | Custom elevation/shadow size. Overrides the variant's default elevation. |
| `margin` | `EdgeInsetsGeometry?` | Empty space surrounding the card container. |
| `contentPadding` | `EdgeInsetsGeometry?` | Padding inside the card. Defaults to `16.0` horizontal, `12.0` vertical (or `12.0`/`8.0` if `dense` is true). |
| `clipBehavior` | `Clip` | Clip behavior for card boundaries. Defaults to `Clip.antiAlias`. |

### Interaction

| Property | Type | Description |
| :--- | :--- | :--- |
| `onTap` | `VoidCallback?` | Callback invoked when the card is tapped. Triggers rounded ink ripple effect. |
| `onLongPress` | `VoidCallback?` | Callback invoked when the card is long-pressed. |
| `splashColor` | `Color?` | Custom color for the ink splash effect. |
| `highlightColor` | `Color?` | Custom color for the ink highlight effect. |
| `hoverColor` | `Color?` | Custom color when hovering over the card. |
| `focusColor` | `Color?` | Custom color when card receives focus. |

### Text Styling & Layout

| Property | Type | Description |
| :--- | :--- | :--- |
| `nameStyle` | `TextStyle?` | Style for headline text. Defaults to `Theme.of(context).textTheme.titleMedium` with `FontWeight.w600`. |
| `subtitleStyle` | `TextStyle?` | Style for subtitle text. Defaults to `Theme.of(context).textTheme.bodyMedium` with `colorScheme.onSurfaceVariant`. |
| `nameMaxLines` | `int` | Maximum lines for headline before ellipsis truncation. Defaults to `1`. |
| `subtitleMaxLines` | `int` | Maximum lines for subtitle before ellipsis truncation. Defaults to `1`. |
| `gap` | `double?` | Gap between avatar, content column, and tail. Defaults to `16.0` (or `12.0` if `dense` is true). |
| `verticalAlignment` | `CrossAxisAlignment` | Vertical alignment of row items. Defaults to `CrossAxisAlignment.center`. |

---

## Usage Examples

### 1. Zero-Config (Auto-Avatar & Default Elevated Card)

Simplest possible usage. Automatically displays an avatar with initials "JD", deterministic background color, rounded corners, and subtle elevation:

```dart
EzContactCard(
  name: 'Jane Doe',
  onTap: () => _openProfile(context),
)
```

### 2. Material 3 Design Variants

Switch between opinionated card design solutions with a single parameter:

```dart
// Elevated variant (Default)
EzContactCard(
  name: 'Elevated Contact',
  subtitle: 'Subtle elevation & surfaceContainerLow',
  variant: EzContactCardVariant.elevated,
)

// Filled variant
EzContactCard(
  name: 'Filled Contact',
  subtitle: 'Flat surfaceContainerHighest background',
  variant: EzContactCardVariant.filled,
)

// Outlined variant
EzContactCard(
  name: 'Outlined Contact',
  subtitle: 'Crisp outlineVariant border with no shadow',
  variant: EzContactCardVariant.outlined,
)
```

### 3. Drop-in ListTile Replacement with Dense Spacing

Migrate existing `ListTile` code effortlessly using familiar property names:

```dart
EzContactCard(
  leading: const Icon(Icons.star, color: Colors.amber),
  name: 'Starred Contact',
  subtitle: 'Team Lead',
  trailing: const Icon(Icons.chevron_right),
  dense: true,
  variant: EzContactCardVariant.outlined,
  onTap: () {},
)
```

### 4. With Action & Custom Avatar

Common pattern for contact lists with a photo and a phone call button:

```dart
EzContactCard(
  name: 'Alice Johnson',
  subtitle: 'Product Manager',
  avatar: EzCircleAvatar(
    name: 'Alice Johnson',
    backgroundImage: NetworkImage('https://example.com/alice.jpg'),
  ),
  tail: IconButton(
    icon: const Icon(Icons.phone),
    onPressed: () => _call(context),
  ),
  onTap: () => _viewProfile(context),
)
```

### 5. Disabled State

Disable interaction and dim the visual representation for inactive or archived contacts:

```dart
EzContactCard(
  name: 'Archived User',
  subtitle: 'Account deactivated',
  enabled: false,
  tail: const Icon(Icons.lock_outline),
  onTap: () {},
)
```

### 6. Fully Styled (Custom Design System)

Match custom brand design requirements while preserving defensive layout:

```dart
EzContactCard(
  name: 'Admin User',
  subtitle: 'System Administrator',
  decoration: BoxDecoration(
    color: Colors.white,
    borderRadius: BorderRadius.circular(16),
    boxShadow: const [
      BoxShadow(
        color: Colors.black12,
        blurRadius: 12,
        offset: Offset(0, 4),
      ),
    ],
    border: Border.all(color: Colors.grey.shade200),
  ),
  contentPadding: const EdgeInsets.all(20),
  nameStyle: const TextStyle(
    fontWeight: FontWeight.bold,
    fontSize: 18,
    color: Colors.indigo,
  ),
  subtitleStyle: TextStyle(
    color: Colors.indigo.shade300,
    fontStyle: FontStyle.italic,
  ),
  gap: 20,
  onTap: () {},
)
```

### 7. Custom Title Widget

Render custom rich widgets in the headline area:

```dart
EzContactCard(
  title: Row(
    children: const [
      Text(
        'Verified User',
        style: TextStyle(fontWeight: FontWeight.bold),
      ),
      SizedBox(width: 6),
      Icon(Icons.verified, size: 16, color: Colors.blue),
    ],
  ),
  subtitle: 'VIP Member',
)
```
