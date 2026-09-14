---
name: clean-architecture
description: >-
  Design and implement Clean Architecture layers for Android features in this repository.
  Use when creating a new feature, reviewing layer placement, or enforcing domain/data/presentation boundaries.
---

# Purpose
Defines the three-layer architecture (Domain / Data / Presentation), the contracts between them, and the mandatory folder structure every feature module must follow.

# When to use
- Creating a new feature module or feature package
- Reviewing where a class belongs (model, repository, use case, ViewModel, Fragment)
- Auditing dependency direction (Domain must not import Android; Data must implement Domain interfaces)
- Designing a new repository interface or use case
- **Writing a Layer Impact Summary** for the Spec Analyzer output (see Section 11)
- **Not for:** DI module wiring (see `koin-di` skill), test structure (see `unit-testing` skill), or team conventions (see `android-conventions` skill)

# Procedure
1. Identify the feature and decide which module it belongs to (consult `module-map` skill).
2. Create the folder structure listed in Section 10 before writing any code.
3. Start from the Domain layer: define the model, repository interface, and use case(s).
4. Implement the Data layer: data source(s), repository impl, and mappers.
5. Implement the Presentation layer: ViewModel (calling use cases only), Fragment (rendering state only).
6. Wire everything with a Koin module in `di/FeatureModule.kt`.
7. Verify dependency direction: Presentation → Domain ← Data. Domain imports Kotlin only.

# Inputs & Outputs
- **Inputs:** Feature requirements; module name; domain entity description
- **Outputs:** Populated `domain/`, `data/`, `view/`, `di/` folders with correct classes per the patterns below

# Examples
**User ask:** "Add a screen that shows vehicle status."
**Assistant plan:** Define `VehicleStatus` domain model → `VehicleStatusRepository` interface → `GetVehicleStatusUseCase` → `VehicleStatusRepositoryImpl` with mapper → `VehicleStatusFragmentViewModel` → `VehicleStatusFragment`.
**Expected artifacts:** Files across all four folders; Koin module; no Android imports in `domain/`.

**User ask:** "Can the repository return a Retrofit `Response<T>`?"
**Assistant plan:** No — cite Section 3. Repository interface must return domain models only. Map inside the repository implementation.
**Expected artifacts:** Corrected interface + mapper in `data/mapper/`.

**User ask (Spec Analyzer mode):** "Summarize what each layer does for a new KPI tracking feature."
**Assistant plan:** Use Section 11 to write one plain-language paragraph per layer — no class names, no code. Describe intent and responsibility only.
**Expected artifacts:** A `🏗️ Layer Impact Summary` block with four plain-English paragraphs.

# Notes & Guardrails
- Domain layer: zero `android.*` / `androidx.*` imports — enforced via grep (see `soc-violations` V3).
- Never skip the use case layer, even for trivial pass-through operations.
- God UseCases (multiple unrelated actions) are a V12 violation — split them.
- `psa/security` module is restricted; do not restructure it without lead approval.

---

# Clean Architecture — Layer Rules & Patterns
> Load this skill when designing, implementing, or reviewing feature architecture in this repository.

---

## 1. Layer Responsibilities

### Domain Layer — Pure Kotlin, Zero Android
The heart of the application. Contains all business rules.

**Allowed imports:** Kotlin stdlib only. `Result`, `kotlinx.coroutines`, custom domain entities.  
**Forbidden:** `android.*`, `androidx.*`, `Context`, `Activity`, `Fragment`, `LiveData`, `MutableLiveData`

```
domain/
├── model/       ← Business entities (pure data classes)
├── repository/  ← Interfaces only (no implementations here)
└── usecase/     ← One class = one action
```

### Data Layer — Implements Domain Contracts
Bridges the domain with the real world (network, database, preferences).

**Allowed imports:** Android framework, Retrofit, Room, Gson, etc.  
**Rule:** Always maps external data to domain models via mapper functions.

```
data/
├── datasource/  ← Remote (Retrofit) and Local (Room/Preferences)
├── repository/  ← Implements domain repository interfaces
└── mapper/      ← Extension functions: DataModel.toDomain()
```

### Presentation Layer — Renders UI State
Displays data and captures user interactions. Zero business logic.

