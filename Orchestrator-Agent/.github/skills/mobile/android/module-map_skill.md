---
name: module-map
description: >-
  Navigate the project's Gradle module structure to decide where new code belongs,
  understand cross-module dependency rules, and identify shared base classes and repositories.
  Use when planning a feature, auditing imports, or resolving module dependency questions.
---

# Purpose
Provides the full module inventory, cross-module dependency rules, shared package locations, and placement guidelines for new features.

# When to use
- Deciding which module a new feature or class belongs in
- Tracing where a shared repository or base class is defined
- Auditing a horizontal `feature A → feature B` dependency that should not exist
- Planning a new standalone module (requires lead approval)
- **Not for:** internal layer structure within a module (see `clean-architecture` skill)

# Procedure
1. Check the **Module Inventory** table to find the target module for the feature domain.
2. Verify the dependency is allowed using **Cross-Module Dependency Rules**.
3. If a feature needs data from another feature, use a shared repository interface in `myMarque/domain/repository/`.
4. For shared base classes (`BaseFragment`, `BaseFragmentViewModel`, etc.), always import from `com.base.*` packages in `myMarque`.
5. When creating a new standalone module, discuss with the lead first — document it in `README.md` and `.github/dependencies.md`.

# Inputs & Outputs
- **Inputs:** Feature description or class name to place; module name under question
- **Outputs:** Confirmed target module path; import package; dependency validation result

# Examples
**User ask:** "Where should I add a new charging schedule screen?"
**Assistant plan:** Charging is a remote vehicle command → place in `remote/` module under `com.psa.mym.remote`.
**Expected artifacts:** New files under `remote/src/main/java/com/psa/mym/remote/[feature]/`.

**User ask:** "Can my `dealer` feature import from `userProfile`?"
**Assistant plan:** No — horizontal feature-to-feature dependencies are forbidden. Extract the shared entity to `myMarque/domain/`.
**Expected artifacts:** Shared domain model in `myMarque`; both modules import from `com.base.*`.

# Notes & Guardrails
- `psa/security` is restricted — no modifications without lead approval.
- `myMarque` must never depend on feature modules (circular dependency).
- Feature modules must never import from each other directly.
- New module creation requires lead approval and documentation updates.

---

# Project Module Map
> Load this skill when analyzing where a feature belongs, identifying cross-module dependencies, or planning a new feature's module placement.

---

## 1. Module Inventory

| Module | Purpose | Key Domains |
|--------|---------|-------------|
| `myMarque` | Core application module — base classes, shared domain, authentication | BaseFragment, BaseFragmentViewModel, BaseViewModel, shared repositories (CarRepository, UserRepository), authentication, DI framework |
| `app` | App entry point, flavor configuration, manifest | Application class, MainActivity |
| `remote` | Vehicle remote control features (charge, climate, lock/unlock, horn, lights) | RemoteCommandRepository, charging schedules, precondition, remote notifications |
| `vehicle` | Vehicle management (add car, scan VIN, vehicle info) | VehicleRepository, VIN scanning, car management |
| `dealer` | Dealer locator, dealer appointments | DealerLocatorRepository, map integration |
| `evrouting` | Electric vehicle routing | Route planning, charging stop optimization |
| `userProfile` | User profile management, account settings | UserProfileRepository, biometrics, SAMS payment |
| `plp` | Premium Loyalty Program — accessories, vehicle courtesy, terms | PLPRepository, loyalty content |
| `onlyyou` | OnlyYou personalization feature | OnlyYou domain |
| `o2x` | O2X feature set | O2X domain |
| `sps` | SmartPhoneStation (CarPlay/Android Auto) | SmartPhoneStationRepository, media connectivity |
| `smartapps` | Smart Applications dashboard | Dashboard stats, trip data |
| `tmts` | TMTS feature | TMTS domain |
| `maintenance` | Vehicle maintenance scheduling | Maintenance domain |
| `assistance` | Roadside assistance | Assistance use cases |
| `navigation` | Custom navigation module (no Jetpack Navigation) | FragmentsHelper, routing |
| `cultureConfiguration` | App culture/locale configuration | Locale, brand configuration |
| `logger` | Logging library — exposes `MyMLogger` | `com.psa.logger.MyMLogger` |
| `btaCarProtocolLib` | BTA car communication protocol | Low-level car protocol |
| `bOUserMyMarque` | Business Object layer for user/car | BO models: UserMergeBO, UserCarMergeBO |
| `remote/psa/security` | PSA security layer | Sensitive — do not modify without lead approval |

---

## 2. Core Shared Packages (in `myMarque` module)

