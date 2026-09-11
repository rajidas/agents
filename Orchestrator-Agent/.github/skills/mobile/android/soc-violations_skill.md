---
name: soc-violations
description: >-
  Detect and fix separation-of-concerns violations in this Android codebase.
  Use when scanning, reviewing, or correcting architecture boundary breaches across
  Fragment/ViewModel/UseCase/Repository/Domain layers.
---

# Purpose
Catalog of the 12 recurring SoC violation types in this project. Each entry provides detection patterns, bad/good code examples, and a fix strategy.

# When to use
- Reviewing a PR for architecture compliance
- Running a pre-merge architecture scan
- Fixing a violation flagged in a review comment
- Unsure which layer owns a responsibility during implementation
- **Not for:** styling, naming, or build configuration issues

# Procedure
1. Run the Quick Detection Commands at the bottom of this file to identify violations by grep.
2. Match each hit to the corresponding violation entry (V1–V12) and apply the listed fix pattern.
3. CRITICAL violations (V1–V4) must be resolved before PR approval.
4. HIGH violations (V5–V8) must be fixed before merge.
5. MEDIUM violations (V9–V12) should be fixed (tracked as tech debt if deferred).

# Inputs & Outputs
- **Inputs:** Kotlin source files under review; PR diff; module path to scan
- **Outputs:** Fix commits resolving violations; review comments citing violation ID (e.g. `V1 – business logic in Fragment`)

# Examples
**User ask:** "Is there any business logic in the fragment?"
**Assistant plan:** Run V1 check patterns; inspect `initObservers` and `initViews` for filtering/sorting/computation.
**Expected artifacts:** Inline code fix moving logic to a UseCase, updated ViewModel exposure.

**User ask:** "Why shouldn't I call the repository directly from my ViewModel?"
**Assistant plan:** Cite V2; show correct UseCase indirection pattern.
**Expected artifacts:** Refactored ViewModel constructor + Koin module update.

# Notes & Guardrails
- Never approve a PR with a V1–V4 violation, regardless of deadline pressure.
- When in doubt about layer placement, favour pushing logic further into the Domain layer.
- `psa/security` module is restricted — do not propose fixes there without lead approval.

---

# SoC & Clean Architecture Violation Catalog
> Load this skill when scanning for, detecting, or fixing architecture violations in this repository.

This catalog covers the **12 violation types** that this team has identified as recurring issues. Each violation includes detection patterns, a bad/good code comparison, and a fix strategy.

---

## 🔴 CRITICAL — Blocks PR

### V1 — Business Logic in UI Layer (Fragment/Activity)

**What it looks like:**
```kotlin
// ❌ Data filtering in Fragment observer — belongs in UseCase
override fun initObservers() {
    viewModel.vehicles.observe(viewLifecycleOwner) { list ->
        val active = list.filter { it.serviceActive && it.batteryLevel > 20 }  // ← VIOLATION
        val sorted = active.sortedByDescending { it.rangeKm }                   // ← VIOLATION
        adapter.submitList(sorted)
    }
}

// ❌ Computation in Fragment
override fun initViews(view: View) {
    val total = viewModel.items.value?.sumOf { it.price } ?: 0  // ← VIOLATION
    binding.totalText.text = "Total: $total"
}

// ❌ Coroutine data fetch in Fragment
override fun initViews(view: View) {
    viewLifecycleOwner.lifecycleScope.launch {
        val result = repository.getData()  // ← DIRECT REPO CALL, VIOLATION
    }
}
```

**✅ Fix — Move logic to UseCase, expose pre-processed data from ViewModel:**
```kotlin
// UseCase handles filtering/sorting
class GetActiveVehiclesSortedByRangeUseCase(
    private val vehicleRepository: VehicleRepository
) {
    suspend operator fun invoke(): Result<List<VehicleStatus>> {
        return vehicleRepository.getAllVehicles().map { list ->
            list.filter { it.serviceActive && it.batteryLevel > 20 }
                .sortedByDescending { it.rangeKm }
        }
    }
}

// ViewModel exposes ready-to-display data
class VehicleListFragmentViewModel(
    private val getActiveVehiclesUseCase: GetActiveVehiclesSortedByRangeUseCase
) : BaseFragmentViewModel() {
    private val _vehicles = MutableLiveData<List<VehicleStatus>>()
    val vehicles: LiveData<List<VehicleStatus>> = _vehicles
}

// Fragment just renders
override fun initObservers() {
    baseFragmentViewModel.vehicles.observe(viewLifecycleOwner) { list ->
        adapter.submitList(list)  // ✅ No logic here
    }
}
```