**Rule:** Fragments/Activities observe LiveData and render it. ViewModels orchestrate UseCases.

```
view/
├── viewmodel/   ← Extends BaseFragmentViewModel, calls UseCases only
├── fragments/   ← Extends BaseFragment<VM>, renders state only
├── activity/    ← Activities (use sparingly)
└── adapter/     ← RecyclerView adapters (no business logic)
```

---

## 2. Domain Model Pattern

```kotlin
// ✅ Pure Kotlin data class — zero Android imports
data class VehicleStatus(
    val vin: String,
    val batteryLevel: Int,
    val isCharging: Boolean,
    val rangeKm: Double
)

// Sealed result type used across the project
// com.base.utils.Result<T> — use this project's Result wrapper
```

---

## 3. Repository Interface Pattern

```kotlin
// ✅ In domain/repository/ — interface ONLY, no implementation
interface VehicleStatusRepository {
    suspend fun getVehicleStatus(vin: String): Result<VehicleStatus>
    suspend fun refreshVehicleStatus(vin: String): Result<Unit>
    fun observeVehicleStatus(): Flow<VehicleStatus?>
}

// ❌ No Android imports
// ❌ No Retrofit types (Response<T>, Call<T>)
// ❌ No Room @Dao references
```

---

## 4. UseCase Pattern (Single Responsibility)

```kotlin
// ✅ One action per class — use operator fun invoke() when single function
class GetVehicleStatusUseCase(
    private val vehicleStatusRepository: VehicleStatusRepository  // interface, not impl
) {
    suspend operator fun invoke(vin: String): Result<VehicleStatus> {
        MyMLogger.i("GetVehicleStatusUseCase - fetching status for vin=$vin")
        return vehicleStatusRepository.getVehicleStatus(vin)
    }
}

// ✅ Named function when UseCase has contextual meaning
class RefreshVehicleStatusUseCase(
    private val repo: VehicleStatusRepository
) {
    suspend fun execute(vin: String): Result<Unit> = repo.refreshVehicleStatus(vin)
}

// ❌ God UseCase — don't do this
class VehicleStatusUseCase {
    fun getStatus() { ... }
    fun refreshStatus() { ... }
    fun saveStatus() { ... }  // ← This should be 3 separate UseCases
}
```

**Real project example** (`InitRemoteVehicleUseCase`):
```kotlin
class InitRemoteVehicleUseCase(
    private val remoteCommandRepository: RemoteCommandRepository,
    private val configureRemoteVehicleUseCase: ConfigureRemoteVehicleUseCase,
    private val getRemoteVehicleInfoUseCase: IGetRemoteVehicleInfoUseCase,
    // ... injected via Koin
) : IInitRemoteVehicleUseCase {
    override suspend operator fun invoke() {
        MyMLogger.d(LogComponentName.REMOTE, "==> InitRemoteVehicleUseCase invoked")
        // delegates to other use cases, repository — no business logic inline
    }
}
```

---

## 5. Repository Implementation Pattern

```kotlin
// ✅ In data/repository/ — implements domain interface
class VehicleStatusRepositoryImpl(
    private val remoteDataSource: VehicleStatusRemoteDataSource,
    private val localDataSource: VehicleStatusLocalDataSource
) : VehicleStatusRepository {

    override suspend fun getVehicleStatus(vin: String): Result<VehicleStatus> {
        return try {
            val dto = remoteDataSource.fetchVehicleStatus(vin)
            Result.success(dto.toDomain())           // ← always map to domain
        } catch (e: Exception) {
            MyMLogger.e(e, "VehicleStatusRepositoryImpl - getVehicleStatus failed")
            Result.failure(e)
        }
    }
}
```

---

## 6. Mapper Pattern

```kotlin
// ✅ In data/mapper/ — extension functions on data models
fun VehicleStatusDto.toDomain(): VehicleStatus = VehicleStatus(
    vin = this.vin ?: "",
    batteryLevel = this.battery?.level ?: 0,
    isCharging = this.charging?.active ?: false,
    rangeKm = this.range?.estimatedKm ?: 0.0
)

// Reverse mapper (domain → data) when saving
fun VehicleStatus.toDto(): VehicleStatusDto = VehicleStatusDto(
    vin = this.vin,
    // ...
)
```

