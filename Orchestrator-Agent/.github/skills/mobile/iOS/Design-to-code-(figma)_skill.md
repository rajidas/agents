# Agent guide (Cursor + VS Code Copilot)

Single source of truth for how this agent should behave.  
Cursor rules and `.github/copilot-instructions.md` point here.

## Ask first

1. Platform? `ios_swiftui` | `flutter` | `react_native` | `android_compose`
2. Fresh under `output/<AppName>/`, or integrate into an existing project?
3. If integrate: absolute path; wire navigation? (default **no** — write `INTEGRATION.md`)

## Source of truth

1. **Official Figma MCP** — layout, variables, components, assets, screenshots
2. **Editor AI** (Cursor / Copilot) — architecture + codegen — **no API keys**
3. **Python CLI** (`figma-ui` / `figma-swiftui`) — assets, build, screenshots, similarity reports

Never invent MCP-available values. Never ask for OpenAI/Anthropic keys when `LLM_PROVIDER=editor`.

## Targets

| Target | Stack |
|---|---|
| `ios_swiftui` | SwiftUI iOS 17+, `@Observable` MVVM |
| `flutter` | Flutter 3.x, `ChangeNotifier`, Material 3 |
| `react_native` | Expo + TypeScript MVVM |
| `android_compose` | Jetpack Compose + ViewModel, Material 3 |

## Quality

- Design tokens only (no hardcoded colors/spacing/typography in views)
- Similarity gate **95%**, target **98%**, max **10** fix iterations
- Integrate mode: never delete the existing tree; skip app entry unless `--wire-navigation`
- Accessibility / Animation agents are out of scope (stubs)

## Asset folders (important)

Downloaded images keep nested paths from Figma (e.g. `Assets/checkout/checkbox.png`
→ `assets/checkout/checkbox.png`). The agent auto-registers them:

| Platform | What gets updated |
|---|---|
| Flutter | `pubspec.yaml` → every folder (`assets/`, `assets/checkout/`, …) |
| React Native | `src/assets/GeneratedAssets.ts` with static `require()` entries |
| iOS | Nested `Assets.xcassets/<folder>/…imageset` (namespaced) |
| Android | Flattened `res/drawable/` **and** nested `src/main/assets/` |

Flutter does **not** recurse — listing only `assets/` is not enough for `assets/checkout/`.

## Repo map (what matters)

```text
Design-to-code-(figma)_skill.md ← you are here
README.md                 ← human start guide
skills/figma-convert/     ← chat skill (all platforms)
configs/                  ← defaults + prompts
templates/                ← blank project scaffolds
src/figma_swiftui/        ← CLI + pipeline (advanced)
output/                   ← generated apps
runs/                     ← reports / screenshots
```

Everything else is implementation detail — newcomers usually only need README + chat.
