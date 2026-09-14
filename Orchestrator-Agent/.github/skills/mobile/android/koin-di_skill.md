---
name: koin-di
description: >-
  Define, wire, and review Koin DI modules for Android features in this repository.
  Use when creating a new feature module, injecting dependencies, or debugging missing Koin bindings.
---

# Purpose
Provides the standard patterns for Koin module definition, scoping rules, cross-module injection, and fragment module loading used across every feature in this project.

# When to use
- Creating a new feature's `di/FeatureModule.kt`
- Adding a new UseCase, Repository, or ViewModel to an existing Koin module
- Debugging a `NoBeanDefFoundException` or missing dependency at runtime
- Reviewing whether a dependency is scoped correctly (`factory` vs `single` vs `viewModel`)
- **Not for:** layer placement decisions (see `clean-architecture` skill) or base class usage (see `android-conventions` skill)

# Procedure
1. Create three `module {}` vals per feature: `vmFeatureModules`, `domainFeatureModules`, `dataFeatureModules`.
2. Declare ViewModels with `viewModel {}`, UseCases and DataSources with `factory {}`, singletons with `single {}` only when shared state is needed.
3. Always bind Repository implementations to their interface: `factory<FeatureRepository> { FeatureRepositoryImpl(get(), get()) }`.
4. Use named parameters in `viewModel {}` blocks for readability.
5. Override `getModulesList()` in the Fragment to return the feature's modules — this triggers `DelegateDI.loadDiModules()`.
6. Cross-module deps (e.g. `CarRepository` from `myMarque`) are already in the Koin graph — just call `get()`; do not re-declare them.

# Inputs & Outputs
- **Inputs:** Feature class names (ViewModel, UseCases, Repository interface + impl, DataSources)
- **Outputs:** `di/FeatureModule.kt` with correct `viewModel {}`, `factory {}`, and optional `single {}` declarations; updated Fragment `getModulesList()`

# Examples
**User ask:** "Wire up `GetVehicleStatusUseCase` and `VehicleStatusFragmentViewModel` with Koin."
**Assistant plan:** `factory { GetVehicleStatusUseCase(vehicleStatusRepository = get()) }` in domain module; `viewModel { VehicleStatusFragmentViewModel(getVehicleStatusUseCase = get()) }` in vm module.
**Expected artifacts:** Updated `FeatureModule.kt`; `getModulesList()` returning the module list.

**User ask:** "I'm getting `NoBeanDefFoundException` for `FeatureRepository`."
**Assistant plan:** Check if `factory<FeatureRepository> { FeatureRepositoryImpl(...) }` is present and that `getModulesList()` includes the data module.
**Expected artifacts:** Fix in the Koin module and/or `getModulesList()` override.

# Notes & Guardrails
- Never use `single {}` for stateless UseCases or Repositories — use `factory {}`.
- Never inject a Repository directly into a ViewModel via the Koin module — inject the UseCase instead.
- Do not re-declare app-level singletons (`CarRepository`, `UserRepository`) already registered in `myMarque` root modules.
- `DelegateDI.loadDiModules()` is called once per Fragment lifecycle — do not call `loadKoinModules()` manually.

---

# Koin Dependency Injection Patterns
> Load this skill when creating or reviewing Koin DI modules in this repository.

**Koin version:** 3.1.2  
**Key imports:** `org.koin.dsl.module`, `org.koin.androidx.viewmodel.dsl.viewModel`, `org.koin.android.ext.android.inject`, `org.koin.androidx.viewmodel.ext.android.getViewModel`

---

## 1. Module Definition Patterns

### Standard Feature Module Naming

```kotlin
// Convention: 3 vals per feature module — vm, domain, data
val vmFeatureModules = module { ... }        // ViewModels
val domainFeatureModules = module { ... }    // UseCases + Repository interfaces
val dataFeatureModules = module { ... }      // DataSources + Repository implementations

fun getFeatureModules() = listOf(vmFeatureModules, domainFeatureModules, dataFeatureModules)
```

### viewModel {} — For ViewModels
```kotlin
val vmFeatureModules = module {
    // ✅ Named parameters — always explicit for readability
    viewModel {
        FeatureFragmentViewModel(
            getFeatureDataUseCase = get(),
            saveFeatureUseCase = get()
        )
    }

    // ✅ Multiple ViewModels in one module (related features)
    viewModel { FeatureListFragmentViewModel(getFeatureListUseCase = get()) }
    viewModel { FeatureDetailFragmentViewModel(getFeatureDetailUseCase = get()) }
}
```

### factory {} — For UseCases and DataSources (new instance each time)
```kotlin
val domainFeatureModules = module {
    // ✅ UseCase — always factory (stateless)
    factory { GetFeatureDataUseCase(featureRepository = get()) }
    factory { SaveFeatureDataUseCase(featureRepository = get()) }

    // ✅ Bind interface to implementation
    factory<FeatureRepository> { FeatureRepositoryImpl(get(), get()) }

    // ✅ Interface binding with explicit factory type
    factory<IGetFeatureUseCase> { GetFeatureDataUseCase(featureRepository = get()) }
}
```

