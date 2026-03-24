# Repository Guidelines

## Project Structure & Module Organization
This is an iOS app workspace centered on the `HiWidget` target.
- `HiWidget/`: main app source (UI, business logic, resources, localized `*.lproj` files).
- `HWidgetExt/`, `HIntentExt/`, `HiWidgetServiceExtension/`, `Island/`, `HiWidgetStickerPackExt/`: extension targets.
- `AD/`, `IAP/`, `Track/`: shared feature modules for ads, purchases, and analytics.
- `Third/`: vendored third-party source/frameworks.
- `Pods/`: CocoaPods dependencies (generated; do not hand-edit).
- `HiWidget.xcodeproj` and `HiWidget.xcworkspace`: project/workspace definitions.

## Build, Test, and Development Commands
- `pod install`: install/update CocoaPods dependencies after changing `Podfile`.
- `open HiWidget.xcworkspace`: open the workspace in Xcode (preferred over `.xcodeproj`).
- `xcodebuild -workspace HiWidget.xcworkspace -scheme HiWidget -configuration Debug build`: CLI build for the main app.
- `xcodebuild -workspace HiWidget.xcworkspace -scheme HWidgetExtExtension build`: validate widget extension build.

Use the shared schemes under `HiWidget.xcodeproj/xcshareddata/xcschemes/` for consistent local/CI behavior.

## Coding Style & Naming Conventions
- Language is primarily Swift; use 4-space indentation and keep line breaks readable.
- Follow existing UIKit/SwiftUI patterns in nearby files; keep changes scoped to the target module.
- Types use `UpperCamelCase`; methods/properties use `lowerCamelCase`; constants should be descriptive.
- Group feature-specific code under existing folders (for example, ad logic stays in `AD/ADManage/...`).
- No repo-wide formatter/linter config is committed; use Xcode formatting tools before submitting.
- In SwiftUI, `Text("xxx")` automatically participates in localization. Do not add `.L` or `.localized()` just to make `Text("xxx")` localizable unless the surrounding API specifically requires a resolved `String`.
- When a resolved localized `String` is needed in modified code, prefer `.L` over `.localized()` for brevity. Reuse existing localization keys.
- Prefer the compatibility helpers in [View+Extension.swift](/Users/guanquanhua/Desktop/Dev/ioswidget/HiWidget/HiWidget/Common/Extension/View+Extension.swift) for the related SwiftUI APIs to avoid iOS-version compile warnings.
- Use `backgroundd(...)` instead of the newer `background(alignment:content:)` overloads when writing code that needs to support mixed iOS versions.
- Use `overlayy(...)` instead of the newer `overlay(alignment:content:)` overloads when adding overlays with alignment or `ViewBuilder` content.
- Use `maskk(...)` instead of the iOS 15 style `mask(alignment:_:)` / closure-based mask overloads when masking views.
- Use `foregroundColorr(...)` instead of directly switching between `foregroundStyle(_:)` and `foregroundColor(_:)`; this wrapper already handles the compatibility split.
- In SwiftUI, when using hex colors, prefer `Color(hex6: ...)` over `Color(UIColor(hex6: ...))`.
- Before adding a new entry to `Localizable.strings`, first check whether that key already exists. If it exists, reuse the existing entry instead of adding a new one.
- If duplicate translation entries are found, delete the newly added duplicate and keep the original existing entry.

## Testing Guidelines
There are currently no dedicated XCTest targets in shared schemes. For new logic:
- Add unit tests in a new `*Tests` target when introducing non-trivial business logic.
- Prefer deterministic tests (no network or ad-SDK calls).
- Run tests with `xcodebuild ... test` for the scheme that includes your test target.
- 因代码库较大，日常需求改动后默认不要执行全量 `xcodebuild`。优先只对本次修改直接影响的文件或模块做语法级校验；仅在用户明确要求、或语法级检查不足以覆盖风险时，再进行更重的构建验证。

## Commit & Pull Request Guidelines
- Recent history uses `feat:` / `fix:` prefixes plus concise summaries; follow this for new commits.
- Commit message 使用中文。
- Keep commits focused (single concern per commit) and avoid bundling refactors with feature work.
- PRs should include: purpose, affected targets/modules, verification steps, and screenshots/videos for UI changes.
- Link related issue/task IDs and call out any config, entitlement, or dependency updates explicitly.
