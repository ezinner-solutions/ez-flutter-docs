# EzPasswordField

A secure, developer-friendly password field with built-in visibility toggle, smart validation, and full `TextFormField` drop-in parity.

## Why use EzPasswordField?

Implementing password fields typically involves repetitive boilerplate:

*   **Manual Toggling:** You have to manually manage `obscureText` state and add an `IconButton` for visibility every time.
*   **Complex Validation:** Writing regex for "at least 8 characters, one uppercase, one digit..." is tedious and error-prone.
*   **Poor UX:** Showing errors one by one (e.g., "Too short" -> fix -> "Need uppercase" -> fix -> "Need digit") frustrates users.
*   **Missing Defaults:** Forgetting autofill hints (`AutofillHints.password`), prefix icons, or empty-field validation.
*   **Inconsistent Rules:** Different parts of your app might enforce slightly different password policies.

**EzPasswordField** solves these problems:

*   **Built-in Visibility:** Comes with a working eye icon out of the box with toggle animations and customizable icons.
*   **Smart Validation:** Accumulates all missing requirements into a single, clear message (e.g., *"Issues: no spaces, at least 8 characters, a digit"*).
*   **Robustness Flags:** Easily prohibit specific characters like spaces or dashes with simple boolean flags (great for PINs or strict credentials).
*   **Full Parity:** Supports `initialValue`, `focusNode`, `autovalidateMode`, `onSaved`, `onFieldSubmitted`, `inputFormatters`, `autofillHints`, and all standard form field properties.
*   **Zero Config:** Defaults to strong security practices (min length 8, uppercase, lowercase, digits, special chars) and modern Material 3 styling.

## API Reference

| Property | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `controller` | `TextEditingController?` | `null` | Controls the text being edited. |
| `initialValue` | `String?` | `null` | Initial text value when no `controller` is provided. |
| `focusNode` | `FocusNode?` | `null` | Defines the keyboard focus for this widget. |
| `labelText` | `String?` | `'Password'` | Label text for the input decoration. |
| `hintText` | `String?` | `null` | Hint text suggesting accepted input. |
| `required` | `bool` | `true` | Whether the field is required (cannot be empty). |
| `requiredMessage` | `String?` | `null` | Custom message when required field is empty (defaults to `'$labelText is required'`). |
| `minLength` | `int` | `8` | Minimum required length for the password. |
| `requireUppercase` | `bool` | `true` | Whether to require at least one uppercase letter. |
| `requireLowercase` | `bool` | `true` | Whether to require at least one lowercase letter. |
| `requireDigits` | `bool` | `true` | Whether to require at least one digit. |
| `requireSpecialChars` | `bool` | `true` | Whether to require at least one special character. |
| `prohibitSpaces` | `bool` | `true` | Whether to prohibit spaces. |
| `prohibitDashes` | `bool` | `false` | Whether to prohibit dashes. |
| `prohibitLetters` | `bool` | `false` | Whether to prohibit letters. |
| `prohibitDigits` | `bool` | `false` | Whether to prohibit digits. |
| `prohibitSpecialChars` | `bool` | `false` | Whether to prohibit special characters. |
| `issuesPrefix` | `String` | `'Issues: '` | Prefix prepended to the list of missing requirements. |
| `showVisibilityToggle` | `bool` | `true` | Whether to show the visibility toggle icon button. |
| `initiallyObscure` | `bool` | `true` | Whether the text field is initially obscured. |
| `visibilityIcon` | `Widget?` | `null` | Custom icon when password is obscured (`Icons.visibility`). |
| `visibilityOffIcon` | `Widget?` | `null` | Custom icon when password is visible (`Icons.visibility_off`). |
| `onVisibilityChanged` | `ValueChanged<bool>?` | `null` | Callback invoked when visibility is toggled. |
| `validator` | `FormFieldValidator<String>?` | `null` | Additional custom validation logic. Merged with built-in validation. |
| `customValidator` | `FormFieldValidator<String>?` | `null` | Alias for `validator`. |
| `onChanged` | `ValueChanged<String>?` | `null` | Called when the user modifies the text value. |
| `onSaved` | `FormFieldSetter<String>?` | `null` | Invoked when the enclosing form is saved via `FormState.save`. |
| `onFieldSubmitted` | `ValueChanged<String>?` | `null` | Invoked when the user submits editing on the keyboard action button. |
| `onEditingComplete` | `VoidCallback?` | `null` | Invoked when editing is completed. |
| `decoration` | `InputDecoration?` | `null` | Decoration to style the text field (merges with defaults). |
| `autovalidateMode` | `AutovalidateMode?` | `null` | Determines the autovalidation trigger mode. |
| `enabled` | `bool?` | `null` | If `false`, disables the field and toggle button. |
| `readOnly` | `bool` | `false` | Whether the text can be edited. |
| `autofocus` | `bool` | `false` | Whether the field automatically acquires focus. |
| `textInputAction` | `TextInputAction?` | `TextInputAction.done` | Keyboard action button type. |
| `keyboardType` | `TextInputType?` | `TextInputType.visiblePassword` | Type of keyboard to use. |
| `autofillHints` | `Iterable<String>?` | `[AutofillHints.password]` | Autofill hints for mobile/browser password managers. |
| `inputFormatters` | `List<TextInputFormatter>?` | `null` | Optional input formatters to apply as the user types. |

## Usage Examples

### 1. Basic Form Integration

The simplest use case. Drop it in a `Form` to get a secure password field with visibility toggle and validation:

```dart
EzPasswordField(
  onSaved: (val) => _password = val,
)
```

### 2. PIN Mode (Digits Only)

Restrict input to digits only for PINs. Use `prohibitLetters` and `prohibitSpecialChars` to enforce the format:

```dart
EzPasswordField(
  labelText: 'PIN',
  hintText: 'Enter 4-6 digits',
  minLength: 4,
  requireUppercase: false,
  requireLowercase: false,
  requireSpecialChars: false,
  requireDigits: true,
  // Prohibitions
  prohibitLetters: true,
  prohibitSpecialChars: true,
  prohibitSpaces: true,
  prohibitDashes: true,
  keyboardType: TextInputType.number,
)
```

### 3. Prohibit Spaces and Dashes

Prevent users from entering whitespace or dashes in their credentials:

```dart
EzPasswordField(
  labelText: 'Account Password',
  prohibitSpaces: true,
  prohibitDashes: true,
)
```

### 4. Custom Validation Rules

Add your own custom validation on top of the built-in rules:

```dart
EzPasswordField(
  validator: (value) {
    if (value != null && value.toLowerCase().contains('password')) {
      return 'Password cannot contain the word "password"';
    }
    return null;
  },
)
```

### 5. Custom Styling and Icons

Style it with custom prefix/suffix icons and colors:

```dart
EzPasswordField(
  visibilityIcon: const Icon(Icons.lock_open_rounded),
  visibilityOffIcon: const Icon(Icons.lock_rounded),
  decoration: InputDecoration(
    labelText: 'Master Password',
    border: const OutlineInputBorder(
      borderRadius: BorderRadius.all(Radius.circular(12)),
    ),
    filled: true,
    fillColor: Colors.grey.shade50,
  ),
)
```
