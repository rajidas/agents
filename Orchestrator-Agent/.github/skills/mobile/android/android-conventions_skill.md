---
name: android-conventions
description: >-
  Apply mandatory Android team conventions for Fragments, ViewModels, logging, ViewBinding,
  navigation, and naming. Use when implementing or reviewing any Android UI layer code.
---

# Purpose
Defines the mandatory patterns every Fragment, ViewModel, and supporting class must follow in this project, including base class inheritance, lifecycle hooks, logging, and naming.

# When to use
- Implementing a new Fragment or ViewModel
- Reviewing a PR for convention compliance
- Adding navigation between screens
- Unsure how to use ViewBinding, sealed state classes, or `MyMLogger`
- **Not for:** DI wiring (see `koin-di` skill), layer placement (see `clean-architecture` skill), or unit tests (see `unit-testing` skill)

# Procedure
1. Fragments **must** extend `BaseFragment<VM>` — implement `initViews()`, `initObservers()`, and `getModulesList()`.
2. ViewModels **must** extend `BaseFragmentViewModel` — expose immutable `LiveData`, call use cases from `viewReady()`.
3. Use `MyMLogger` for all log output — never `android.util.Log` or `println`.
4. Use ViewBinding — inflate in `onCreateView`, null in `onDestroyView`.
5. Navigate via `FragmentsHelper` — never call `beginTransaction()` directly in feature fragments.
6. Define UI state as a sealed class with at least `Loading`, `Success`, and `Error` variants.
7. Follow the **Naming Conventions Quick Reference** table at the bottom of this file.

# Inputs & Outputs
- **Inputs:** Feature name; Fragment layout XML file already created; domain model from Domain layer
- **Outputs:** `[Feature]Fragment.kt`, `[Feature]FragmentViewModel.kt`, `[Feature]State.kt`; navigation call via `FragmentsHelper`

# Examples
**User ask:** "Create a Fragment for the vehicle status screen."
**Assistant plan:** Extend `BaseFragment<VehicleStatusFragmentViewModel>`; implement `initViews` for click listeners; `initObservers` for state rendering; `getModulesList` returning the feature Koin module.
**Expected artifacts:** `VehicleStatusFragment.kt`, `VehicleStatusFragmentViewModel.kt`, `VehicleStatusState.kt`.

**User ask:** "How do I log an error in my ViewModel?"
**Assistant plan:** Import `com.psa.logger.MyMLogger`; call `MyMLogger.e(exception, "context message")`.
**Expected artifacts:** Corrected log call replacing any `Log.e(...)` usage.

# Notes & Guardrails
- `initViews()` and `initObservers()` are called by `BaseFragment.onViewCreated()` — do not call them manually.
- `viewReady()` is called by `BaseFragment` after `initObservers()` — do not call it manually.
- Always null `_binding` in `onDestroyView()` to prevent memory leaks.
- Never expose `MutableLiveData` publicly — use the private `_x` / public `x: LiveData` pattern.

---

# Android Team Conventions
> Load this skill when implementing, reviewing, or validating Android code in this repository.

---

## 1. BaseFragment — Mandatory Contract

All fragments **must** extend `BaseFragment<VM>` from `com.base.view.fragments.BaseFragment`.

### Full Pattern
```kotlin
class FeatureFragment : BaseFragment<FeatureFragmentViewModel>(FeatureFragmentViewModel::class) {

    private var _binding: FragmentFeatureBinding? = null
    private val binding get() = _binding!!

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?
    ): View {
        _binding = FragmentFeatureBinding.inflate(inflater, container, false)
        return binding.root
    }

    // ✅ MANDATORY — Abstract in BaseFragment
    override fun initViews(view: View) {
        // UI setup ONLY: click listeners, adapter attachment, initial visibility
        // ❌ NO business logic
        // ❌ NO data fetching
    }

    // ✅ MANDATORY — Abstract in BaseFragment
    override fun initObservers() {
        // LiveData observations ONLY
        // ❌ NO data transformations
        // ❌ NO business decisions
        baseFragmentViewModel.state.observe(viewLifecycleOwner) { state ->
            renderState(state)
        }
    }

    // ✅ MANDATORY — Return the feature's Koin module
    override fun getModulesList() = listOf(featureModule)

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null  // ✅ Always null to prevent memory leaks
    }

    private fun renderState(state: FeatureState) {
        when (state) {
            is FeatureState.Loading -> { /* show loading */ }
            is FeatureState.Success -> { /* bind data to views */ }
            is FeatureState.Error -> { /* show error */ }
        }
    }
}
```

### Key Rules
- `initViews()` and `initObservers()` are **called by `BaseFragment.onViewCreated()`** — do not call manually
- `baseFragmentViewModel.viewReady()` is **called by BaseFragment** after `initObservers()` — do not call manually
- `getModulesList()` triggers `DelegateDI.loadDiModules()` via `loadKoinModules()` — this is how feature DI modules are loaded
- `showLoader()` / `hideLoader()` are available from `BaseFragmentViewModel` — use them, don't reinvent

---

## 2. BaseFragmentViewModel — Mandatory Contract

All ViewModels **must** extend `BaseFragmentViewModel` from `com.base.view.fragments.BaseFragmentViewModel`.

### Inheritance Chain
`BaseFragmentViewModel` → `BaseViewModel` → `ViewModel` + `KoinComponent`

