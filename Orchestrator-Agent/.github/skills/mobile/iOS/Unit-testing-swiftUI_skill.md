---
name: unit-testing
description: >-
  Generate, execute, validate, update, and report iOS Unit Tests and UI Tests
  using XCTest, XCUITest, Swift Testing, and Swift Package Testing.
tools: ['read', 'edit', 'search', 'execute', 'get_errors', 'create_file', 'insert_edit_into_file', 'file_search', 'grep_search', 'list_dir', 'read_file', 'replace_string_in_file', 'run_in_terminal']
---

## Skill: unit-testing

## Used by

- unit-test-agent

---

## Purpose

Generate, execute, validate, update, and report iOS Unit Tests and UI Tests.

Supported Frameworks:

- XCTest
- XCUITest
- Swift Testing
- Swift Package Testing

Supported UI Technologies:

- UIKit
- SwiftUI

Supported Architectures:

- MVC
- MVVM
- MVVM-C
- MVP
- VIPER
- VIP
- Clean Architecture
- RIBs
- TCA
- MVI
- Redux
- Coordinator Pattern

---

## Inputs

Provided by architecture-detector:

```text
architecture
secondary_architecture
source_files
module_name
project_root
build_system
```

Optional:

```text
coverage_report.json
coverage_report.txt
test_report.json
test_report.txt
```

---

## Existing Test Analysis

Always execute before generating tests.

Steps:

1. Locate source files.
2. Locate test files.
3. Extract production methods.
4. Extract existing test methods.
5. Compare method coverage.
6. Compare behavior coverage.
7. Generate only missing tests.

Never:

```text
Overwrite passing tests
Generate duplicate tests
Regenerate existing tests
```

---

## Duplicate Prevention (Mandatory)

The agent must never create duplicate unit or UI test cases. Before writing any
new test, run this de-duplication check.

### Step 1 - Build an index of existing tests

Enumerate every existing test method across the test targets:

```bash
grep -rEn "func test[A-Za-z0-9_]+|@Test" <TestTargets>
```

For each existing test, record a fingerprint:

```text
{type-under-test} + {method-under-test} + {scenario/behavior} + {input/state}
```

Example fingerprints:

```text
LoginViewModel.login.success
LoginViewModel.login.invalidCredentials
HomeRepository.fetch.emptyResponse
```

### Step 2 - Skip anything already covered

A candidate test is a DUPLICATE and must NOT be generated if any existing test
matches the same fingerprint, even when:

- the test method name differs
- the arrange/act code is written differently
- it lives in a different test file or target

Match on behavior (type + method + scenario), not on method-name string equality.

### Step 3 - Only fill genuine gaps

Generate a new test only when its fingerprint is absent from the index. Map each
new test back to the specific uncovered method/branch/state it targets.

### Step 4 - One canonical test per behavior

- Do not create two tests asserting the same behavior with different data unless
  the data represents a distinct branch/equivalence class.
- Prefer extending fixtures/parameterized cases over emitting near-identical tests.
- If a near-duplicate is detected in EXISTING tests, do not add another; report it
  as already covered.

### Step 5 - Idempotency guarantee

Running the agent twice on an unchanged codebase must produce zero new test files
and zero new test methods (net-zero diff). If a second run would add tests, the
de-duplication check has failed and generation must stop.

Never rename or duplicate an existing passing test to "regenerate" it.

---

## Coverage Scan

### NEW

No test file exists.

Action:

```text
CREATE
```

---

### SKIP

Coverage is sufficient.

Action:

```text
DO NOTHING
```

---

### UPDATE_METHOD

Tests exist.

Methods remain uncovered.

Action:

```text
Generate tests only for uncovered methods.
```

---

### UPDATE_BEHAVIOR

Methods are covered.

Behavior coverage is incomplete.

Generate:

- failure tests
- timeout tests
- retry tests
- loading tests
- empty state tests
- error state tests
- branch coverage tests
- switch case coverage tests
- guard statement coverage tests

Never overwrite passing tests.

---

## Coverage Driven Generation

Priority:

```text
UPDATE_BEHAVIOR
UPDATE_METHOD
NEW
```

## Coverage Goals

```text
Line Coverage Goal    : 90%
Branch Coverage Goal  : 80%
Minimum Line Coverage : 80%
```

The run is not coverage-complete until line coverage is at least 80% (goal 90%)
and branch coverage is at least 80%, unless no additional meaningful tests can be
generated for reachable production code. Enforcement is performed by the
coverage-analyzer Coverage Gate.