### Base Classes
| Class | Package | Purpose |
|-------|---------|---------|
| `BaseFragment<VM>` | `com.base.view.fragments` | Mandatory parent for all Fragments |
| `BaseFragmentViewModel` | `com.base.view.fragments` | Mandatory parent for all feature ViewModels |
| `BaseViewModel` | `com.base.view.viewmodel` | Parent of BaseFragmentViewModel — provides analytics, error, vehicle change |
| `BaseActivity` | `com.base.view.activities` | Activity base class |
| `BaseActivityViewModel` | `com.base.view.activities` | Shared activity ViewModel |

### Shared Repositories (Domain Interfaces)
| Interface | Package | Provided by |
|-----------|---------|-------------|
| `CarRepository` | `com.base.domain.repository` | `myMarque` root DI |
| `UserRepository` | `com.base.domain.repository` | `myMarque` root DI |
| `SharedPreferenceRepository` | `com.base.domain.repository` | `myMarque` root DI |
| `PIMSTripNDriveRepository` | `com.base.domain.repository` | `myMarque` root DI |

These are available via `by inject()` in any Fragment (already in BaseFragment), or via `get()` in any Koin module.

### DI Loading
| Class | Package | Purpose |
|-------|---------|---------|
| `DelegateDI` | `com.base.framework.di` | `loadDiModules(list)` → wraps `loadKoinModules()` |

### Logging
| Class | Package | Usage |
|-------|---------|-------|
| `MyMLogger` | `com.psa.logger` | `MyMLogger.i/d/e/w(msg)` — mandatory |

---

## 3. Feature Module Standard Packages

Each module follows this package convention:

```
com.psa.mym.[module]/          ← feature-specific code
com.base.[domain]/             ← shared domain (in myMarque)
com.base.framework.di/         ← DI infrastructure (in myMarque)
```

Examples:
- `com.psa.mym.remote.domain.usecases.InitRemoteVehicleUseCase`
- `com.psa.mym.plp.domain.repositories.PLPRepository`
- `com.psa.mym.dealer.view.viewmodel.DealerLocatorViewModel`
- `com.base.domain.usecases.GetSelectedCarUseCase` (shared, in myMarque)

---

## 4. Cross-Module Dependency Rules

### Allowed Dependencies
```
feature modules → myMarque (base classes, shared domain, shared repos)
feature modules → logger (MyMLogger)
feature modules → bOUserMyMarque (BO models)
feature modules → btaCarProtocolLib (car protocol, if needed)
```

### Forbidden Dependencies
```
myMarque → feature modules (would create circular dependency)
feature module A → feature module B (horizontal dependency — use shared domain instead)
any module → psa/security (restricted — lead approval required)
```

### When a Feature Needs Data From Another Feature
Use a shared repository interface in `myMarque/domain/repository/` or `com.base.features.*` package. Never import directly from another feature module.

---

## 5. Where to Place a New Feature

| Feature Type | Module to use |
|-------------|---------------|
| Vehicle remote commands (charge, climate, etc.) | `remote/` |
| Vehicle info, VIN, car registration | `vehicle/` |
| Dealer search, appointment | `dealer/` |
| User account, profile, biometrics | `userProfile/` |
| Loyalty program, accessories | `plp/` |
| EV route planning | `evrouting/` |
| System-wide shared entity or repository | `myMarque/` (base packages) |
| New standalone feature | Create new module (discuss with lead first) |

---

## 6. Module Structure Within Each Feature Module

```
[module]/
├── build.gradle
├── src/
│   ├── main/
│   │   └── java/com/psa/mym/[module]/
│   │       ├── [feature]/
│   │       │   ├── domain/
│   │       │   │   ├── model/
│   │       │   │   ├── repository/
│   │       │   │   └── usecase/
│   │       │   ├── data/
│   │       │   │   ├── datasource/
│   │       │   │   ├── repository/
│   │       │   │   └── mapper/
│   │       │   └── view/
│   │       │       ├── viewmodel/
│   │       │       ├── fragments/
│   │       │       └── adapter/
│   │       └── framework/
│   │           └── di/              ← Koin modules
│   └── test/
│       └── java/com/psa/mym/[module]/
│           └── [feature]/           ← Test files mirror main structure
```

---

## 7. Build Variants / Flavors

The app has multiple build variants (see `flavors.gradle`). When implementing a new feature:
- Check if the feature applies to all flavors or specific ones
- If flavor-specific, check for flavor source sets
- Features in `myMarque` apply to all flavors
- Ask the tech lead before adding flavor-specific logic in shared modules
