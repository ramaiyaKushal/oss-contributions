# Contribution 1: Improve `engine.py` Test Coverage

**Contribution Number:** 1  
**Student:** Kushal Atul Ramaiya  
**Issue:** [pytorch/ignite#1790](https://github.com/pytorch/ignite/issues/1790)  
**Status:** Phase I Complete

---

## Why I Chose This Issue

I chose this issue because improving test coverage is a practical way to learn how
PyTorch-Ignite's core execution engine behaves while making a focused contribution.
The task matches my Python and testing experience and gave me an opportunity to
study event handling, exception routing, weak references, and the engine's legacy
and generator execution paths.

I also wanted to learn how to use coverage reports to identify untested branches,
write targeted regression tests, and verify changes against an established
open-source project's test suite.

---

## Understanding the Issue

### Problem Description

Several defensive and exception-handling branches in `ignite/engine/engine.py`
were not executed by the existing test suite. The missing coverage included the
guard for an unresolved engine weak reference in `_fire_event` and the path that
routes a processing-function exception to `Events.EXCEPTION_RAISED`.

### Expected Behavior

- `_fire_event` should raise `RuntimeError` if a handler's engine weak reference
  cannot be resolved.
- Exceptions raised by an engine's processing function should be passed to a
  registered `Events.EXCEPTION_RAISED` handler.
- Exception routing should work for both the generator-based and legacy engine
  execution paths.

### Current Behavior

The implementation already contained the expected behavior, but the relevant
branches were not covered by tests. This made regressions in those branches less
likely to be detected.

### Affected Components

- `ignite/engine/engine.py`
- `tests/ignite/engine/test_engine.py`
- Engine event-handler registration and firing
- Generator and legacy engine run paths

---

## Reproduction Process

### Environment Setup

I cloned the Ignite repository, installed its development and test dependencies,
and ran the engine test suite locally. I used the coverage report's missing-line
output to identify branches in `engine.py` that were not exercised.

The full engine test file includes distributed tests that require pytest's
`worker_id` fixture. In my local environment, I verified the non-distributed
suite separately.

### Steps to Reproduce

1. Run the existing engine tests with coverage enabled.
2. Inspect the missing lines reported for `ignite/engine/engine.py`.
3. Observe that the unresolved weak-reference guard and process-function
   exception-routing branches are not covered.

### Reproduction Evidence

- **Commit showing reproduction:** Not captured in a separate commit; the missing
  branches were identified before implementation using `coverage report --show-missing`.
- **Screenshots/logs:** Coverage output identified the relevant missing lines in
  `_fire_event` and the dataset execution exception handlers.
- **My findings:** The existing implementation behaved correctly, but tests did
  not explicitly exercise these defensive and exception-routing paths.

---

## Solution Approach

### Analysis

The issue was missing test coverage rather than incorrect production behavior.
`_fire_event` contains a defensive check for a dead weak reference. Both
`_run_once_on_dataset_as_gen` and `_run_once_on_dataset_legacy` catch processing
exceptions and call `_handle_exception`, which fires `Events.EXCEPTION_RAISED`
when a handler is registered.

### Proposed Solution

Add focused unit tests to `tests/ignite/engine/test_engine.py`:

- Construct a dead weak reference and verify that `_fire_event` raises the
  documented `RuntimeError`.
- Use a processing function that raises `ValueError`, register an
  `Events.EXCEPTION_RAISED` handler, and verify that it receives the exception.

The existing `interrupt_resume_enabled=[False, True]` parameterization exercises
the exception-routing test through both engine execution implementations.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** Identify the uncovered defensive and exception-routing behavior
in the engine.

**Match:** Follow the existing `TestEngine` style and reuse its class-level
parameterization to cover generator and legacy execution paths.

**Plan:**
1. Import `weakref` and add a focused `_fire_event` guard test.
2. Add a processing-function exception-routing test.
3. Run targeted and non-distributed engine tests.
4. Check the PR diff for formatting and unrelated changes.

**Implement:** Branch `improve-engine-coverage`; commit
[`9fcfd529`](https://github.com/ramaiyaKushal/ignite/commit/9fcfd5290ef04328cae48dac39d2a263df8fe57c).

**Review:**
- [x] Change is limited to tests.
- [x] Tests follow existing project patterns.
- [x] Both engine execution paths are exercised.
- [x] No unrelated source changes are included.
- [x] Diff passes `git diff --check`.

**Evaluate:** Run the targeted tests and the non-distributed engine test suite,
then confirm the new branches are exercised by coverage.

---

## Testing Strategy

### Unit Tests

- [x] Verify `_fire_event` raises when its handler contains an unresolved weak reference.
- [x] Verify a processing-function `ValueError` reaches `Events.EXCEPTION_RAISED`.
- [x] Verify exception routing through both generator and legacy execution paths.

### Integration Tests

- [x] Run the non-distributed `tests/ignite/engine/test_engine.py` suite.
- [x] Confirm existing engine behavior remains unchanged outside the new tests.

### Manual Testing

The targeted new tests passed in both parameterized execution modes:
`4 passed`.

The non-distributed engine suite passed:
`195 passed, 8 skipped, 4 deselected`.

Running the complete engine test file locally produced two setup errors because
the environment did not provide pytest's `worker_id` fixture for distributed
tests; these errors are unrelated to the PR.

---

## Implementation Notes

### Week 1 Progress

I inspected the coverage report, traced the relevant engine control flow, added
two focused tests, and verified the changes locally. The main challenge was
covering a defensive weak-reference branch that is not naturally reachable
through normal public API usage.

### Week 2 Progress

Not applicable; the scoped implementation and local verification were completed
in Week 1. Maintainer feedback and any requested revisions will be documented
here when available.

### Code Changes

- **Files modified:** `tests/ignite/engine/test_engine.py`
- **Key commits:** [`9fcfd529`](https://github.com/ramaiyaKushal/ignite/commit/9fcfd5290ef04328cae48dac39d2a263df8fe57c)
- **Approach decisions:** Kept the change test-only and reused the existing
  `TestEngine` parameterization to avoid duplicating generator and legacy tests.

---

## Pull Request

**PR Link:** [pytorch/ignite#3777](https://github.com/pytorch/ignite/pull/3777)  

**PR Description:** Adds tests for two previously untested branches in
`ignite/engine/engine.py`: the unresolved engine weak-reference guard in
`_fire_event`, and processing-function exception routing to
`Events.EXCEPTION_RAISED` for both generator and legacy execution paths.

**Maintainer Feedback:**
- No maintainer feedback received yet.

**Status:** Awaiting review

---

## Learnings & Reflections

### Technical Skills Gained

- Reading and interpreting Python coverage reports
- Understanding PyTorch-Ignite's engine event lifecycle
- Testing exception-routing behavior
- Working with Python weak references
- Using existing parameterization to cover multiple implementations

### Challenges Overcome

The unresolved weak-reference guard is defensive behavior that is difficult to
reach through normal engine usage. I created the minimum internal test setup
needed to execute the guard and confirm its error behavior. I also verified that
the existing class parameterization caused the exception test to run against
both execution paths.

### What I'd Do Differently Next Time

I would capture a baseline coverage report in a dedicated reproduction commit
or artifact before implementing the tests. I would also discuss whether
unreachable defensive branches should be tested through internal state or
explicitly excluded from coverage before adding a test coupled to private
implementation details.

---

## Resources Used

- [PyTorch-Ignite contribution guidelines](https://github.com/pytorch/ignite/blob/master/CONTRIBUTING.md)
- [PyTorch-Ignite issue #1790](https://github.com/pytorch/ignite/issues/1790)
- [Python `weakref` documentation](https://docs.python.org/3/library/weakref.html)
- [pytest documentation](https://docs.pytest.org/)
- [coverage.py documentation](https://coverage.readthedocs.io/)
