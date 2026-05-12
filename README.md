# SwiftUI MV Skill

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform](https://img.shields.io/badge/Platform-iOS%20|%20macOS-000000.svg?logo=apple)](https://developer.apple.com/swift/)
[![Swift](https://img.shields.io/badge/Swift-5.10+-F05138.svg?logo=swift&logoColor=white)](https://swift.org)
[![Codex](https://img.shields.io/badge/Codex-Skill-111111.svg)](https://openai.com/codex/)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-blueviolet.svg)](https://claude.ai/code)

SwiftUI MV Skill is an agent skill for building, refactoring, and reviewing
SwiftUI apps with a clean `MV + Services + DataStore + DesignSystem`
architecture.

It helps Codex and Claude Code make consistent architecture decisions while
working inside SwiftUI projects. Instead of creating ViewModels by default, it
keeps UI, state, business logic, reusable components, resources, and utilities
in clear ownership boundaries.

## What It Does

SwiftUI MV Skill guides an AI coding agent to:

- inspect the existing SwiftUI project structure before making changes
- keep screen-local state inside SwiftUI views
- move business logic, networking, persistence, auth, subscriptions, and domain
  actions into services
- keep global app state in an Observation-based DataStore
- organize reusable UI code inside a DesignSystem
- separate raw app assets and files into Resources
- place general-purpose helpers and extensions inside Utils
- avoid creating ViewModels unless the project already uses them or the user
  explicitly asks for that architecture
- run a read-only architecture audit with the bundled helper script

## Folder Structure

The skill prefers this root-level SwiftUI app structure when the app needs those
areas:

```text
Project
├── Views
├── Models
├── Enums
├── Services
├── DataStore
├── DesignSystem
├── Resources
└── Utils
```

Folder responsibilities:

- `Views`: SwiftUI screens, screen-local state, local view fragments, and
  screen-specific extensions.
- `Models`: data models, domain models, API models, persistence models, and
  DTO-like types.
- `Enums`: shared app, route, mode, category, domain, and service-level enum
  types.
- `Services`: business logic and external systems such as networking,
  authentication, storage, subscriptions, remote config, notifications,
  payments, and backend communication.
- `DataStore`: app-wide observable state and global source of truth, preferably
  using Swift Observation with `@Observable`.
- `DesignSystem`: reusable UI components, button styles, visual modifiers,
  colors, typography, icons, spacing, and native SwiftUI style extensions.
- `Resources`: raw non-Swift files such as asset catalogs, images, icons, JSON,
  audio, video, fonts, markdown, PDFs, and SVGs.
- `Utils`: general helper code, Swift extensions, technical view modifiers,
  validators, date helpers, and other non-design-system utilities.

Unused folders do not need to be created just to satisfy the structure.

## Install For Both Codex And Claude Code

Install the same `swiftui-mv/` skill folder into both agent skill directories:

```bash
git clone https://github.com/ataberkturan/swiftui-mv-skill.git
mkdir -p ~/.codex/skills ~/.claude/skills
cp -R swiftui-mv-skill/swiftui-mv ~/.codex/skills/
cp -R swiftui-mv-skill/swiftui-mv ~/.claude/skills/
```

Restart Codex or Claude Code after installation if the skill does not appear
immediately.

## Install For Codex

Install from inside Codex with Skill Installer:

```text
$skill-installer install https://github.com/ataberkturan/swiftui-mv-skill/tree/main/swiftui-mv
```

Codex Skill Installer installs it into:

```text
~/.codex/skills/swiftui-mv
```

You can trigger it by asking Codex to use `swiftui-mv`, or by making a request
that matches the skill description, such as reviewing a SwiftUI architecture or
refactoring a feature into MV + Services + DataStore.

## Install For Claude Code

Install as a personal Claude Code skill:

```bash
git clone https://github.com/ataberkturan/swiftui-mv-skill.git
mkdir -p ~/.claude/skills
cp -R swiftui-mv-skill/swiftui-mv ~/.claude/skills/
```

Claude Code discovers personal skills from:

```text
~/.claude/skills/<skill-name>/SKILL.md
```

This copies the skill to:

```text
~/.claude/skills/swiftui-mv
```

Use it explicitly with:

```text
/swiftui-mv
```

or let Claude Code invoke it automatically when your request matches the skill.

## Local Development

From a cloned checkout, install locally with:

```bash
mkdir -p ~/.codex/skills ~/.claude/skills
cp -R swiftui-mv ~/.codex/skills/
cp -R swiftui-mv ~/.claude/skills/
```

Run the bundled architecture audit helper:

```bash
python3 swiftui-mv/scripts/audit_swiftui_mv.py /path/to/SwiftUI/project
```

Use strict mode when warnings should fail the audit:

```bash
python3 swiftui-mv/scripts/audit_swiftui_mv.py /path/to/SwiftUI/project --strict
```

## Repository Layout

```text
swiftui-mv-skill
├── swiftui-mv
│   ├── SKILL.md
│   ├── agents
│   ├── references
│   └── scripts
└── tests
```

`swiftui-mv/` is the canonical skill folder. It contains the required
`SKILL.md`, optional UI metadata, references, and the read-only audit helper.

created by Ataberk Turan.