The agent must prioritize and generate tests for:

- uncovered files
- uncovered methods
- uncovered branches
- uncovered states
- uncovered routes
- uncovered user journeys

Coverage improvement must be iterative:

1. Generate tests
2. Execute tests
3. Collect coverage
4. Identify coverage gaps
5. Generate additional tests
6. Re-run tests
7. Recalculate coverage

Repeat until:

- coverage goals are reached
- no additional meaningful tests can be generated
- all reachable production code is covered

Coverage analysis must prioritize the lowest-covered production code first.

Coverage Priority Order:

```text
0-20%
20-50%
50-80%
80-90%
```

Coverage Priority:

```text
Repository
UseCase
Interactor
Service
Network Layer
ViewModel
Presenter
Builder
Dependency Injection
Coordinator
Router
SwiftUI View
UIKit ViewController
XIB / Storyboard Controllers
```

The agent must aggressively target:

- business logic gaps
- branch coverage gaps
- state coverage gaps
- async execution paths
- navigation flows
- deep-link flows
- accessibility paths
- UI user journeys
- error handling paths
- retry scenarios
- timeout scenarios

Never:

- generate duplicate tests
- overwrite passing tests
- regenerate equivalent tests
- decrease existing coverage

If coverage_report.json or coverage_report.txt exists:

1. Parse existing coverage.
2. Identify uncovered files.
3. Identify uncovered methods.
4. Identify uncovered branches.
5. Identify uncovered states.
6. Generate tests only for coverage gaps.

Generated tests must maximize:

```text
Line Coverage
Branch Coverage
State Coverage
User Journey Coverage
Navigation Coverage
```

The agent should continue improving coverage until reaching the coverage goals or exhausting all meaningful test opportunities.

---

## Coverage Exclusions

Do not generate coverage for:

- generated code
- mocks
- stubs
- test utilities
- external dependencies
- package dependencies
- build artifacts
- auto-generated files

Focus coverage generation on production source code only.

---

## Coverage Improvement Loop

Repeat until:

- coverage goals reached
- no additional meaningful tests can be generated

Loop:

1. Execute tests
2. Collect coverage
3. Identify gaps
4. Generate tests
5. Re-run tests
6. Recalculate coverage

Never regenerate existing passing tests.

---

## Test Quality Validation

Generated tests must:

- contain meaningful assertions
- verify actual business behavior
- validate expected outcomes
- validate state transitions
- validate navigation behavior where applicable
- avoid implementation-detail coupling
- avoid flaky behavior
- avoid timing-based assertions
- avoid force-unwrapped dependencies

Reject:

- placeholder assertions
- `XCTAssertTrue(true)`
- empty tests
- duplicate tests
- meaningless coverage-only tests
- tests that do not validate behavior
- tests that rely on arbitrary delays or sleeps

Priority:

1. Behavior Verification
2. Business Rules
3. State Validation
4. Navigation Validation
5. Error Handling Validation
6. Accessibility Validation
7. Coverage Expansion

Generated tests should maximize:

- correctness
- maintainability
- reliability
- readability
- coverage

---

## Test Generation Constraints

Generate tests only for existing production code.

Never:

- invent APIs
- invent methods
- invent classes
- invent protocols
- invent properties
- invent navigation routes

All generated tests must be derived from actual source code.

Generated tests must compile against the existing project structure.

---

## Repository Testing

Generate:

- success tests
- failure tests
- empty response tests
- malformed response tests
- mapping tests
- cache tests
- retry tests
- fallback tests

Examples:

```text
HomeRepositoryTests
LoginRepositoryTests
UserRepositoryTests
```

---

## UseCase Testing

Generate:

- success tests
- failure tests
- dependency interaction tests
- edge case tests
- empty result tests

---

## Interactor Testing

Generate:

- business logic tests
- repository interaction tests
- state transition tests
- edge case tests

---

## Service Testing

Generate:

- API response tests
- parsing tests
- transformation tests
- mapping tests
- error handling tests

---

## Network Testing

Generate:

- success response tests
- API failure tests
- HTTP error tests
- decoding tests
- timeout tests
- retry tests
- cancellation tests
- offline tests

Coverage Target:

```text
NetworkClient
APIClient
Repository Networking
Service Layer
```

---

## Persistence Testing

Generate:

- save tests
- fetch tests
- update tests
- delete tests
- migration tests
- cache tests

Supported Technologies:

- Core Data
- SwiftData
- Realm
- SQLite
- Custom Persistence Layers

Validate:

- data integrity
- migration success
- cache consistency

---

## ViewModel Testing

Generate:

- state transition tests
- loading state tests
- success state tests
- failure state tests
- retry tests
- async tests
- published property tests

---

## Presenter Testing

Generate:

- formatting tests
- presentation tests
- state update tests
- view interaction tests

---

## Security Testing

Generate:

- token storage tests
- token deletion tests
- keychain tests
- secure storage tests
- credential lifecycle tests

Validate:

- data protection
- credential persistence
- credential removal

Supported:

- Keychain
- Secure Enclave integrations
- custom secure storage

---

## Builder Testing

Generate:

- dependency injection tests
- service injection tests
- ViewModel injection tests
- Router injection tests
- module wiring tests

Examples:

```text
HomeModuleBuilderTests
LoginModuleBuilderTests
```

---

## Dependency Injection Testing

Generate:

- registration tests
- resolution tests
- dependency graph tests
- service registration tests
- ViewModel resolution tests

Supported Frameworks:

- Swinject
- Resolver
- Factory
- Needle
- Custom Dependency Containers

---

## Mocking Strategy

Preferred:

- protocol-based mocks
- generated mocks
- dependency injection

Avoid:

- real network calls
- real database access
- real filesystem access
- real service dependencies

Reuse existing mocks whenever possible.

Generate new mocks only when required.

---

## Test Data Strategy

Generate:

- deterministic test data
- reusable fixtures
- reusable builders
- reusable factories

Avoid:

- hard-coded production data
- random data without seeding

Prefer:

- Fixtures
- Test Builders
- Factory Pattern

---

## Router Testing

Generate:

- push navigation tests
- modal presentation tests
- destination validation tests
- parameter forwarding tests

Examples:

```text
HomeRouterTests
LoginRouterTests
```

---

## Coordinator Testing

Generate:

- startup flow tests
- navigation tests
- flow completion tests
- child coordinator tests
- parent coordinator tests
- dependency propagation tests
- deep link tests

Examples:

```text
AppCoordinatorTests
LoginCoordinatorTests
HomeCoordinatorTests
CheckoutCoordinatorTests
```

Coverage Priority:

```text
AppCoordinator
FeatureCoordinator
ChildCoordinator
FlowCoordinator
```

---

## Deep Link Testing

Generate:

- URL routing tests
- Universal Link tests
- Coordinator deep-link tests
- Navigation destination tests
- Parameter propagation tests

Examples:

```text
DeepLinkHandlerTests
UniversalLinkTests
LoginDeepLinkTests
```

---

## Feature Flag Testing

Generate:

- feature enabled tests
- feature disabled tests
- remote configuration tests
- rollout tests
- experiment tests

Validate:

- feature behavior when enabled
- feature behavior when disabled
- fallback behavior
- configuration updates

Coverage must include all feature branches.

---

## SwiftUI View Tests

Generate:

- loading state tests
- success state tests
- empty state tests
- error state tests
- navigation tests
- accessibility tests
- accessibility identifier tests
- accessibility label tests
- VoiceOver validation tests
- interaction tests
- ViewModel integration tests

Preferred:

```text
ViewInspector
```

Fallback:

```text
XCTest
```

### ViewInspector Auto-Detection and Fallback

SwiftUI view-unit tests must never silently fail to compile because ViewInspector
is missing. Before generating ViewInspector-based tests, detect availability.

Detect ViewInspector in the project:

```bash
grep -rEn "ViewInspector" *.xcodeproj/project.pbxproj Package.swift Package.resolved Podfile Podfile.lock 2>/dev/null
```

Also treat it as available if any test file already contains:

```swift
import ViewInspector
```

Decision:

- If ViewInspector IS available -> generate `import ViewInspector` view-state and
  interaction unit tests (preferred, fast, no simulator UI automation needed).
- If ViewInspector is NOT available -> do one of the following, in order:
  1. If the project uses Swift Package Manager and dependency changes are allowed,
     add ViewInspector as a test-only dependency, then generate ViewInspector tests.
  2. Otherwise, fall back to **XCUITest** for SwiftUI screens (drive the running
     UI via accessibility identifiers) plus plain **XCTest** for the backing
     `ObservableObject` / ViewModel state.
