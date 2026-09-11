---
name: unit-testing
description: >-
  Write and review unit tests for Domain, Data, and ViewModel layers using JUnit4 + MockK.
  Use when adding tests for use cases, repository implementations, mappers, or ViewModels.
---

# Purpose
Provides the standard test class structure, MockK patterns, and naming conventions for every testable layer in this project.

# When to use
- Writing a new unit test for a UseCase, Repository, ViewModel, or Mapper
- Reviewing test coverage for a PR
- Fixing a flaky or incorrectly structured test
- Adding regression tests after a bug fix
- **Not for:** UI/Espresso tests, instrumented tests, or build configuration

# Procedure
1. Place the test file at `[module]/src/test/java/[same.package.as.class]/[ClassName]Test.kt`.
2. Choose the correct structure from the sections below (UseCase, ViewModel, Repository, Mapper).
3. Declare mocks with `@MockK`, initialise in `init { MockKAnnotations.init(this) }`, build the class under test last.
4. Call `clearAllMocks()` in `@Before setUp()`.
5. Name each test: `` `given [precondition] when [action] then [expected result]` ``.
6. Use Arrange / Act / Assert structure inside every test body.
7. Add `InstantTaskExecutorRule` + `TestCoroutineRule` for ViewModel tests.
8. Minimum coverage target: **70% per changed class**.

# Inputs & Outputs
- **Inputs:** Class under test; its constructor dependencies (to mock); expected behaviour per method
- **Outputs:** `[ClassName]Test.kt` with at least: happy path, failure path, and edge case (empty/null input)

# Examples
**User ask:** "Write tests for `GetVehicleStatusUseCase`."
**Assistant plan:** Mock `VehicleStatusRepository`; test success return, exception thrown, and empty VIN.
**Expected artifacts:** `GetVehicleStatusUseCaseTest.kt` with 3+ tests using `coEvery`/`coVerify`.

**User ask:** "My ViewModel test fails with `Cannot invoke observeForever on a background thread`."
**Assistant plan:** Add `@get:Rule var instantTaskExecutorRule = InstantTaskExecutorRule()`.
**Expected artifacts:** Updated test class with the rule added.

# Notes & Guardrails
- Always mock interfaces, never concrete implementations.
- Never use `Thread.sleep()` — use `TestCoroutineRule` and coroutine test utilities.
- `runBlocking` is acceptable for coroutine tests when `TestCoroutineRule` is not available.
- Do not test Android framework behaviour — mock at the boundary.

---

# Unit Testing with JUnit4 + MockK
> Load this skill when writing or reviewing unit tests in this repository.

**Stack:** JUnit4 + MockK + `kotlinx.coroutines.test` + `androidx.arch.core:core-testing`  
**Target coverage:** 70% minimum per changed class  
**Test location:** `[module]/src/test/java/[same.package.as.class]/[ClassName]Test.kt`

---

## 1. Standard Test Class Structure

### UseCase Test (most common)
```kotlin
package com.psa.mym.feature.usecases  // ← same package as class under test

import io.mockk.MockKAnnotations
import io.mockk.clearAllMocks
import io.mockk.coEvery
import io.mockk.coVerify
import io.mockk.impl.annotations.MockK
import kotlinx.coroutines.runBlocking
import org.junit.Before
import org.junit.Test
import kotlin.test.assertEquals
import kotlin.test.assertTrue
import kotlin.test.assertFalse

class GetFeatureDataUseCaseTest {

    // ✅ Mock the interface, not the implementation
    @MockK
    private lateinit var featureRepository: FeatureRepository

    private lateinit var classUnderTest: GetFeatureDataUseCase

    // ✅ Always clearAllMocks before each test
    @Before
    fun setUp() {
        clearAllMocks()
    }

    // ✅ MockKAnnotations in init block — before classUnderTest instantiation
    init {
        MockKAnnotations.init(this)
        classUnderTest = GetFeatureDataUseCase(featureRepository)
    }

    @Test
    fun `given valid vin when invoke then returns success with data`() = runBlocking {
        // Arrange
        val expected = FeatureModel(id = "123", name = "Test")
        coEvery { featureRepository.getData(any()) } returns Result.success(expected)

        // Act
        val result = classUnderTest("123")

        // Assert
        assertTrue(result.isSuccess)
        assertEquals(expected, result.getOrNull())
        coVerify(exactly = 1) { featureRepository.getData("123") }
    }

    @Test
    fun `given repository throws exception when invoke then returns failure`() = runBlocking {
        // Arrange
        val exception = RuntimeException("Network error")
        coEvery { featureRepository.getData(any()) } throws exception

        // Act
        val result = classUnderTest("123")

        // Assert
        assertTrue(result.isFailure)
        coVerify(exactly = 1) { featureRepository.getData("123") }
    }

    @Test
    fun `given empty id when invoke then returns empty result`() = runBlocking {
        // Arrange
        coEvery { featureRepository.getData("") } returns Result.success(FeatureModel.EMPTY)

        // Act
        val result = classUnderTest("")

        // Assert
        assertTrue(result.isSuccess)
    }
}
```

