# Capsoul — instructions for coding agents

Read SPEC.md before starting work. When a decision changes, update SPEC.md in the same change.
Also follow AGENTS.md (Expo's general guidance: check versioned Expo docs instead of trusting memory, routing rules, EAS). Where the two conflict, this file wins.

## Hard constraints
- Do not view, copy or adapt code, assets or UI layouts from the Daily You app or its repository. All design is original.
- Never delete user entries or photos without an explicit user confirmation flow. Database migrations must be non-destructive.

## Stack
- Expo (React Native), TypeScript in strict mode, Expo Router.
- Storage: expo-sqlite for data, app document directory (expo-file-system) for photos.
- Ask before adding a dependency, especially one that needs a development build and won't run in Expo Go.

## Commands
- `npx expo start` — dev server; scan the QR code with Expo Go
- `npx expo install <package>` — add packages (never plain npm install, so versions match the SDK)
- `npx tsc --noEmit` — typecheck
- `npx expo lint` — lint
- Tests: no test runner yet; add one with the first data-layer code.

## Workflow
- Small changes on a branch; one feature or fix per pull request.
- Run typecheck, lint and tests before committing.
- Commit and PR descriptions explain *why*, not only what. The owner is learning agentic workflows, so call out non-obvious decisions.

## Layout
- Must work on tall foldables (Galaxy Z Flip 6) and resize correctly when the phone folds or unfolds.
- No fixed pixel heights; respect safe areas; test on Android and iOS.
