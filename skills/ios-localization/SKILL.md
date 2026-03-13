---
name: ios-localization
description: Extract and localize user-facing strings in Swift and SwiftUI projects. Use this skill when the user wants to internationalize their iOS app, localize hardcoded strings, create Localizable.strings files, or translate app content into multiple languages. Trigger when the user mentions localization, NSLocalizedString, Localizable.strings, or wants to make their app support multiple languages.
license: Proprietary
---

# iOS Localization Guide

## Overview

This skill helps you extract hardcoded user-facing strings from Swift and SwiftUI code and replace them with proper localization keys. It generates translations for 12 languages and maintains consistent key naming conventions.

## Supported Languages

The skill generates translations for:
- English (en)
- Chinese Simplified (zh-Hans)
- Chinese Traditional (zh-Hant)
- Japanese (ja)
- Korean (ko)
- Spanish (es)
- Hindi (hi)
- Arabic (ar)
- French (fr)
- Portuguese (pt)
- Russian (ru)
- German (de)

## Workflow

### Step 1: Scan Project Files

Scan all `.swift` and `.storyboard` files in the project to identify hardcoded strings.

```bash
# Find all Swift files
find . -name "*.swift" -type f

# Find all Storyboard files
find . -name "*.storyboard" -type f
```

### Step 2: Identify Localizable Strings

Look for hardcoded strings that should be localized:

**In Swift/SwiftUI:**
- Text("Hello World") in SwiftUI views
- UILabel text assignments: `label.text = "Welcome"`
- Button titles: `button.setTitle("Submit", for: .normal)`
- Alert messages and titles
- Navigation titles
- Placeholder text

**In Storyboards:**
- Label text attributes
- Button titles
- Navigation item titles
- Tab bar item titles

### Step 3: Generate Localization Keys

Create descriptive, consistent keys following this pattern:

```
<screen_or_feature>_<element_type>_<purpose>
```

Examples:
- `home_welcome_title` - Welcome title on home screen
- `login_button_submit` - Submit button on login screen
- `profile_label_username` - Username label on profile
- `error_message_network` - Network error message

### Step 4: Replace Hardcoded Strings

**SwiftUI:**
```swift
// Before
Text("Welcome to the App")

// After
Text(NSLocalizedString("home_welcome_message", comment: "Welcome message on home screen"))
```

**UIKit:**
```swift
// Before
label.text = "Enter your email"

// After
label.text = NSLocalizedString("login_label_email", comment: "Email input label")
```

**SwiftUI with key extension:**
```swift
// Create a helper extension
extension String {
    var localized: String {
        return NSLocalizedString(self, comment: "")
    }
}

// Usage
Text("home_welcome_message".localized)
```

### Step 5: Create Localizable.strings

Generate the `Localizable.strings` file for each language:

```
en.lproj/Localizable.strings
zh-Hans.lproj/Localizable.strings
zh-Hant.lproj/Localizable.strings
ja.lproj/Localizable.strings
...
```

Format:
```
/* Comment describing context */
"key_name" = "Translated Text";
```

Example:
```
/* Welcome message on home screen */
"home_welcome_message" = "Welcome to the App";

/* Submit button on login screen */
"login_button_submit" = "Submit";
```

### Step 6: Handle Format Specifiers

Preserve formatting placeholders in translations:

```swift
// Code
let message = String(format: NSLocalizedString("greeting_user", comment: ""), username)

// Localizable.strings
"greeting_user" = "Hello, %@!";
"items_count" = "You have %d items";
"progress_format" = "%.1f%% complete";
```

## Rules for String Detection

### DO Localize:
- User-visible UI text (labels, buttons, titles)
- Alert and error messages shown to users
- Placeholder text in text fields
- Navigation and tab bar titles
- Confirmation dialogs
- Empty state messages

### DO NOT Localize:
- Debug logs and print statements
- Internal error messages not shown to users
- Variable names and keys
- URLs and API endpoints
- Technical identifiers
- Strings that are only variable names

## Language-Specific Considerations

### Arabic (ar)
- Right-to-left (RTL) layout
- Text expansion: translations may be 20-30% shorter
- Special characters may require different fonts

### Japanese (ja) / Chinese (zh-Hans/zh-Hant) / Korean (ko)
- No spaces between words
- Text may be more compact
- Consider vertical layout options

### German (de)
- Text expansion: translations may be 20-30% longer
- Compound words are common

### Hindi (hi)
- Requires Devanagari script support
- Verify font rendering

## Example Output Structure

```
Project/
├── en.lproj/
│   └── Localizable.strings
├── zh-Hans.lproj/
│   └── Localizable.strings
├── zh-Hant.lproj/
│   └── Localizable.strings
├── ja.lproj/
│   └── Localizable.strings
├── ko.lproj/
│   └── Localizable.strings
├── es.lproj/
│   └── Localizable.strings
├── hi.lproj/
│   └── Localizable.strings
├── ar.lproj/
│   └── Localizable.strings
├── fr.lproj/
│   └── Localizable.strings
├── pt.lproj/
│   └── Localizable.strings
├── ru.lproj/
│   └── Localizable.strings
├── de.lproj/
│   └── Localizable.strings
└── Source/
    └── Views/
        └── UpdatedSwiftFiles.swift
```

## Best Practices

1. **Use descriptive keys**: Prefer `login_button_submit` over `button1`
2. **Add comments**: Always include comments explaining context
3. **Group by feature**: Keep related keys together in the strings file
4. **Avoid string concatenation**: Use format specifiers instead
5. **Test all languages**: Verify UI doesn't break with longer translations
6. **Use genstrings**: For large projects, use `genstrings` tool to extract keys automatically

```bash
# Generate strings file from Swift files
find . -name "*.swift" -print0 | xargs -0 genstrings -o en.lproj/
```