---

## 2. ViewModel Test Structure

```kotlin
package com.psa.mym.feature.viewmodel

import androidx.arch.core.executor.testing.InstantTaskExecutorRule
import io.mockk.MockKAnnotations
import io.mockk.clearAllMocks
import io.mockk.coEvery
import io.mockk.coVerify
import io.mockk.impl.annotations.MockK
import kotlinx.coroutines.runBlocking
import org.junit.After
import org.junit.Before
import org.junit.Rule
import org.junit.Test
import kotlin.test.assertTrue

class FeatureFragmentViewModelTest {

    // ✅ MANDATORY for ViewModel tests — synchronizes LiveData on main thread
    @get:Rule
    var instantTaskExecutorRule = InstantTaskExecutorRule()

    // ✅ Add TestCoroutineRule if it exists in the project
    @get:Rule
    val testCoroutineRule = TestCoroutineRule()  // com.psa.mym.TestCoroutineRule

    @MockK
    private lateinit var getFeatureDataUseCase: GetFeatureDataUseCase

    @MockK
    private lateinit var saveFeatureUseCase: SaveFeatureUseCase

    private lateinit var classUnderTest: FeatureFragmentViewModel

    @Before
    fun setUp() {
        clearAllMocks()
    }

    @After
    fun tearDown() {}

    init {
        MockKAnnotations.init(this)
        classUnderTest = FeatureFragmentViewModel(
            getFeatureDataUseCase,
            saveFeatureUseCase
        )
    }

    @Test
    fun `when viewReady then getFeatureDataUseCase is invoked`() = runBlocking {
        // Arrange
        val data = FeatureModel(id = "1", name = "Test")
        coEvery { getFeatureDataUseCase() } returns Result.success(data)

        val states = mutableListOf<FeatureState>()
        classUnderTest.state.observeForever { states.add(it) }

        // Act
        classUnderTest.viewReady()

        // Assert
        assertTrue(states.any { it is FeatureState.Success })
        coVerify(exactly = 1) { getFeatureDataUseCase() }
    }

    @Test
    fun `when getFeatureData fails then error state is posted`() = runBlocking {
        // Arrange
        coEvery { getFeatureDataUseCase() } returns Result.failure(RuntimeException("error"))

        val states = mutableListOf<FeatureState>()
        classUnderTest.state.observeForever { states.add(it) }

        // Act
        classUnderTest.viewReady()

        // Assert
        assertTrue(states.any { it is FeatureState.Error })
    }
}
```

---

## 3. Repository Implementation Test

```kotlin
class FeatureRepositoryImplTest {

    @MockK
    private lateinit var remoteDataSource: FeatureRemoteDataSource

    @MockK
    private lateinit var localDataSource: FeatureLocalDataSource

    private lateinit var classUnderTest: FeatureRepositoryImpl

    @Before
    fun setUp() { clearAllMocks() }

    init {
        MockKAnnotations.init(this)
        classUnderTest = FeatureRepositoryImpl(remoteDataSource, localDataSource)
    }

    @Test
    fun `given remote returns dto when getData then returns mapped domain model`() = runBlocking {
        // Arrange
        val dto = FeatureDto(id = "1", name = "test")
        coEvery { remoteDataSource.fetchData(any()) } returns dto

        // Act
        val result = classUnderTest.getData("1")

        // Assert
        assertTrue(result.isSuccess)
        assertEquals("1", result.getOrNull()?.id)
        coVerify(exactly = 1) { remoteDataSource.fetchData("1") }
    }

    @Test
    fun `given remote throws when getData then returns failure`() = runBlocking {
        // Arrange
        coEvery { remoteDataSource.fetchData(any()) } throws RuntimeException("Network error")

        // Act
        val result = classUnderTest.getData("1")

        // Assert
        assertTrue(result.isFailure)
    }
}
```

