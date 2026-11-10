# Test Implementation Quality

How tests are written, not just whether they exist. This file is the reference
the self-review (`26_review_03`) uses to assess test quality, the same way code
quality is assessed against the design guidance. It complements the testing
standards (`24_qa_01`) and the stack-specific test rules; it does not replace
them.

## Reuse Over Re-Creation

- Build test data with the existing builders and fixture wrappers; do not
  hand-roll inline object graphs when a builder exists. Extend the builder when
  it lacks something instead of constructing ad-hoc shapes.
- Reuse shared setup: put common arrangement in `beforeEach` / `@BeforeEach` or a
  shared render/setup helper instead of copying it into each test.
- Reuse shared stubbing/mocking: centralise common dependency stubs and test
  doubles; do not redefine the same mock in every test.

## Assertions Are Pessimistic, Specific, and Precise

- An assertion must not be able to pass by accident. Assert exact values,
  identities, and quantities — not loose substrings or truthy checks that a wrong
  result could still satisfy.
- Verify quantity before indexing into a collection; assert identity before
  asserting properties.
- For absence, assert the element or value is actually gone (for example
  `queryBy… → not present`), not merely that something else is present.
- Prefer explicit expected values computed before the action over re-deriving
  them from the result under test.

## Fast-Fail, Step-by-Step — But Stay Focused

- When a test exercises a change with several meaningful steps, validate at each
  step (for example status → structure → identity → properties) so a failure
  pinpoints the exact step that broke.
- Each test still proves one behaviour. Step assertions support that behaviour;
  they do not turn the test into an unfocused catch-all.

## Setup and Support Assertions Preserve Focus

- Arrangement and intermediate ("support") assertions must not drown out the core
  assertions. A reader should see immediately what the test proves.
- Extract noisy setup and repeated support checks into helpers/fixtures so the
  body keeps clear visibility of the actual subject under test.
- Use explicit failure messages where a bare assertion would be cryptic.

## Tests Must Not Depend on Timing

A test that passes on a fast machine and fails on a slow one is not a flaky
test; it is a test that asserts the wrong thing. Frontend and end-to-end tests
run against a page whose behaviour arrives in stages — markup first, then the
framework that binds it, then the state it renders. A test that acts before the
stage it depends on has arrived is racing, and the race is decided by machine
speed.

### Wait for the state you depend on, never for the element that hosts it

Before interacting with a control whose behaviour comes from JavaScript — a
menu, a dropdown, a toggle, anything a framework binds — assert that the
behaviour is present, not merely that the element is on the page. Markup renders
before the framework binds it, so a control is visible and clickable while it is
still inert. A click on an inert control is silently discarded: the click
succeeds, nothing happens, and the failure surfaces later on an unrelated
assertion.

Choose a signal the framework itself produces — a class it applies, an attribute
it sets, a value it exposes — and assert that first.

### Assert the outcome of an interaction, not just the interaction

After acting on a control, assert the state it was supposed to produce before
depending on it. `click()` reports that a click was delivered, never that it had
an effect. Without the follow-up assertion, a lost click is only discovered
further down the test, where the error names the wrong step.

### Forbidden

- Fixed waits (`waitForTimeout`, `sleep`) used to let something catch up. Wait
  for the condition instead.
- Raising a timeout to make a failing test pass. The timeout is not the defect.
- Retrying an interaction until it works.

A fixed wait is acceptable only where the thing waited on is genuinely a
duration — a debounce interval, an animation the test asserts the length of.

### Where a race is found, remove the option of repeating it

A race repaired at one call site stays available at every other. When the same
control is driven from more than one test, put the readiness-aware interaction
in a shared helper, use it everywhere, and enforce the helper so the raw
interaction cannot come back. A helper nobody is obliged to call prevents
nothing.

### Review Hook

The self-review assesses every frontend and end-to-end test change against this
section. A test that passes locally has not been shown to be timing-independent;
the question is what it waits for, not whether it was green.

## Review Hook

The self-review assesses every test change against this guidance under its Test
Quality category. Tests that pass but are unfocused, accidentally-passing, or
duplicate setup/stubs that belong in shared helpers are not done.