---

## 7. Dependency Direction Rule

Dependencies must **only flow inward**:

```
Presentation → Domain ← Data
     ↓               ↑
  Uses Interfaces   Implements Interfaces
```

- Presentation depends on Domain (through ViewModels calling UseCases)
- Data depends on Domain (implementing Repository interfaces)
- Domain depends on **nothing** (pure Kotlin)

---

## 8. Data Flow — Unidirectional

```
User Action
    ↓
Fragment.initObservers() / click listener
    ↓
ViewModel.onUserAction()
    ↓
UseCase.invoke()
    ↓
Repository interface
    ↓
DataSource (network/db)
    ↓ (mapped via mapper)
Domain Model
    ↓
Result<DomainModel>
    ↓ (back up the chain)
ViewModel posts to LiveData
    ↓
Fragment observes and renders
```

---

## 9. Interface Typing in Koin

When multiple implementations exist (or to enforce abstraction), always bind by interface:

```kotlin
// ✅ Correct — typed to interface
factory<VehicleStatusRepository> { VehicleStatusRepositoryImpl(get(), get()) }
factory<IGetRemoteVehicleInfoUseCase> { GetRemoteVehicleInfoUseCase(get(), get()) }

// ✅ Correct — when only one impl and no interface needed
factory { SwitchChargeUseCase(remoteCommandRepository = get()) }

// ❌ Wrong — exposes concrete type when interface exists
factory { VehicleStatusRepositoryImpl(get(), get()) }  // if interface exists
```

---

## 11. Layer Impact Summary — Plain Language Mode

> Use this section **only when operating as the Spec Analyzer**. Do NOT write code or class names. Describe intent in 2–3 sentences per layer so that a product manager, designer, or tester can understand what will change and why.

### How to describe each layer

**Domain**
Describe what new concept or rule is being introduced into the app's core logic. Focus on *what the app will now know how to do* that it couldn't before. Mention whether an existing concept is extended or a new one is created.

> Example: *"We introduce the concept of a login attempt lifecycle into the app's business rules. The app will now understand the different stages a login can go through — from the user tapping the button all the way to the home screen appearing — and will know how to describe what happened at each stage."*

**Data**
Describe where the information comes from and how it travels. Mention whether it is sent to a remote service, stored locally, or both. Avoid mentioning specific technologies.

> Example: *"Each login step event will be sent to the monitoring service in real time. If the user is not yet logged in or if sending fails, the event is saved locally and will be sent automatically the next time a connection is available."*

**Presentation**
Describe what changes (if anything) from the user's perspective. If this layer is not impacted, say so explicitly.

> Example: *"No visible change to the user interface. This feature runs silently in the background — the user will not see any new screens or buttons."*

**Dependency Injection**
One sentence: describe what new building blocks need to be registered so the app can assemble the feature at runtime.

> Example: *"The new tracking service, its storage component, and the three event-sending actions all need to be registered so the app can find and connect them automatically."*

### Rules for this mode
- ❌ No class names, method names, or interface names
- ❌ No code snippets of any kind
- ✅ Max 3 sentences per layer
- ✅ Write as if explaining to a product manager who knows the product but not the code
- ✅ If a layer is not affected, write: *"No changes required in this layer."*

---

## 10. Mandatory Folder Structure Per Feature

```
[module]/src/main/java/[package]/[feature]/
├── domain/
│   ├── model/
│   │   └── FeatureModel.kt
│   ├── repository/
│   │   └── FeatureRepository.kt
│   └── usecase/
│       ├── GetFeatureDataUseCase.kt
│       └── SaveFeatureDataUseCase.kt
├── data/
│   ├── datasource/
│   │   ├── FeatureRemoteDataSource.kt
│   │   └── FeatureLocalDataSource.kt
│   ├── repository/
│   │   └── FeatureRepositoryImpl.kt
│   └── mapper/
│       └── FeatureMappers.kt
├── view/
│   ├── viewmodel/
│   │   └── FeatureFragmentViewModel.kt
│   ├── fragments/
│   │   └── FeatureFragment.kt
│   └── adapter/
│       └── FeatureAdapter.kt
└── di/
    └── FeatureModule.kt
```