---

## 4. Mapper Test (Pure Functions → 100% Coverage)

```kotlin
class FeatureMappersTest {

    @Test
    fun `given valid dto when toDomain then all fields are correctly mapped`() {
        // Arrange
        val dto = FeatureDto(id = "123", name = "test", active = true)

        // Act
        val domain = dto.toDomain()

        // Assert
        assertEquals("123", domain.id)
        assertEquals("test", domain.name)
        assertTrue(domain.isActive)
    }

    @Test
    fun `given dto with null fields when toDomain then defaults are applied`() {
        // Arrange
        val dto = FeatureDto(id = null, name = null, active = null)

        // Act
        val domain = dto.toDomain()

        // Assert
        assertEquals("", domain.id)
        assertEquals("", domain.name)
        assertFalse(domain.isActive)
    }
}
```

---

## 5. Real Project Pattern Reference

From `GetVehicleCourtesyUseCaseTest.kt`:
```kotlin
class GetVehicleCourtesyUseCaseTest {

    @MockK
    private lateinit var plpRepository: PLPRepository

    @MockK
    private lateinit var allServicesDataUseCase: GetLoyaltyContentAllServicesDataUseCase

    private var classUnderTest: GetVehicleCourtesyUseCase

    @Before
    fun setUp() {
        clearAllMocks()
    }

    init {
        MockKAnnotations.init(this)
        classUnderTest = GetVehicleCourtesyUseCase(plpRepository, allServicesDataUseCase)
    }
}
```

From `ChargingFragmentViewModelTest.kt` (ViewModel with rules):
```kotlin
class ChargingFragmentViewModelTest {

    @get:Rule
    var instantTaskExecutorRule = InstantTaskExecutorRule()

    @get:Rule
    val testCoroutineRule = TestCoroutineRule()

    @MockK
    private lateinit var scheduleChargingUseCase: ScheduleChargingUseCase

    // ... additional mocks

    init {
        MockKAnnotations.init(this)
        classUnderTest = ChargingFragmentViewModel(
            scheduleChargingUseCase,
            // ... all constructor params
        )
    }
}
```

---

## 6. MockK Quick Reference

| Scenario | Syntax |
|----------|--------|
| Mock suspend function (success) | `coEvery { dep.func(any()) } returns value` |
| Mock regular function (success) | `every { dep.func(any()) } returns value` |
| Mock function throws | `coEvery { dep.func(any()) } throws Exception("msg")` |
| Mock Unit function | `coEvery { dep.func(any()) } just runs` |
| Verify suspend called once | `coVerify(exactly = 1) { dep.func(arg) }` |
| Verify never called | `coVerify(exactly = 0) { dep.func(any()) }` |
| Capture argument | `val slot = slot<String>(); every { dep.func(capture(slot)) } returns x` |
| Mock object | `val mock = mockk<ClassName>()` |
| Relaxed mock (no stubs needed) | `val mock = mockk<ClassName>(relaxed = true)` |

---

## 7. Test Naming Convention

```
`given [precondition] when [action] then [expected result]`
```

| Layer | Example |
|-------|---------|
| UseCase | `` `given valid vin when invoke then returns vehicle status` `` |
| Repository | `` `given remote returns dto when getData then returns mapped model` `` |
| ViewModel | `` `when viewReady then state transitions to Success` `` |
| Mapper | `` `given dto with null fields when toDomain then defaults are applied` `` |

---

## 8. Coverage Requirements Per Layer

| Layer | Target | Focus |
|-------|--------|-------|
| UseCase | 80%+ | All `invoke()` paths: success, failure, boundary |
| Repository impl | 70%+ | Remote/local paths, error handling, mapper calls |
| ViewModel | 70%+ | `viewReady()`, all public actions, all state transitions |
| Mapper | 100% | All fields, null defaults |
| DataSource | 60%+ | When worth testing in isolation |