---

### V2 — ViewModel Bypassing UseCase Layer

**What it looks like:**
```kotlin
// ❌ ViewModel directly injects and calls repository
class FeatureFragmentViewModel(
    private val featureRepository: FeatureRepository  // ← should be UseCase
) : BaseFragmentViewModel() {
    fun loadData() {
        viewModelScope.launch {
            val result = featureRepository.getData()  // ← BYPASS VIOLATION
        }
    }
}

// ❌ ViewModel injected with DataSource directly
class FeatureFragmentViewModel(
    private val remoteDataSource: FeatureRemoteDataSource  // ← data layer in viewmodel
)
```

**✅ Fix:**
```kotlin
class FeatureFragmentViewModel(
    private val getFeatureDataUseCase: GetFeatureDataUseCase  // ← UseCase only
) : BaseFragmentViewModel() {
    fun loadData() {
        viewModelScope.launch {
            val result = getFeatureDataUseCase()  // ✅ through UseCase
        }
    }
}
// In Koin module: viewModel { FeatureFragmentViewModel(get()) }
// NOT: viewModel { FeatureFragmentViewModel(featureRepository = get()) }
```

---

### V3 — Android Framework Imports in Domain Layer

**What it looks like:**
```kotlin
// ❌ Domain model with Android import
package com.psa.mym.feature.domain.model

import android.os.Parcelable          // ← VIOLATION
import androidx.lifecycle.LiveData    // ← VIOLATION
import android.content.Context        // ← VIOLATION
import kotlinx.parcelize.Parcelize    // ← VIOLATION (annotation processor)

@Parcelize
data class FeatureModel(val id: String) : Parcelable

// ❌ UseCase with Context
class GetFeatureDataUseCase(
    private val context: Context,     // ← VIOLATION
    private val repository: FeatureRepository
)
```

**✅ Fix — Pure Kotlin only in domain:**
```kotlin
package com.psa.mym.feature.domain.model

// ✅ No imports needed for pure data classes
data class FeatureModel(
    val id: String,
    val name: String,
    val isActive: Boolean
)

// ✅ UseCase has no Android deps
class GetFeatureDataUseCase(
    private val repository: FeatureRepository
) {
    suspend operator fun invoke(): Result<FeatureModel> = repository.getData()
}
```

**Scanner check:** Run `grep -r "import android\." [module]/src/main/java/[package]/[feature]/domain/` — any result is a violation.

---

### V4 — Missing Mapper Layer (Raw DTO in UI/Domain)

**What it looks like:**
```kotlin
// ❌ Repository returns raw DTO to ViewModel
interface FeatureRepository {
    suspend fun getData(): VehicleStatusDto  // ← DTO should not cross domain boundary
}

// ❌ ViewModel receives and displays DTO directly
binding.nameText.text = dto.vehicleInfo?.nameLabel ?: ""  // ← raw DTO in Fragment

// ❌ ViewModel maps the DTO (mapping is data layer concern)
val domain = FeatureModel(
    id = dto.id ?: "",
    name = dto.vehicleInfo?.nameLabel ?: ""  // ← mapping in ViewModel, VIOLATION
)
```

**✅ Fix:**
```kotlin
// data/mapper/FeatureMappers.kt
fun VehicleStatusDto.toDomain(): FeatureModel = FeatureModel(
    id = this.id ?: "",
    name = this.vehicleInfo?.nameLabel ?: ""
)

// data/repository/FeatureRepositoryImpl.kt
override suspend fun getData(): Result<FeatureModel> {
    val dto = remoteDataSource.fetchData()
    return Result.success(dto.toDomain())  // ✅ mapped here, domain model crosses boundary
}

// domain/repository/FeatureRepository.kt
interface FeatureRepository {
    suspend fun getData(): Result<FeatureModel>  // ✅ only domain model
}
```

---

## 🟠 HIGH — Should Fix Before PR

### V5 — Wrong Logging

