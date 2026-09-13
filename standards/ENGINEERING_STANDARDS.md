# Cure Consulting Group Engineering Standards

This document ratifies the official engineering standards, architecture conventions, and operational workflows for all software engineered across **Cure Consulting Group**. Every internal product, client engagement, and venture portfolio project must adhere to these standards.

---

## 1. Per-Project Standards

### 1.1 Multi-Platform Project Topology (Monorepo Standard)
For projects targeting multiple platforms (e.g., Web, iOS, Android, Chrome Extension, Backend), the project **must** follow a unified monorepo structure. This guarantees synchronized data contracts, design tokens, and shared business logic.

```
<project-root>/
├── apps/
│   ├── web/                    # Next.js / Vite Web Application
│   ├── ios/                    # SwiftUI iOS Application (Xcode / SPM)
│   ├── android/                # Kotlin Jetpack Compose Application
│   └── extension/              # Chrome Extension (Manifest V3)
├── services/
│   ├── api/                    # Core Backend (FastAPI, Cloud Run, Express)
│   └── functions/              # Cloud Functions / Firebase serverless triggers
├── packages/
│   ├── contracts/              # Shared schemas (Zod, TypeScript types, OpenAPI, Protobuf)
│   ├── design-tokens/          # Shared colors, typography, layout constants
│   └── sdk/                    # Generated or hand-crafted API client for web/mobile
├── tooling/
│   ├── eslint-config/          # Shared ESLint presets
│   ├── tsconfig/               # Base TypeScript configurations
│   └── maestro/                # Cross-platform E2E test flows
├── .github/
│   ├── workflows/              # GitHub Actions CI/CD pipelines
│   └── dependabot.yml          # Grouped dependency automation
├── package.json                # Turborepo / pnpm / npm workspace root
└── README.md                   # Project overview, setup, and runbook
```

### 1.2 Single-Platform Project Topology (Polyrepo Standard)
For standalone microservices, command-line utilities, or single-tier applications (e.g., `cure-finops-watchdog`):
```
<project-root>/
├── src/                        # Application source code
├── tests/                      # Unit, integration, and contract tests
├── scripts/                    # Bootstrap and deployment scripts
├── .github/workflows/          # CI/CD pipelines
├── Dockerfile                  # Container definition
├── README.md                   # Setup guide and architecture documentation
└── [package.json|pyproject.toml|Package.swift]
```

### 1.3 Branching Strategy
- **Default Branch**: Must always be `main` (representing releasable production state).
- **Branch Naming**:
  - `feat/<ticket-id>-<short-description>` (e.g., `feat/STORY-104-boxscore-pdf`)
  - `fix/<ticket-id>-<short-description>` (e.g., `fix/BUG-402-token-expiry`)
  - `chore/<ticket-id>-<short-description>` (e.g., `chore/deps-bump-nextjs`)
- **Protected Rules**:
  - No direct pushes to `main`.
  - All modifications must arrive through Pull Requests.
  - Linear git history: use **Squash and Merge** with descriptive PR titles.

### 1.4 Environment & Secret Isolation
- **Rule of Zero Secrets**: Never commit `.env`, `.env.local`, API keys, service accounts, or credentials.
- **Three-Tier Environment**:
  1. `Development`: Runs against local Firebase/Docker emulators.
  2. `Staging / Preview`: Automatically provisioned on pull requests for QA/UAT.
  3. `Production`: Live customer-facing environment.
- **CI/CD Cloud Authentication**: Use GitHub Actions OIDC (Workload Identity Federation) for GCP/AWS. Avoid long-lived service account keys.

---

## 2. Per-Platform Standards

### 2.1 Web Applications (`apps/web`)
- **Framework**: Next.js (App Router preferred) or Vite + React for SPA dashboards.
- **Language**: TypeScript with `strict: true`. The use of `any` is forbidden.
- **Validation**: Every API response and user input must be validated via `zod`.
- **Styling**: Tailwind CSS utilizing standardized design tokens from `packages/design-tokens`.
- **Quality Gates**:
  - `npm run lint` (ESLint) must pass with zero warnings.
  - `npm run typecheck` (`tsc --noEmit`) must exit with code 0.
  - Core user funnels covered by **Playwright** end-to-end tests.

### 2.2 iOS Applications (`apps/ios`)
- **Framework**: SwiftUI-first. UIKit only when bridging specialized SDKs.
- **Language & Runtime**: Swift 6 Concurrency (`async/await`, `@MainActor`, structured tasks). Unchecked concurrency is prohibited.
- **Package Management**: Swift Package Manager (SPM). CocoaPods is deprecated.
- **Testing**:
  - Unit tests via `XCTest` / Swift Testing framework.
  - Mobile UI automation flows written in **Maestro**.
