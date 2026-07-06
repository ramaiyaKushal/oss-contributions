# Contribution 2: Google Gemini Model Provider

**Contribution Number:** 2  
**Student:** Kushal Atul Ramaiya  
**Issue:** [orthogonalhq/nous-core#71](https://github.com/orthogonalhq/nous-core/issues/71)  
**Week:** 5

**Status:** Phase IV Complete — Iterating

---

## Why I Chose This Issue

The issue matches my interest in AI model integrations and gives me an opportunity to learn how different model providers connect to a shared application interface.

I also chose it because the issue has a clearly defined scope and acceptance criteria, making it suitable for a focused open-source contribution.

---

## Understanding the Issue

### Problem Description

Nous Core supports multiple AI model providers through a shared
`IModelProvider` interface. However, it does not currently include a provider implementation for Google's Gemini API.

Issue #71 requests a new Gemini provider that follows the current certified provider-leaf workflow used by the provider adapter surface.

### Expected Behavior

The completed Gemini provider should:

- Implement `invoke`, `stream`, and `getConfig`.
- Validate input with `TextModelInputSchema`.
- Handle Gemini's streaming API format.
- Be registered through the generated provider catalog workflow.
- Be exported from the provider package public surface.
- Include tests covering metadata, request formatting, response parsing,
  streaming, authentication, errors, and provider discovery.

### Scope Boundary

The issue explicitly states that `IModelProvider` and `TextModelInputSchema` should not be modified.

### Affected Components

- `self/subcortex/providers/src/providers/gemini/`
- `self/subcortex/providers/src/index.ts`
- `self/subcortex/providers/src/provider-adapters.ts`
- `self/subcortex/providers/src/provider-definitions.ts`
- `self/subcortex/providers/src/provider-factories.ts`
- `self/subcortex/providers/src/__tests__/providers/gemini.test.ts`

---

## Week 1 Progress

### Issue Selection

I selected [orthogonalhq/nous-core issue #71](https://github.com/orthogonalhq/nous-core/issues/71), titled **“Adapter: Google Gemini Model Provider.”**

### Initial Outreach

I commented on the issue to introduce myself, explain that I wanted to work on it as part of my CodePath AI Capstone Project, and ask the maintainer for assignment and any additional guidance.

**Comment:** [Issue comment #4639510920](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4639510920)

At the end of Week 1, I had not yet received a maintainer response or formal assignment.

---

## Week 2 Progress

### Maintainer Response and Assignment

The maintainer explained that the provider adapter surface was being refactored
and asked contributors to wait for an updated workflow.

**Comment:** [Maintainer update](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4644395718)

The maintainer later confirmed that the updated provider adapter documentation
was available and formally assigned the issue to me. They also asked Cloud API
provider contributions to target
`feat/contributor-friendly-inference-provider-surface` instead of `dev`.

The maintainer provided the following guidance:

- Implement provider adapters as certified provider leaves under
  `self/subcortex/providers/src/providers/<vendor>/`.
- Use the provider adapter documentation as the source of truth.
- Do not hand-edit generated provider catalogs.
- Flag suspicious, contradictory, stale, or confusing project behavior.

**Comment:** [Provider adapter specification update](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4684774025)

### Local Development Environment

I cloned the Nous Core repository and prepared a clean contribution branch:

- **PR target:** `feat/contributor-friendly-inference-provider-surface`
- **Working branch:** `google-gemini-model-provider`
- **Starting commit:** `182b74f0`

I verified that the working branch was created directly from the requested PR
target without additional commits from earlier experiments.

### Reproducing the Issue

Issue #71 is a feature request rather than a bug. To reproduce the issue, I
confirmed that Gemini support is absent from the active integration branch.

I inspected the provider package and found:

- No registered Gemini provider implementation exists.
- The provider package does not publicly export a Gemini provider.
- The provider definitions catalog does not include Gemini.
- The provider registry does not include a Gemini creation path.
- The existing provider test suite does not contain Gemini provider tests.

These findings confirm the missing behavior described by the issue.

### Codebase Investigation

I reviewed the following files to understand the existing architecture and
project conventions:

- `anthropic-provider.ts`
- `chat-completions-provider.ts`
- `ollama-provider.ts`
- `schemas.ts`
- `provider-definitions.ts`
- `provider-registry.ts`
- Existing provider and adapter tests

The maintainer documentation describes a certified provider-leaf and
generated-catalog workflow. However, the current PR target branch does not yet
contain all of the documented structure. I identified this mismatch as a
dependency that requires clarification before implementation.

### Proposed Approach and Clarifying Questions

I posted my proposed implementation approach and asked the maintainer:

- Whether Gemini should use the native `generateContent` API or Google's
  OpenAI-compatible endpoint.
- Whether native Gemini function calling is part of this issue or whether the
  first version should support text generation and streaming only.

**Comment:** [Approach and clarification questions](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4686274446)

### Implementation Plan

After the API and scope questions are resolved, I plan to:

1. Confirm the correct provider structure available on the PR target branch.
2. Add Gemini provider metadata and `GEMINI_API_KEY` authentication.
3. Implement `getConfig`, `invoke`, and `stream`.
4. Validate input using the existing `TextModelInputSchema`.
5. Translate Nous messages and system prompts into the selected Gemini API
   format.
6. Parse Gemini responses, streaming events, usage metadata, and errors.
7. Integrate Gemini through the project's approved provider discovery workflow.
8. Add tests for requests, responses, streaming, authentication, errors,
   aborts, and provider discovery.
9. Run the documented typecheck and provider test suite before opening a pull
   request.

---

## Week 4 Progress

### Pull Request Submitted

I completed the implementation and opened a draft pull request against the
maintainer-requested integration branch:

**Pull Request:** [orthogonalhq/nous-core#420](https://github.com/orthogonalhq/nous-core/pull/420)

**PR Target:** `feat/contributor-friendly-inference-provider-surface`

**Working Branch:** `google-gemini-model-provider`

**Commit:** `cbc0fbfa Add Google Gemini provider leaf`

### Contribution Summary

The pull request adds a certified Google Gemini provider leaf under
`self/subcortex/providers/src/providers/gemini/`.

The implementation includes:

- Gemini provider metadata using `ProviderDefinitionLeaf`.
- `GEMINI_API_KEY` authentication through `x-goog-api-key`.
- Native Gemini `generateContent` support for non-streaming requests.
- Native Gemini `streamGenerateContent?alt=sse` support for streaming.
- Input validation using the existing `TextModelInputSchema`.
- Gemini adapter formatting and response parsing.
- Provider factory wiring.
- Generated provider catalog updates.
- Public provider export updates.
- Tests for provider metadata, adapter formatting/parsing, invoke, streaming,
  authentication, rate-limit handling, and registry discovery.

### Validation

Before submitting the pull request, I ran:

- `node self/subcortex/providers/scripts/generate-provider-aggregates.mjs --check`
- Focused Gemini provider tests
- Documented provider checklist tests
- Full `self/subcortex/providers` Vitest suite
- `oxlint` on the touched Gemini and export files

Results:

- Focused Gemini tests: 13 passed
- Provider checklist tests: 56 passed
- Full provider test suite: 400 passed, 4 skipped
- Lint check: 0 warnings, 0 errors

One known note: package typecheck currently reports missing `@nous/shared`
CLI-session exports on the integration branch. This appears unrelated to the
Gemini provider changes.

### Maintainer Feedback and Next Steps

I commented on the issue with the PR link and a summary of the implementation:

**Comment:** [Issue comment #4847458823](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4847458823)

Current feedback status:

- The issue was assigned to me.
- The maintainer provided branch and provider-leaf guidance.
- The PR has been submitted and is awaiting maintainer review.
- No code-review change requests have been received yet.

Next steps:

- Watch for maintainer feedback on the PR.
- Respond to questions or requested changes.
- Push follow-up commits if review feedback requires updates.
- Mark the PR ready for review when the implementation is ready to leave draft
  status.

### Phase IV Status

**Status:** Phase IV Complete — PR submitted and README updated.

**Review State:** Awaiting review / feedback.

---

## Week 5 Check-In

### Current PR Status

The pull request is still open:

**Pull Request:** [orthogonalhq/nous-core#420](https://github.com/orthogonalhq/nous-core/pull/420)

Current status:

- Phase IV is complete because the PR has been submitted and the contribution
  README has been updated.
- The PR has moved from initial submission into iteration.
- I am waiting for maintainer review after addressing the first round of
  maintainer feedback.

### Maintainer Feedback Addressed

The maintainer confirmed that the native Gemini API path is acceptable for this
PR:

- Use Gemini `generateContent` / `streamGenerateContent`.
- Keep the custom Gemini adapter/provider shape.
- Keep native function calling out of scope until the shared tool bridge exists.

The maintainer also identified one runtime issue:

- The original streaming timeout covered the initial `fetch()` call only.
- After SSE response headers arrived, `reader.read()` could still block
  indefinitely if Gemini or a proxy stalled the stream body.

I addressed this by adding bounded timeout and cancellation behavior around
each streaming body read.

### Updates Made This Week

This week I:

- Added timeout handling around each Gemini SSE `reader.read()` call.
- Cancelled the stream reader on body-read timeout.
- Cancelled the stream reader when the caller aborts during body consumption.
- Added regression tests for stalled post-header SSE streams.
- Added regression tests for caller abort during streaming body reads.
- Marked the PR ready for review after fixing the maintainer blocker.
- Merged the latest `feat/contributor-friendly-inference-provider-surface`
  branch into my PR branch to resolve provider-roster/catalog conflicts.
- Updated the PR with a comment summarizing the fix and validation results.

Relevant updates:

- **Streaming timeout fix commit:** `b68287a7 Bound Gemini stream body reads`
- **Merge conflict resolution commit:** `d3704bd8 Merge remote-tracking branch 'origin/feat/contributor-friendly-inference-provider-surface' into google-gemini-model-provider`
- **Maintainer feedback response:** [PR comment #4884016494](https://github.com/orthogonalhq/nous-core/pull/420#issuecomment-4884016494)
- **Merge conflict update:** [PR comment #4884042934](https://github.com/orthogonalhq/nous-core/pull/420#issuecomment-4884042934)

### Validation

After the streaming timeout fix, I ran:

- Gemini focused tests: 15 passed
- Provider checklist tests: 56 passed
- Full provider test suite: 402 passed, 4 skipped
- Gemini `oxlint`: 0 warnings, 0 errors
- `git diff --check`

After resolving merge conflicts with the updated integration branch, I ran:

- Generated provider check: passed
- Gemini focused tests: 15 passed
- Provider checklist tests: 56 passed
- Full provider suite: 455 passed, 4 skipped
- Gemini `oxlint`: passed
- `git diff --check --cached`: passed

Typecheck still reports existing `@nous/shared` CLI-session export errors on
the integration branch. I am tracking this as an upstream/base-branch issue,
not a Gemini-provider-specific failure.

### Blockers and Risks

Current blockers or risks:

- The PR is still awaiting maintainer review.
- GitHub/Vercel shows a Vercel authorization failure that appears unrelated to
  the code changes.
- The integration branch is changing quickly, so additional provider roster or
  generated catalog conflicts may appear again before review is complete.

### Next Steps

Next week I plan to:

- Monitor the PR for maintainer comments.
- Respond to review feedback within 24 hours when possible.
- Re-run focused and provider-level tests after any requested changes.
- Keep the branch aligned with the integration branch if more provider catalog
  churn lands upstream.

---

## Project Information

**Project:** Nous Core  
**Repository:** [orthogonalhq/nous-core](https://github.com/orthogonalhq/nous-core)  
**Issue:** [Adapter: Google Gemini Model Provider](https://github.com/orthogonalhq/nous-core/issues/71)  
**Issue Status:** Open  
**Maintainer Feedback:** Updated provider adapter specification and PR target provided; first maintainer blocker addressed; PR awaiting follow-up review

**Issue Assignment:** Assigned to `ramaiyaKushal`  
**Current Phase:** Phase IV — Submit and Iterate

---

## Pull Request

**PR Link:** [orthogonalhq/nous-core#420](https://github.com/orthogonalhq/nous-core/pull/420)

**PR Description:**

This pull request adds a certified Google Gemini provider leaf to Nous Core. It
implements native Gemini text generation and streaming support, wires the
provider into the generated catalog workflow, exports the provider from the
package surface, and adds tests for the new provider behavior.

**Maintainer Feedback:**

- The maintainer asked contributors to target
  `feat/contributor-friendly-inference-provider-surface`.
- The maintainer clarified that provider leaves should use
  `ProviderDefinitionLeaf` and should not hand-author `wellKnownProviderId`.
- The maintainer confirmed the native Gemini API path is acceptable and asked
  for streaming body-read timeout/cancellation behavior.
- I addressed the streaming timeout feedback and pushed an updated commit.

**Status:** Iterating / awaiting follow-up review

---

## Learnings & Reflections

### Technical Skills Gained

I learned how provider integrations fit into a shared model-provider
architecture, including provider metadata, factories, adapters, generated
catalogs, and streaming response parsing.

### Challenges Overcome

The provider surface changed while I was working on the issue. I had to update
my branch, discard the older flat-provider approach, and rework the solution to
match the certified provider-leaf structure requested by the maintainer.

### What I Would Do Differently Next Time

I would verify the current integration branch and contributor docs before
starting implementation work, especially in a project where the architecture is
actively being refactored.

---

## Resources

- [Nous Core repository](https://github.com/orthogonalhq/nous-core)
- [Nous Core issue #71](https://github.com/orthogonalhq/nous-core/issues/71)
- [My issue outreach comment](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4639510920)
- [Maintainer update](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4644395718)
- [Provider adapter specification update](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4684774025)
- [My approach and clarification questions](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4686274446)
- [My PR submission comment](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4847458823)
- [Google Gemini provider PR](https://github.com/orthogonalhq/nous-core/pull/420)
- [Streaming timeout fix PR comment](https://github.com/orthogonalhq/nous-core/pull/420#issuecomment-4884016494)
- [Merge conflict resolution PR comment](https://github.com/orthogonalhq/nous-core/pull/420#issuecomment-4884042934)
- [Provider adapter contributor documentation](https://docs.nue.orthg.nl/docs/development/provider-adapters)