- Never emit `import ViewInspector` when the dependency is absent and cannot be added.

Fallback mapping when ViewInspector is unavailable:

```text
SwiftUI view-state assertion   -> XCUITest via accessibilityIdentifier
SwiftUI ViewModel/state logic  -> XCTest on the ObservableObject
SwiftUI navigation flow        -> XCUITest navigation test
```

To keep the XCUITest fallback reliable, generated SwiftUI views under test must
expose stable `accessibilityIdentifier` values; if missing, prefer testing the
ViewModel/state layer with XCTest rather than asserting on rendered UI.

Classify a missing/undecidable UI test target or dependency wiring as
`PROJECT_CONFIG_ISSUE`, not `TEST_CODE_ISSUE`.

Coverage Priority:

```text
State transitions
Navigation
Loading states
Error states
Accessibility
Interactions
```

---

## Additional Accessibility Coverage

- accessibilityIdentifier tests
- accessibilityLabel tests
- accessibilityHint tests
- VoiceOver validation
- Dynamic Type validation
- Large Content Viewer validation

---

## Localization Testing

Generate:

- localization key validation tests
- missing translation tests
- fallback language tests
- pluralization tests
- RTL layout tests

Validate:

- localized strings
- localization fallback behavior
- right-to-left layouts
- accessibility localization

---

## UIKit View Testing

Generate:

- viewDidLoad tests
- viewWillAppear tests
- viewDidAppear tests
- user interaction tests
- delegate tests
- datasource tests
- navigation tests
- dependency injection tests

---

## XIB and Storyboard Testing

Generate:

- nib loading tests
- storyboard instantiation tests
- IBOutlet validation tests
- IBAction validation tests
- runtime loading tests

Validate:

- outlets connected
- actions connected
- controller initialization succeeds

---

## TCA Store Testing

Generate:

- reducer tests
- state transition tests
- effect tests
- dependency tests
- async effect tests

Use:

```swift
TestStore
```

Validate:

- actions
- state transitions
- side effects

---

## Async Test Generation

Detect:

```swift
async
await
Task
actor
@MainActor
AsyncSequence
```

Generate:

- success tests
- failure tests
- timeout tests
- cancellation tests
- concurrent execution tests
- actor isolation tests

Treat uncovered async paths as high-priority coverage gaps.

---

## Notification Testing

Generate:

- notification observer tests
- notification posting tests
- notification payload tests
- lifecycle notification tests

Supported:

- NotificationCenter
- custom notification systems

Validate:

- notification registration
- notification delivery
- notification cleanup

---

## Memory Management Validation

Generate:

- deallocation tests
- weak reference tests
- retain cycle tests
- coordinator cleanup tests
- closure capture validation tests

Detect:

```swift
weak
unowned
deinit
```

---

## Simulator Lifecycle (Mandatory)

The simulator must be booted at most once per run and reused for every test.
Never boot, shut down, or re-launch the simulator per test case, per test class,
or per file.

### Boot once, reuse always

1. List simulators and detect an already-booted device:

```bash
xcrun simctl list devices booted
```

2. If a compatible simulator is already booted, REUSE it. Do not boot another.
3. If none is booted, boot exactly one and keep it for the entire run:

```bash
xcrun simctl boot "<device-udid>"
```

4. Do not call `shutdown`/`erase` between tests. Shut down only at the very end
   of the full run, if cleanup is required.

### Run all tests in a single invocation

Execute the entire suite in ONE `xcodebuild test` call so the app installs and
the simulator launches only once. Never loop `xcodebuild test` per test method.

```bash
xcodebuild test \
  -workspace <workspace> \
  -scheme <scheme> \
  -destination "platform=iOS Simulator,id=<booted-udid>" \
  -parallel-testing-enabled NO
```

- Use `-destination id=<booted-udid>` (a concrete booted device) rather than
  `name=...,OS=latest`, so xcodebuild attaches to the already-booted simulator
  instead of starting a new one.
- To run a subset, pass multiple `-only-testing:` flags in the SAME command;
  do not run one command per test.

### Prohibited patterns

```text
Boot simulator, run 1 test, shutdown, boot again for next test   ❌
One `xcodebuild test` invocation per test method or per class    ❌
Erasing/rebooting the simulator between test cases               ❌
```

### Allowed patterns

```text
Reuse a booted simulator across the whole suite                  ✅
Single `xcodebuild test` run for all tests                       ✅
Optional parallel testing across clones of the SAME booted run   ✅
```

