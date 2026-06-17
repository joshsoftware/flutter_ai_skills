# Flutter AI Skills Template

A ready-to-use Flutter project template with AI skills pre-installed for both Claude and Cursor.

The project was initialized with `flutter create .`, and the skills below are already wired up for both tools — no bootstrap step required.

This template standardizes:

* Clean Architecture
* Feature-first development
* API integrations
* Authentication
* Local storage
* Caching
* Testing
* Pull Request Reviews

---

# Repository Structure

```text
flutter_ai_skills/
├── README.md
├── AGENTS.md
├── CLAUDE.md
│
├── .claude/
│   └── skills/
│       ├── project-bootstrap/
│       ├── architecture/
│       ├── api-standards/
│       ├── auth-standards/
│       ├── data-storage/
│       ├── caching/
│       ├── unit-testing/
│       └── flutter-pr-review/
│
├── .cursor/
│   └── skills/
│       └── ... (mirrors .claude/skills)
│
├── lib/
│   └── main.dart
├── test/
├── android/ ios/ web/ linux/ macos/ windows/
└── pubspec.yaml
```

---

# Prerequisites

Install either:

## Flutter

```bash
flutter --version
```

## FVM

```bash
fvm --version
```

---

# Getting Started

This repo is a Flutter project, not just a skills library. Use it as a template for new apps:

```bash
git clone <this-repo-url> banking_app
cd banking_app
flutter pub get
```

Cloning only renames the folder — the project is still identified as `flutter_ai_skills` everywhere else (`pubspec.yaml` name, Android `applicationId`, iOS bundle identifier, desktop app names, etc.). To actually rename the app, use the [`rename`](https://pub.dev/packages/rename) package:

```bash
dart pub global activate rename
rename setAppName --value "Banking App"
rename setBundleId --value "com.example.banking_app"
```

Or update these manually if you prefer not to install the tool:

* `pubspec.yaml` → `name:`
* `android/app/build.gradle` → `applicationId`
* `ios/Runner.xcodeproj` / `Info.plist` → bundle identifier
* `linux/`, `macos/`, `windows/` → executable/display name

## Open with Cursor

```bash
cursor .
```

## Open with Claude

```bash
claude
```

Both tools pick up their respective skills automatically — Cursor from `.cursor/skills/`, Claude from `.claude/skills/` (driven by `CLAUDE.md` at the project root).

---

# Available Skills

| Skill             | Purpose                                 |
| ----------------- | --------------------------------------- |
| project-bootstrap | Project initialization                  |
| architecture      | Architecture and feature generation     |
| api-standards     | APIs, repositories, models, datasources |
| auth-standards    | Authentication and authorization        |
| data-storage      | Hive and Secure Storage                 |
| caching           | Offline support and caching             |
| unit-testing      | Unit, widget and integration testing    |
| flutter-pr-review | Pull request review                     |

---

# Developer Workflow

## Step 1

Initialize the project:

```text
Initialize this project
```

The AI automatically:

* Loads project-bootstrap
* Loads architecture
* Creates the project foundation

---

## Step 2

Create a feature:

```text
Create Customer feature
```

The AI automatically:

* Creates domain layer
* Creates data layer
* Creates presentation layer
* Creates providers
* Creates routing
* Creates tests when required

---

## Step 3

Create authentication:

```text
Create Login feature
```

The AI automatically applies:

* architecture
* auth-standards
* data-storage
* unit-testing

---

## Step 4

Add caching:

```text
Add caching to Customer feature
```

The AI automatically applies:

* caching

---

## Step 5

Generate tests:

```text
Generate tests for Customer feature
```

The AI automatically applies:

* unit-testing

---

## Step 6

Review code:

```text
Review current branch changes
```

The AI automatically applies:

* flutter-pr-review

---

# Example Prompts

```text
Initialize this project

Create Login feature

Create Customer feature

Create Dashboard feature

Add caching to Customer feature

Generate tests for Customer feature

Review current branch changes
```

---

# Architecture

The architecture specification is defined in:

```text
.claude/skills/architecture/SKILL.md
.cursor/skills/architecture/SKILL.md
```

This is the source of truth for:

* Clean Architecture
* Feature First Structure
* Dependency Rules
* Riverpod
* GoRouter
* Dio
* Hive
* Secure Storage
* Testing Standards

All generated code must follow this specification.

---

# Project Priorities

1. Security
2. Scalability
3. Clean Architecture
4. Testability
5. Maintainability

All skills and generated code must follow these priorities.
