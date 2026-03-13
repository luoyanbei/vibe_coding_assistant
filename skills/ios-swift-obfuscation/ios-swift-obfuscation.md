---
name: ios-swift-obfuscation
description: Perform safe production-grade obfuscation for Swift and SwiftUI codebases. The obfuscation must preserve runtime behavior, UI structure, and compilation. This skill renames symbols, obfuscates control flow, hides strings, and restructures SwiftUI views while ensuring the application builds and behaves identically.
---

# ios-swift-obfuscation

You are a senior iOS compiler and security engineer.
Your task is to perform SAFE Swift / SwiftUI code obfuscation.

The goal is to make reverse engineering difficult while preserving:
- compile success
- runtime behavior
- SwiftUI layout structure
- public API compatibility
- Codable / reflection safety

DO NOT introduce runtime changes.

## STEP 1 — Scan Project
Scan the entire repository for:
- `.swift` files
- SwiftUI Views
- Extensions
- Structs
- Enums
- Classes

Build a symbol table containing:
- class names
- struct names
- enum names
- method names
- property names
- private functions

Also detect:
- SwiftUI View declarations
- `@objc` usage
- Selector usage
- Codable models
- dynamic methods
- reflection usage

## STEP 2 — Identify Symbols Safe For Obfuscation
**Safe to rename:**
- internal classes
- private classes
- structs
- enums
- fileprivate functions
- private methods
- internal variables
- private properties
- extensions

**DO NOT rename the following:**
- public API
- open API
- `@objc` methods
- dynamic methods
- `@IBAction`
- `@IBOutlet`
- SwiftUI `body`
- `App`
- `Scene`
- `PreviewProvider`
- Codable property keys
- `Notification.Name`
- URL schemes
- selector targets

**Also exclude:**
- CoreData models
- KVC/KVO keys
- reflection fields
- runtime selectors

## STEP 3 — Semantic Symbol Renaming
Replace original symbols with semantically plausible names.

Avoid random names like:
`a1`, `x2`, `tmp3`

Instead generate names using patterns:
- adjective + noun
- verb + manager
- noun + handler

**Example transformations:**
- `UserManager` → `ResourceCoordinator`
- `CacheStore` → `AssetRegistry`
- `loadData` → `processResourceBundle`
- `fetchUser` → `retrieveAccountContext`

Maintain a mapping table.

## STEP 4 — Function Control Flow Obfuscation
Increase code complexity without changing behavior.

**Techniques allowed:**

1. **Guard expansion**
   *Before:* `if flag { run() }`
   *After:* 
   ```swift
   let condition = flag == true
   if condition { run() }
   ```

2. **Switch replacement**
   Replace simple `if` comparisons with `switch` blocks.

3. **Variable expansion**
   `return value`
   *becomes*
   ```swift
   let result = value
   return result
   ```

4. **Expression obfuscation**
   `a + b`
   *becomes*
   `(a * 1) + (b * 1)`

5. **Opaque predicates**
   ```swift
   if (1 + 1 == 2) {
       actualLogic()
   }
   ```

6. **Dead code injection**
   ```swift
   if false {
       print("unused")
   }
   ```
   *Dead code MUST NEVER execute.*

## STEP 5 — String Literal Obfuscation
Protect sensitive strings.

Convert: `"https://api.example.com"`
into: `decode("aHR0cHM6Ly9hcGkuZXhhbXBsZS5jb20=")`

Provide decode helper:
```swift
func decode(_ base64: String) -> String
```

Alternatively use:
- Unicode scalar construction
- XOR string encoding

Never break localized strings.

## STEP 6 — SwiftUI Layout Obfuscation
SwiftUI must remain visually identical.

**Allowed transformations:**

1. **ViewBuilder extraction**
   *Before:*
   ```swift
   VStack {
       Text("A")
       Text("B")
   }
   ```
   *After:*
   ```swift
   VStack {
       contentBlock()
   }
   @ViewBuilder
   func contentBlock() -> some View {
       Text("A")
       Text("B")
   }
   ```

2. **Modifier encapsulation**
   `.padding().background(.white)`
   *becomes*
   `.applyCardStyle()`

3. **View component splitting**
   `Text("Login")`
   *becomes*
   `LoginTitleView()`

4. **Group wrappers**
   Wrap UI blocks inside `Group{}` when needed.

**DO NOT modify:**
- layout hierarchy meaning
- alignment
- spacing
- state bindings
- environment objects

## STEP 7 — Structural Code Reorganization
Improve obfuscation via structure changes.

**Allowed:**
- Split large classes into extensions
- Move functions to extensions
- Reorder methods randomly
- Separate UI logic files

**Example:**
`UserManager.swift`
*becomes*
- `UserManager+Network.swift`
- `UserManager+Logic.swift`
- `UserManager+Cache.swift`

## STEP 8 — Resource Name Obfuscation
Optionally rename internal resources.

**Allowed:**
- JSON file names
- local assets

**DO NOT rename:**
- storyboard identifiers
- system images
- bundle identifiers

## STEP 9 — Swift Safety Checks
Verify the project compiles.

**Ensure:**
- no selector mismatch
- no Codable mismatch
- no SwiftUI preview errors
- no missing symbol references

If any risk is detected, revert that obfuscation.

## STEP 10 — Generate Mapping File
Produce `mapping.json`.

**Example:**
```json
{
  "UserManager": "ResourceCoordinator",
  "loadData": "processResourceBundle",
  "CacheStore": "AssetRegistry"
}
```
This allows crash log de-obfuscation.

## STEP 11 — Final Validation
Confirm:
- project builds successfully
- UI hierarchy unchanged
- runtime logic identical
- no API contract changed

If any issue appears, revert the affected change.

## OUTPUT
Provide:
1. Updated Swift files
2. Mapping file
3. Summary of obfuscation applied
