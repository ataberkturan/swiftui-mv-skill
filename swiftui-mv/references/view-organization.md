# View Organization

## Screen Folder Pattern

Each screen-level SwiftUI view gets its own group under `Views`. The group name matches the view name.

```text
Views
└── HomeView
    ├── HomeView.swift
    ├── HomeState.swift
    └── Extensions
        ├── HomeView+Components.swift
        └── HomeView+Extensions.swift
```

Repeat this pattern for each screen:

```text
Views
├── HomeView
├── LoginView
└── ProfileView
```

## Main View File

`HomeView.swift` contains:

```text
main view struct
body
high-level layout
local @State properties
@Environment dependencies
top-level screen composition
```

Keep the main file readable and high-level. Move small view fragments to `HomeView+Components.swift`. Move local actions and helpers to `HomeView+Extensions.swift`.

## Components Extension

`Views/HomeView/Extensions/HomeView+Components.swift` contains small UI fragments that belong only to `HomeView`.

Do not create standalone `struct View` components inside `ViewName+Components.swift`.

Correct:

```swift
extension HomeView {
    var headerSection: some View {
        VStack {
            Text("Title")
            Text("Subtitle")
        }
    }

    func optionRow(title: String, isSelected: Bool) -> some View {
        HStack {
            Text(title)
            Spacer()

            if isSelected {
                Image(systemName: "checkmark")
            }
        }
    }
}
```

Wrong:

```swift
struct HeaderSection: View {
    var body: some View {
        Text("Header")
    }
}
```

## Local Component Rule

```text
No parameters -> computed property
Has parameters -> function
```

Examples:

```swift
var headerSection: some View {
    ...
}

func actionButton(title: String) -> some View {
    ...
}
```

Local components should be small, view-specific, simple, scoped to the parent view, not reusable across the app, and not complex enough to become a standalone view.

If a component becomes too complex, too generic, or likely to be reused, move it to `DesignSystem`.

## View Extensions

`Views/HomeView/Extensions/HomeView+Extensions.swift` contains view-specific methods:

```text
button action methods
small helper methods
view-specific interaction logic
small async wrappers
formatting helpers
screen-local behavior
```

Example:

```swift
extension HomeView {
    func handleGenerateButtonTap() {
        Task {
            await generateWallpaper()
        }
    }

    func resetGenerationState() {
        prompt = ""
        selectedImage = nil
        state = .idle
    }
}
```

Do not put networking, database logic, authentication, payment logic, storage implementation, image generation business logic, or large domain logic in a view extension. Those belong in `Services`.

## View-Specific State

If a state enum belongs only to one view, keep it inside that view group.

Correct:

```text
Views/HomeView/HomeState.swift
```

Wrong:

```text
Enums/HomeState.swift
```

Example:

```swift
enum HomeState {
    case idle
    case loading
    case result
    case failed
}
```

Do not create a global `States` folder.

Rule:

```text
View-specific UI state -> corresponding View folder
Shared/global state enum -> Enums folder
```

Shared/global enums such as `AppRoute`, `GenerationMode`, `AppTheme`, and `PermissionStatus` belong in `Enums`.

## Local vs Reusable UI

Keep UI in `ViewName+Components.swift` when it is:

```text
small
screen-specific
strongly tied to parent view state
not generic
not complex
a simple local UI fragment
```

Move UI to `DesignSystem` when it is:

```text
generic
standalone
complex
reusable or potentially reusable
too large for a view extension
UI/styling focused
initialized with its own model/config
```

Simple rule:

```text
Small + screen-specific -> ViewName+Components.swift
Generic + standalone + complex -> DesignSystem
```

## Naming

```text
View group: HomeView
Main view file: HomeView.swift
Components extension file: HomeView+Components.swift
Extensions file: HomeView+Extensions.swift
View state file: HomeState.swift or HomeViewState.swift
```
