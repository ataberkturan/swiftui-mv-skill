# Examples

## Full Project Structure

```text
Project
├── Views
│   ├── HomeView
│   │   ├── HomeView.swift
│   │   ├── HomeState.swift
│   │   └── Extensions
│   │       ├── HomeView+Components.swift
│   │       └── HomeView+Extensions.swift
│   └── LoginView
│       ├── LoginView.swift
│       ├── LoginState.swift
│       └── Extensions
│           ├── LoginView+Components.swift
│           └── LoginView+Extensions.swift
├── Models
│   ├── User.swift
│   ├── LoginResponse.swift
│   └── GenerationResult.swift
├── Enums
│   ├── AppRoute.swift
│   ├── AppTheme.swift
│   └── PermissionStatus.swift
├── Services
│   ├── APIClient.swift
│   ├── AuthService.swift
│   ├── StorageService.swift
│   ├── SubscriptionService.swift
│   ├── RemoteConfigService.swift
│   ├── KeychainService.swift
│   └── GenerationService.swift
├── DataStore
│   ├── AppDataStore.swift
│   ├── CreditStore.swift
│   └── FeatureFlagStore.swift
├── DesignSystem
│   ├── Foundation
│   │   ├── Colors
│   │   │   └── Colors.swift
│   │   ├── Typography
│   │   │   └── Typography.swift
│   │   ├── Icons
│   │   │   └── Icons.swift
│   │   └── Spacing
│   │       └── Spacing.swift
│   ├── Components
│   │   ├── EmptyStateCard.swift
│   │   └── StatCard.swift
│   ├── Views
│   │   ├── GlassPanelView.swift
│   │   └── LoadingCardView.swift
│   ├── ButtonStyles
│   │   ├── MediumButtonStyle.swift
│   │   └── PrimaryButtonStyle.swift
│   └── ViewModifiers
│       ├── GlassBackgroundModifier.swift
│       └── CardSurfaceModifier.swift
├── Resources
│   ├── Assets
│   │   ├── Icons.xcassets
│   │   ├── Images.xcassets
│   │   └── Illustrations.xcassets
│   ├── Audio
│   │   └── success_sound.mp3
│   ├── Data
│   │   └── onboarding_questions.json
│   └── Graphics
│       └── background.svg
└── Utils
    ├── Extensions
    │   ├── String+Extensions.swift
    │   ├── Date+Extensions.swift
    │   └── View+Extensions.swift
    ├── ViewModifiers
    │   ├── KeyboardDismissModifier.swift
    │   └── ReadSizeModifier.swift
    └── Helpers
        └── Validator.swift
```

This is an example. Do not create unused folders just for the sake of structure.

## Correct Local Components

```swift
extension HomeView {
    var headerSection: some View {
        VStack(alignment: .leading) {
            Text("Wallpaper")
            Text("Create a new image")
        }
    }

    func modeButton(title: String, isSelected: Bool) -> some View {
        Button(title) {
            selectedMode = title
        }
        .buttonStyle(isSelected ? .borderedProminent : .bordered)
    }
}
```

## Wrong Local Component

```swift
struct HeaderSection: View {
    var body: some View {
        Text("Wallpaper")
    }
}
```

Do not place standalone `struct View` declarations inside `ViewName+Components.swift`.

## Correct Service Consumption

```swift
@MainActor
@Observable
final class AuthService {
    func signIn(email: String, password: String) async throws {
        ...
    }
}

struct LoginView: View {
    @Environment(AuthService.self) private var authService
    @State private var email = ""
    @State private var password = ""
    @State private var state: LoginState = .idle

    var body: some View {
        Button("Sign In") {
            handleSignInTap()
        }
    }
}
```

## Wrong Service Creation

```swift
struct LoginView: View {
    private let authService = AuthService()
}
```

Views consume services; they do not instantiate them.

## Correct DataStore Shape

```swift
import Observation

@MainActor
@Observable
final class AppDataStore {
    var isUserCompletedOnboarding = false
    var isFirstLaunch = true
    var currentCredits = 0
    var dailyUsageLimit = 0
    var isSubscribed = false
}
```

## Correct Observation Environment Injection

```swift
import Observation
import SwiftUI

@MainActor
@Observable
final class AuthService {
    func signIn(email: String, password: String) async throws {
        ...
    }
}

@main
struct WallpaperApp: App {
    @State private var appDataStore = AppDataStore()
    @State private var authService = AuthService()

    var body: some Scene {
        WindowGroup {
            RootView()
                .environment(appDataStore)
                .environment(authService)
        }
    }
}

struct LoginView: View {
    @Environment(AppDataStore.self) private var appDataStore
    @Environment(AuthService.self) private var authService
    @State private var email = ""

    var body: some View {
        TextField("Email", text: $email)
    }
}
```

## Correct Binding to Observable Environment

```swift
struct SettingsView: View {
    @Environment(AppDataStore.self) private var appDataStore

    var body: some View {
        @Bindable var appDataStore = appDataStore
        Toggle("Subscribed", isOn: $appDataStore.isSubscribed)
    }
}
```

## Wrong DataStore Scope

```swift
@Observable
final class AppDataStore {
    var searchText = ""
    var isSheetPresented = false
    var selectedTabAnimationProgress = 0.0

    func fetchRemoteConfigDirectlyFromSupabase() async {
        ...
    }
}
```

Temporary UI state belongs in views. Direct external-system implementation belongs in services.

## Correct Model and Enum Placement

```text
Models/Wallpaper.swift
Models/LoginResponse.swift
Enums/AppRoute.swift
Enums/GenerationMode.swift
Views/HomeView/HomeState.swift
```

Wrong:

```text
Views/HomeView/Wallpaper.swift
Enums/HomeState.swift
States/HomeState.swift
```

## Resources vs DesignSystem

```text
Resources/Assets/Icons.xcassets
DesignSystem/Foundation/Icons/Icons.swift
```

The first path stores raw assets. The second path provides SwiftUI-native access.

## Utils vs DesignSystem Modifiers

```text
DesignSystem/ViewModifiers/CardSurfaceModifier.swift
DesignSystem/ViewModifiers/GlassBackgroundModifier.swift
Utils/ViewModifiers/KeyboardDismissModifier.swift
Utils/ViewModifiers/ReadSizeModifier.swift
```

Visual design language belongs in `DesignSystem`. Technical behavior helpers belong in `Utils`.
