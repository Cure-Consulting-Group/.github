## Summary
<!-- Brief description of the change, problem solved, and link to related ticket (e.g. STORY-123 or BUG-456). -->

Closes: #

---

## Type of Change
- [ ] 🚀 `feat`: New feature / capability
- [ ] 🐛 `fix`: Bug fix
- [ ] ⚡ `perf`: Performance improvement
- [ ] 🔨 `refactor`: Internal refactoring without feature change
- [ ] 🧪 `test`: Test additions or corrections
- [ ] 📦 `chore`: Build tooling, dependency, or CI update
- [ ] 📝 `docs`: Documentation updates

---

## Affected Platforms & Components
- [ ] 🌐 **Web** (`apps/web` - Next.js / React)
- [ ] 🍎 **iOS** (`apps/ios` - SwiftUI / Swift)
- [ ] 🤖 **Android** (`apps/android` - Kotlin / Compose)
- [ ] 🧩 **Extension** (`apps/extension` - Chrome MV3)
- [ ] ⚙️ **Backend / Functions** (`services/` - Cloud Functions / Cloud Run / FastAPI)
- [ ] 📐 **Shared Contracts / Packages** (`packages/contracts`, `design-tokens`)

---

## Cross-Platform & Contract Verification
<!-- Complete if changing shared contracts, APIs, or database models -->
- [ ] **Backwards Compatibility**: Existing client versions will continue to operate without crashing.
- [ ] **Shared Types Updated**: TypeScript / Swift / Kotlin contract models are in sync.
- [ ] **Schema Migration**: Database migrations or Firestore rules have been tested against local emulators.

---

## Checklist
- [ ] My code adheres to the [Cure Consulting Group Engineering Standards](https://github.com/Cure-Consulting-Group/.github/blob/main/standards/ENGINEERING_STANDARDS.md).
- [ ] Automated tests have been added or updated to cover this change.
- [ ] Linting and type-checking passed cleanly (`npm run lint`, `tsc --noEmit`, etc.).
- [ ] Zero secrets, credentials, or private API keys committed.
- [ ] Diff is kept under 400 lines (or split into focused sub-PRs).
- [ ] Screenshots, screen recordings, or Maestro trace links are attached (for UI changes).