- **Distribution**: Automated Fastlane pipelines deploying signed release builds to TestFlight upon Git tagging (`v*.*.*`).
- **Bundle ID Convention**: `com.cureconsulting.<client-or-product>.<app>`.

### 2.3 Android Applications (`apps/android`)
- **Framework**: Jetpack Compose with Material3.
- **Language**: Kotlin with Coroutines and `StateFlow`.
- **Architecture**: Clean Architecture (Presentation -> Domain -> Data).
- **Dependency Management**: Centralized Gradle Version Catalog (`gradle/libs.versions.toml`).
- **Dependency Injection**: Hilt / Dagger.
- **Testing**:
  - Unit tests with JUnit5 and MockK.
  - Screenshot regression testing with **Paparazzi**.
  - E2E testing with **Maestro**.
- **Delivery**: Gradle Play Publisher or Firebase App Distribution for staging tracks.

### 2.4 Chrome Extensions (`apps/extension`)
- **Standard**: Manifest V3 compliant.
- **Lifecycle**: Background processes must execute within ephemeral Service Workers. Persistent background scripts are prohibited.
- **Security**: Content scripts must be strictly isolated. Sensitive authentication tokens must reside in `chrome.storage.session` and never `localStorage`.

### 2.5 Backend & Cloud Services (`services/api`, `services/functions`)
- **Frameworks**:
  - Python: FastAPI with Pydantic v2.
  - Node.js: Express or Firebase Cloud Functions (Gen 2).
  - AI Workflows: Firebase Genkit or Google Gen AI SDK.
- **Compliance & Security**:
  - For Health Tech (`Level5`, etc.): Strict HIPAA adherence. Zero PHI logging in plaintext. Field-level encryption for sensitive clinical transcripts.
  - Structured JSON logging with standardized severity levels (`DEBUG`, `INFO`, `WARN`, `ERROR`).
- **Emulation**: All cloud functions and Firestore rules must be executable locally via `firebase emulators:start`.

---

## 3. Per-Task Standards

### 3.1 Task & Issue Taxonomy
Every work item tracked in GitHub Issues or Jira must follow standard issue taxonomy:
- `EPIC-<ID>`: Multi-week product initiatives (e.g., `EPIC-080: HoopTrace Ecosystem Rebranding`).
- `STORY-<ID>`: User-facing capability increments (e.g., `STORY-1046: Team season fixture scheduling`).
- `TASK-<ID>`: Platform-specific engineering tasks (e.g., `TASK-204: Build SwiftUI game clock controller`).
- `BUG-<ID>`: Defect repairs against existing ratified specs.
- `CHORE-<ID>`: Infrastructure, dependencies, performance ratchet, and refactoring.

### 3.2 Commit Standards (Conventional Commits)
All commit messages must follow the format:
`<type>(<scope>): <imperative description>`

**Permitted Types**:
- `feat`: A new feature or capability.
- `fix`: A bug fix.
- `perf`: Performance optimization.
- `refactor`: Code change that neither fixes a bug nor adds a feature.
- `test`: Adding or correcting tests.
- `chore`: Build tooling, dependency bumps, linting changes.
- `docs`: Documentation updates.

**Examples**:
- `feat(ios): stream live boxscore updates via WebSocket`
- `fix(web): prevent duplicate stat capture on fast taps`
- `chore(deps): group and bump firebase dependencies to v13.7`

### 3.3 PR Sizing & Scoping Guardrails
- **Diff Ceiling**: Pull requests must not exceed **400 lines of modified code** (excluding generated lockfiles or mocks).
- **Single Responsibility**: One PR addresses one story or bug. Do not bundle unrelated refactoring with feature delivery.
- **Cross-Platform Decoupling Rule**:
  When a feature involves changes across multiple platforms (e.g., Web, iOS, and Backend):
  1. **PR 1 (Contract)**: Update shared types in `packages/contracts` and verify backwards-compatibility.
  2. **PR 2 (Backend)**: Deploy backend endpoint / function handling new contract.
  3. **PR 3 (Web)**: Implement web UI consuming contract.
  4. **PR 4 (iOS/Android)**: Implement native mobile screens consuming contract.
  *Never open a 3,000-line multi-platform monolithic PR.*

### 3.4 Multi-Platform PR Checklist (Definition of Done)
Before marking any PR ready for review:
- [ ] Code builds without errors on all target platforms.
- [ ] Automated tests pass (`npm test`, `pytest`, or `fastlane test`).
- [ ] For backend/API changes: backwards compatibility with earlier mobile app versions is guaranteed.
- [ ] No secrets, keys, or sensitive environment tokens are committed.
- [ ] Screenshots, screen recordings, or Maestro trace links are attached to UI PRs.
- [ ] PR description cites the parent `STORY-` or `BUG-` ticket.