### BaseViewModel provides (available to all ViewModels)
```kotlin
// Analytics
fun pushOpenScreenEvent(taggingData: TaggingData)
fun pushClickEvent(taggingData: TaggingData)
fun pushCustomEvent(taggingData: TaggingData)

// Coroutine helper
fun BaseViewModel.launch(dispatchers: CoroutineDispatcher = Dispatchers.Main, request: suspend CoroutineScope.() -> Unit)

// Vehicle change
val vehicleChange: LiveData<Unit>
open fun onVehicleChange()

// Error channel
open fun getErrorObservable(): Channel<ErrorWrapper>
```

### BaseFragmentViewModel provides
```kotlin
val showLoader: LiveData<Boolean>   // observed by BaseFragment
fun showLoader()
fun hideLoader()
val pimsVinState: LiveData<PimsAndVinState>
abstract fun viewReady()            // called by BaseFragment.onViewCreated()
```

### Full Pattern
```kotlin
class FeatureFragmentViewModel(
    private val getFeatureDataUseCase: GetFeatureDataUseCase,
    private val saveFeatureUseCase: SaveFeatureUseCase
) : BaseFragmentViewModel() {

    private val _state = MutableLiveData<FeatureState>()
    val state: LiveData<FeatureState> = _state  // ✅ Private MutableLiveData, public LiveData

    override fun viewReady() {
        loadData()
    }

    private fun loadData() {
        showLoader()
        viewModelScope.launch {
            val result = getFeatureDataUseCase()
            result.fold(
                onSuccess = { data -> _state.postValue(FeatureState.Success(data)) },
                onFailure = { error ->
                    MyMLogger.e(error as Exception, "FeatureFragmentViewModel - loadData failed")
                    _state.postValue(FeatureState.Error(error.message ?: "Unknown error"))
                }
            )
            hideLoader()
        }
    }

    fun onSaveClicked(data: FeatureData) {
        viewModelScope.launch {
            val result = saveFeatureUseCase(data)
            // handle result
        }
    }
}
```

### Key Rules
- ✅ **Always** use `viewModelScope.launch` for coroutines
- ✅ **Always** use `private val _x: MutableLiveData` + `val x: LiveData`
- ❌ **Never** inject `Repository` directly — only `UseCase` classes
- ❌ **Never** import `android.util.Log` — use `MyMLogger`
- ❌ **Never** do data transformation here — mappers belong in the Data layer

---

## 3. Logging — MyMLogger

**Import:** `com.psa.logger.MyMLogger`  
**Never use:** `android.util.Log`, `println()`

```kotlin
// ✅ Correct usage
MyMLogger.d("FeatureViewModel - viewReady called")
MyMLogger.i("FeatureFragment - user tapped confirm button")
MyMLogger.w("FeatureRepositoryImpl - cache miss, fetching remote")
MyMLogger.e(exception, "FeatureRepositoryImpl - getData failed: ${exception.message}")

// With LogComponentName (when available in module)
MyMLogger.d(LogComponentName.REMOTE, "Remote vehicle type = $vehicleType")

// ❌ Wrong
Log.d("TAG", "message")
Log.e("TAG", "error", exception)
println("debug output")
```

---

## 4. ViewBinding

```kotlin
// ✅ Correct — inflate in onCreateView, null in onDestroyView
private var _binding: FragmentFeatureBinding? = null
private val binding get() = _binding!!

override fun onCreateView(...): View {
    _binding = FragmentFeatureBinding.inflate(inflater, container, false)
    return binding.root
}

override fun onDestroyView() {
    super.onDestroyView()
    _binding = null
}

// Usage
binding.featureTitle.text = "Hello"
binding.confirmButton.setOnClickListener { /* ... */ }

// ❌ Wrong
val title = view.findViewById<TextView>(R.id.featureTitle)
```

---

## 5. State with Sealed Classes

```kotlin
sealed class FeatureState {
    object Loading : FeatureState()
    data class Success(val data: FeatureModel) : FeatureState()
    data class Error(val message: String) : FeatureState()
    object Empty : FeatureState()
    object Idle : FeatureState()
}
```

---

## 6. Navigation

Navigation must be described as a call via the project's navigation helper (FragmentsHelper), **never** via direct `supportFragmentManager.beginTransaction()` in feature fragments.

```kotlin
// ✅ Describe navigation like this (search for FragmentsHelper in the module before implementing)
FragmentsHelper.navigate(requireActivity(), TargetFragment.newInstance(args))

// ❌ Never in feature code
parentFragmentManager.beginTransaction()
    .replace(R.id.container, TargetFragment())
    .commit()
```

---

## 7. Naming Conventions Quick Reference

| Component | Pattern | Example |
|-----------|---------|---------|
| Domain model | `[Noun]` | `VehicleStatus` |
| Repository interface | `[Noun]Repository` | `VehicleStatusRepository` |
| Use Case | `[Verb][Noun]UseCase` | `GetVehicleStatusUseCase` |
| Data model/DTO | `[Noun]Dto` | `VehicleStatusDto` |
| Mapper | extension on DTO | `VehicleStatusDto.toDomain()` |
| ViewModel | `[Feature]FragmentViewModel` | `VehicleStatusFragmentViewModel` |
| Fragment | `[Feature]Fragment` | `VehicleStatusFragment` |
| State | `[Feature]State` | `VehicleStatusState` |
| Koin module val | `[feature]Module` | `vehicleStatusModule` |
| Layout file | `fragment_[feature]` | `fragment_vehicle_status.xml` |
