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

## Review Hook

The self-review assesses every test change against this guidance under its Test
Quality category. Tests that pass but are unfocused, accidentally-passing, or
duplicate setup/stubs that belong in shared helpers are not done.
