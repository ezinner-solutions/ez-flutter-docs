# EzEmailField

A pre-configured, self-validating Flutter text field specifically designed for email input with built-in RFC 5322 regex validation, auto-trimming, mobile autofill, and quick clear support.

## Why use EzEmailField?

Implementing email fields repeatedly across an application involves tedious boilerplate. Standard `TextFormField` requires you to:

*   **Copy-Paste Regex:** Manually maintain email regex patterns across different forms.
*   **Whitespace Bugs:** Handle leading and trailing spaces that mobile keyboards insert via auto-correct or clipboard paste, which cause valid user emails to fail validation.
*   **Missing Autofill:** Manually configure `autofillHints: [AutofillHints.email]` so iOS, Android, and password managers can suggest saved addresses.
*   **Clear Button Boilerplate:** Manage custom state or suffix icon controllers just to let users clear typed text quickly.
*   **Inconsistent UX:** Deal with disparate validation messages and styling across different screens.

**EzEmailField** solves all of this out of the box:

*   **Built-in RFC 5322 Validation:** Comes with an optimized, industry-standard regex for validating email formats automatically.
*   **Auto-trimming:** Automatically trims leading and trailing whitespace on validation and form submission (`autoTrim: true` by default).
*   **Mobile Autofill:** Pre-configured with `[AutofillHints.email]` for instant autofill prompts on mobile devices and browsers.
*   **Quick Clear Button:** Optional `showClearButton: true` displays a suffix icon button that appears when text is entered and clears on tap.
*   **100% Drop-in Parity:** Supports all standard `TextFormField` parameters (`validator`, `initialValue`, `focusNode`, `autovalidateMode`, `enabled`, `onSaved`, `inputFormatters`, `onTap`).
*   **Backwards Compatible:** Includes `EZEmailField` typedef so existing projects continue to compile without code modifications.

---

## API Reference

| Property | Type | Description |
| :--- | :--- | :--- |
| `labelText` | `String` | The label displayed above the field. Defaults to `'Email'`. |
| `hintText` | `String` | Placeholder text shown when the field is empty. Defaults to `'Enter your email address'`. |
| `required` | `bool` | If true, validation ensures the field is not empty. Defaults to `true`. |
| `requiredMessage` | `String?` | Custom error message when the required field is empty. Defaults to `'${labelText} is required.'`. |
| `invalidEmailMessage` | `String?` | Custom error message when the format is invalid. Defaults to `'Please enter a valid email address.'`. |
| `autoTrim` | `bool` | Automatically trims leading and trailing whitespace. Defaults to `true`. |
| `showClearButton` | `bool` | Shows a clear suffix icon button when text is present. Defaults to `false`. |
| `clearIcon` | `Widget?` | Custom icon widget for the clear button. Defaults to `Icon(Icons.clear, size: 20)`. |
| `controller` | `TextEditingController?` | External controller. If null, an internal controller is managed automatically. |
| `initialValue` | `String?` | Initial text value when no external controller is provided. |
| `focusNode` | `FocusNode?` | Focus node to control keyboard focus. |
| `decoration` | `InputDecoration?` | Custom decoration overriding default styling (prefix icon, borders, padding). |
| `validator` | `FormFieldValidator<String>?` | Custom validation function. If provided, replaces default email validation. |
| `customValidator` | `FormFieldValidator<String>?` | Backwards-compatible alias for `validator`. |
| `emailRegex` | `RegExp?` | Custom regex pattern replacing the default RFC 5322 regex. |
| `onChanged` | `ValueChanged<String>?` | Callback invoked whenever text changes. |
| `onSaved` | `FormFieldSetter<String>?` | Callback invoked when the enclosing form is saved via `FormState.save()`. |
| `onFieldSubmitted` | `ValueChanged<String>?` | Callback invoked when the user submits editing on the keyboard. |
| `autovalidateMode` | `AutovalidateMode?` | Controls when validation errors are displayed (e.g. `onUserInteraction`). |
| `autofillHints` | `Iterable<String>?` | Autofill hints for the OS. Defaults to `const [AutofillHints.email]`. |
| `readOnly` | `bool` | If true, prevents editing. Defaults to `false`. |
| `autofocus` | `bool` | Automatically focuses the field on display. Defaults to `false`. |
| `enabled` | `bool?` | Whether the field is enabled for user interaction. |
| `inputFormatters` | `List<TextInputFormatter>?` | Optional formatters applied as the user types. |
| `keyboardType` | `TextInputType?` | Keyboard type. Defaults to `TextInputType.emailAddress`. |
| `textInputAction` | `TextInputAction?` | Keyboard action button. Defaults to `TextInputAction.next`. |
| `textCapitalization` | `TextCapitalization` | Keyboard capitalization. Defaults to `TextCapitalization.none`. |

*See [TextFormField](https://api.flutter.dev/flutter/material/TextFormField-class.html) for all standard inherited properties.*

---

## Usage Examples

### 1. Basic (Zero Config)

Just drop it into a `Form`. It automatically validates email format and required status:

```dart
Form(
  key: _formKey,
  child: Column(
    children: [
      const EzEmailField(),
      ElevatedButton(
        onPressed: () {
          if (_formKey.currentState!.validate()) {
            // Email is valid!
          }
        },
        child: const Text('Submit'),
      ),
    ],
  ),
)
```

### 2. Clear Button & Auto-Validation

Shows a clear button when typing and validates instantly on user interaction:

```dart
EzEmailField(
  labelText: 'Account Email',
  showClearButton: true,
  autovalidateMode: AutovalidateMode.onUserInteraction,
  onSaved: (email) => print('Saved: $email'),
)
```

### 3. Custom Error Messages

Customizing validation strings:

```dart
EzEmailField(
  requiredMessage: 'Email address cannot be empty',
  invalidEmailMessage: 'Please provide a valid email format (e.g. name@domain.com)',
)
```

### 4. Custom Validation (Domain Restrictions)

Override default validation with domain rules:

```dart
EzEmailField(
  labelText: 'Corporate Email',
  validator: (value) {
    if (value == null || !value.endsWith('@company.com')) {
      return 'Must be an @company.com email address';
    }
    return null;
  },
  decoration: const InputDecoration(
    helperText: 'Only employees with @company.com can sign in',
  ),
)
```

### 5. Custom Styling

Override prefix icons, borders, and fill colors via `InputDecoration`:

```dart
EzEmailField(
  labelText: 'Work Email',
  hintText: 'john.doe@company.com',
  decoration: const InputDecoration(
    border: OutlineInputBorder(),
    prefixIcon: Icon(Icons.work_outline),
    filled: true,
  ),
)
```