### single {} — For Singletons (use sparingly)
```kotlin
// ✅ Only for shared state, session-level managers, or observables that must be shared
single<IGetRemoteNotificationsStatusUseCase> {
    GetRemoteNotificationsStatusUseCase(
        getRemoteNotificationSettingsUseCase = get(),
        // ...
    )
}

// ❌ Don't use single for regular UseCases — they should be factory
```

### Real Project Example (PLPModules.kt)
```kotlin
val vmPLPModules = module {
    viewModel { PLPAccessoriesViewModel(getPLPAccessoriesUseCase = get(), isPLPMemberUseCase = get(), getPLPAccessoriesStoreUrlUseCase = get()) }
    viewModel { VehicleCourtesyViewModel(getVehicleCourtesyUseCase = get()) }
}

val domainPLPModules = module {
    factory<IIsPLPMemberUseCase> { IsPLPMemberUseCase(loyaltyRepository = get()) }
    factory { GetPLPAccessoriesUseCase(plpRepository = get(), allServicesDataUseCase = get()) }
    factory<PLPRepository> { PLPRepositoryImpl(plpLocalDataSource = get(), plpDataSource = get()) }
    factory { ConfirmPLPTermsAndConditionUseCase(plpRepository = get()) }
}

val dataPLPModules = module {
    // DataSources go here
}
```

---

## 2. Loading Modules in Fragments

The project uses `DelegateDI.loadDiModules()` (wraps `loadKoinModules()`), called automatically by `BaseFragment` via `getModulesList()`.

```kotlin
// ✅ Fragment must override getModulesList
class FeatureFragment : BaseFragment<FeatureFragmentViewModel>(FeatureFragmentViewModel::class) {

    override fun getModulesList() = listOf(featureModule)  // ← single module val

    // Or multiple modules for a complex feature
    override fun getModulesList() = getFeatureModules()    // ← function returning list
}

// ✅ The DelegateDI mechanism (don't modify this)
object DelegateDI {
    fun loadDiModules(list: List<Module>) = loadKoinModules(list)
}
// BaseFragment.onCreate() → injectFeatures() → loadDiModules(getModulesList())
```

---

## 3. Injecting Dependencies

### In ViewModels — Constructor injection via Koin module
```kotlin
// ✅ Dependencies come from Koin module — no @Inject needed
class FeatureFragmentViewModel(
    private val getFeatureUseCase: GetFeatureUseCase
) : BaseFragmentViewModel() { ... }

// ✅ In Koin module
viewModel { FeatureFragmentViewModel(get()) }
```

### In Fragments — Lazy injection
```kotlin
// ✅ For shared dependencies (available in root Koin graph)
private val carRepository: CarRepository by inject()    // from BaseFragment

// ✅ For ViewModel — handled by BaseFragment automatically
// baseFragmentViewModel is set in BaseFragment.onCreate() via getViewModel(clazz = vmClazz)
```

### In Other Classes (KoinComponent)
```kotlin
// ✅ When class extends KoinComponent (like BaseViewModel)
class SomeClass : KoinComponent {
    private val dependency: SomeDependency by inject()
}
```

---

## 4. Scoping Guidelines

| Component | Koin scope | Reason |
|-----------|-----------|--------|
| ViewModel | `viewModel {}` | Scoped to Fragment lifecycle by Koin |
| UseCase | `factory {}` | Stateless, new instance is fine |
| Repository impl | `factory {}` | Usually stateless |
| DataSource | `factory {}` | Usually stateless |
| Session manager / observable | `single {}` | Must share state across features |
| App-level repository | `single {}` | If already defined in root module |

---

## 5. Cross-Module Dependencies

When a module needs a dependency defined in another module (e.g., `remote` needs `CarRepository` from `myMarque`):

```kotlin
// ✅ Just use get() — Koin resolves across all loaded modules
factory { GetVehicleStatusUseCase(carRepository = get()) }
// carRepository is registered in myMarque's root modules — already in Koin graph

// ❌ Don't re-declare already-registered dependencies in your module
factory<CarRepository> { CarRepositoryImpl(...) }  // if already registered in myMarque
```

---

## 6. Common Mistakes to Avoid

```kotlin
// ❌ Missing interface binding — exposes impl type
factory { FeatureRepositoryImpl(get(), get()) }
// ✅ Correct
factory<FeatureRepository> { FeatureRepositoryImpl(get(), get()) }

// ❌ Using single for UseCase
single { GetFeatureDataUseCase(get()) }
// ✅ Correct
factory { GetFeatureDataUseCase(get()) }

// ❌ Not returning module in getModulesList()
override fun getModulesList() = emptyList<Module>()  // breaks DI for this fragment
// ✅ Correct
override fun getModulesList() = listOf(featureModule)

// ❌ Injecting repository directly in ViewModel
viewModel { FeatureViewModel(featureRepository = get()) }  // bypasses UseCase layer
// ✅ Correct
viewModel { FeatureViewModel(getFeatureDataUseCase = get()) }
```