**Detection:** Search for `Log.d`, `Log.e`, `Log.i`, `Log.w`, `Log.v`, `println`

```kotlin
// ❌ Wrong
import android.util.Log
Log.d("FeatureVM", "data loaded: $data")
Log.e("FeatureVM", "error", exception)
println("debug: $value")

// ✅ Correct
import com.psa.logger.MyMLogger
MyMLogger.d("FeatureFragmentViewModel - data loaded: $data")
MyMLogger.e(exception, "FeatureFragmentViewModel - loadData failed: ${exception.message}")
MyMLogger.i("FeatureFragment - user confirmed action")
```

---

### V6 — Direct Fragment Transactions in Feature Code

**Detection:** Search for `beginTransaction()` outside of navigation helper/wrapper classes.

```kotlin
// ❌ Wrong in feature Fragment
binding.button.setOnClickListener {
    parentFragmentManager.beginTransaction()
        .replace(R.id.container, DetailFragment.newInstance(id))
        .addToBackStack(null)
        .commit()
}

// ✅ Correct — describe as navigation helper call
// Search for FragmentsHelper or the project's navigation pattern in the module
FragmentsHelper.navigateTo(requireActivity(), DetailFragment.newInstance(id))
```

---

### V7 — ViewBinding Not Used

**Detection:** Search for `findViewById` in Fragment or Activity.

```kotlin
// ❌ Wrong
val button = view.findViewById<Button>(R.id.confirmButton)
button.setOnClickListener { ... }

// ✅ Correct
binding.confirmButton.setOnClickListener { ... }

// ❌ Wrong — binding not nulled
override fun onDestroyView() {
    super.onDestroyView()
    // missing: _binding = null
}
```

---

### V8 — Exposed MutableLiveData

**Detection:** Search for `val.*MutableLiveData` (public property with mutable type).

```kotlin
// ❌ Wrong — MutableLiveData is publicly exposed
val state: MutableLiveData<FeatureState> = MutableLiveData()

// ✅ Correct — private mutable, public immutable
private val _state: MutableLiveData<FeatureState> = MutableLiveData()
val state: LiveData<FeatureState> = _state
```

---

## 🟡 MEDIUM — Should Fix

### V9 — Fragment Not Extending BaseFragment

```kotlin
// ❌ Wrong
class FeatureFragment : Fragment() { ... }

// ✅ Correct
class FeatureFragment : BaseFragment<FeatureFragmentViewModel>(FeatureFragmentViewModel::class) { ... }
```

### V10 — ViewModel Not Extending BaseFragmentViewModel

```kotlin
// ❌ Wrong
class FeatureFragmentViewModel : ViewModel() { ... }
class FeatureFragmentViewModel : AndroidViewModel(application) { ... }

// ✅ Correct
class FeatureFragmentViewModel(...) : BaseFragmentViewModel() { ... }
```

### V11 — Koin Module Not Loaded in Fragment

```kotlin
// ❌ Wrong — feature DI is never loaded
override fun getModulesList() = emptyList<Module>()
// or
// getModulesList() not overridden when a feature module exists

// ✅ Correct
override fun getModulesList() = listOf(featureModule)
```

### V12 — God UseCase / God ViewModel

```kotlin
// ❌ God UseCase — multiple unrelated responsibilities
class VehicleUseCase {
    fun getStatus() { ... }
    fun updateStatus() { ... }
    fun deleteVehicle() { ... }  // ← 3 separate UseCases needed
}

// ✅ Split into atomic UseCases
class GetVehicleStatusUseCase(...)
class UpdateVehicleStatusUseCase(...)
class DeleteVehicleUseCase(...)
```

---

## Quick Detection Commands

```bash
# V3: Android imports in domain
grep -r "import android\." */src/main/java/*/domain/

# V5: Wrong logging
grep -rn "android.util.Log\|Log\.d\|Log\.e\|Log\.i\|println" --include="*.kt" .

# V7: findViewbyId usage
grep -rn "findViewById" --include="*.kt" */src/main/

# V8: Public MutableLiveData
grep -rn "val.*: MutableLiveData" --include="*.kt" */src/main/

# V6: Direct fragment transactions
grep -rn "beginTransaction\(\)" --include="*.kt" */src/main/
```