Preferred devices (first already-booted match wins):

```text
iPhone 16
iPhone 15
iPhone 14
```

---

## Simulator Diagnostics

If simulator unavailable:

```text
environment_status = simulator_missing
```

Classification:

```text
ENVIRONMENT_ISSUE
```

Do not classify as APP_BUG.

---

## Dynamic Project Discovery

Discover:

```bash
find . -name "*.xcworkspace"
find . -name "*.xcodeproj"
find . -name "Package.swift"
```

Priority:

```text
1. xcworkspace
2. xcodeproj
3. Package.swift
```

---

## Scheme Discovery

Run:

```bash
xcodebuild -list
```

Collect:

```text
Schemes
Targets
Unit Test Targets
UI Test Targets
```

Priority:

```text
1. User Specified
2. Main Application
3. First Buildable Scheme
```

---

## Test Target Discovery

Discover:

```text
*Tests
*UITests
Swift Testing Targets
```

Examples:

```text
AppTests
AppUITests
FeatureTests
FeatureUITests
```

---

## Scheme Test Action Preflight

Before running `xcodebuild test`, the agent must verify the scheme is actually
configured for the Test action. Skipping this check is what causes silent
`Tests executed: 0` runs and `collection_failed` coverage.

### Step 1 - Verify the scheme can run tests

```bash
xcodebuild -project <project> -scheme <scheme> -showTestPlans
```

or:

```bash
xcodebuild -project <project> -scheme <scheme> -showBuildSettings -json 2>/dev/null
```

Interpretation:

- If test plans / test targets are listed -> scheme is test-capable, continue.
- If the command returns "There are no test plans associated with the scheme"
  or the Test action has no targets -> the scheme is NOT test-configured.

### Step 2 - Detect the specific blocking error

Treat the following as a project-configuration failure, NOT a test-code failure:

```text
xcodebuild: error: Scheme <scheme> is not currently configured for the test action.
```

Also treat as project-configuration failure:

- project contains no unit/UI test target at all
- test target exists on disk but is not a member of the scheme Test action
- generated test files exist but are not added to any target

### Step 3 - Attempt auto-remediation

When test targets EXIST but are not attached to the scheme Test action:

1. Locate the shared scheme:

```bash
find . -path "*/xcshareddata/xcschemes/<scheme>.xcscheme"
```

2. Ensure the scheme `<TestAction>` contains a `<TestableReference>` pointing at
   each discovered test target's `BuildableReference`.
3. If the scheme is a user scheme (not shared), promote it to a shared scheme so
   the change persists and is executable in CI.

When NO test target exists:

- The agent must NOT fabricate a target silently.
- It must report the exact remediation (create Unit/UI Testing bundle targets and
  attach generated files) and classify as `PROJECT_CONFIG_ISSUE`.

### Step 4 - Ensure generated files have target membership

Every generated test file must be added to a test target that is part of the
scheme Test action. A generated `.swift` file that is not a member of an
executable test target must be reported as `PROJECT_CONFIG_ISSUE`, because it can
never run.

### Step 5 - Re-verify before execution

Re-run the Step 1 check. Only proceed to `Unit Test Execution` once the scheme is
confirmed test-capable. Otherwise stop, emit diagnostics, and classify as
`PROJECT_CONFIG_ISSUE`.

---

## Unit Test Execution

Workspace:

```bash
xcodebuild test \
-workspace <workspace> \
-scheme <scheme> \
-destination "platform=iOS Simulator,name=<device>,OS=latest"
```

Fallback:

```bash
xcodebuild test \
-project <project> \
-scheme <scheme> \
-destination "platform=iOS Simulator,name=<device>,OS=latest"
```

---

## UI Test Execution

```bash
xcodebuild test \
-scheme <scheme> \
-only-testing:<UITarget> \
-destination "platform=iOS Simulator,name=<device>,OS=latest"
```

---

## UI Test Coverage

Generate:

- launch tests
- login flow tests
- onboarding tests
- navigation tests
- search tests
- detail page tests
- state restoration tests
- accessibility flow tests

Prioritize:

```text
Critical User Journeys
Primary Navigation
Authentication Flows
Checkout Flows
```

---

## Smoke Testing

Generate:

- app launch tests
- critical screen tests
- navigation smoke tests
- authentication smoke tests
- crash detection tests

Prioritize:

- application launch
- primary user journeys
- critical workflows

---


## Swift Testing Framework

Detect:

```swift
import Testing
@Test
```

Generate:

```swift
@Test
#expect()
```

---

## Objective-C Support

Detect:

```text
.m
.mm
.h
```

Generate:

- XCTest Objective-C tests
- Objective-C service tests
- Objective-C ViewController tests
- Objective-C category tests

Support mixed Swift and Objective-C projects.

---


## Result Collection

Collect:

```text
.xcresult
DerivedData
SwiftPM Results
```

Generate:

```text
test_report.txt
test_report.json
```

`test_report.json` must contain a `results` array. Each FAILED test must record
`suite`, `test`, `file`, `line`, `message`, `stack`, `classification`, and
`platform` so jira-integration can build a ticket with a real title and
description. Parse the `.xcresult` (e.g. `xcrun xcresulttool get --format json`)
to extract the failing test identifier, source file, line, and failure message.
Never emit a failed result with an empty `test`, `file`, or `message`.

---

## Snapshot Testing

Generate when available:

- SwiftUI snapshots
- UIKit snapshots
- Dark Mode snapshots
- Dynamic Type snapshots

Preferred Frameworks:

```text
SnapshotTesting
iOSSnapshotTestCase
```

---

## Performance Testing

Generate:

- rendering performance tests
- ViewModel performance tests
- repository performance tests
- collection rendering tests
- list scrolling tests
- data processing tests

Use:

- XCTMeasure

Validate:

- rendering efficiency
- execution time
- performance regressions

---

## Coverage Integration

Coverage generation is delegated to:

```text
coverage-analyzer
```

Outputs:

```text
coverage_report.txt
coverage_report.json
```

---

## JIRA Reporting

Create JIRA only for:

```text
APP_BUG
```

Never create JIRA for:

```text
TEST_CODE_ISSUE
ENVIRONMENT_ISSUE
COVERAGE_CONFIG_ISSUE
PROJECT_CONFIG_ISSUE
```

---

## Failure Classification

### PROJECT_CONFIG_ISSUE

Examples:

- scheme not configured for the test action
- no unit/UI test target in the project
- test target not attached to the scheme Test action
- generated test file has no target membership

Action:

```text
Attempt auto-remediation (attach test targets to the scheme Test action)
Display exact remediation steps if auto-fix is not possible
Do not create JIRA
Do not classify as APP_BUG or TEST_CODE_ISSUE
```

---

### TEST_CODE_ISSUE

Examples:

- compile failures
- stale tests
- broken mocks

Action:

```text
Fix
Rerun
Do not create JIRA
```

---

### ENVIRONMENT_ISSUE

Examples:

- simulator unavailable
- runtime unavailable
- Xcode unavailable

Action:

```text
Display diagnostics
Do not create JIRA
```

---

### COVERAGE_CONFIG_ISSUE

Examples:

- xccov unavailable
- coverage bundle missing

Action:

```text
Display diagnostics
Do not create JIRA
```

---

### APP_BUG

Examples:

- repository regression
- ViewModel regression
- router regression
- coordinator regression
- business logic regression

Action:

```text
Create JIRA only for verified APP_BUG failures
Keep failure visible
Never modify production code
```

---

## Quality Rules

- Use XCTest
- Use Swift Testing when available
- Use XCTestExpectation
- No sleep()
- No fake assertions
- No placeholder assertions
- Use @testable import
- Avoid flaky tests
- Support UIKit
- Support SwiftUI
- Support Swift Package Manager

---

## Test Stability Rules

Generated tests must:

- be deterministic
- be repeatable
- be isolated
- be environment independent

Avoid:

- fixed delays
- sleep()
- dependency on execution order
- real external services
- real network dependencies

Prefer:

- mocks
- stubs
- dependency injection
- fixtures
- test builders

Generated tests should produce the same result across repeated executions.

---

## Final Run Summary

```text
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🍎 iOS TEST RESULT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Platform         : iOS
Architecture     : {architecture}

Tests Executed   : {count}
Passed           : {count}
Failed           : {count}

Coverage Status  : {available | unavailable}
Coverage Engine  : xccov

Coverage Delta   : {value}

App Bugs         : {count}
Test Issues      : {count}
Environment      : {count}

JIRA Tickets     : {ticket_ids}

Reports:

- test_report.txt
- test_report.json
- coverage_report.txt
- coverage_report.json

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
