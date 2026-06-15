# Contribution 1: Google Gemini Model Provider

**Contribution Number:** 1  
**Student:** Kushal Atul Ramaiya  
**Issue:** [orthogonalhq/nous-core#71](https://github.com/orthogonalhq/nous-core/issues/71)  
**Current Phase:** Phase II — Reproduce and Plan  
**Status:** Phase I complete; Phase II in progress

---

## Why I Chose This Issue

I chose this issue as part of my CodePath AI Capstone Project because it focuses
on adding support for Google's Gemini API to an open-source AI system. The issue
matches my interest in AI model integrations and gives me an opportunity to
learn how different model providers connect to a shared application interface.

I also chose it because the issue has a clearly defined scope and acceptance
criteria, making it suitable for a focused open-source contribution.

---

## Understanding the Issue

### Problem Description

Nous Core supports multiple AI model providers through a shared
`IModelProvider` interface. However, it does not currently include a provider
implementation for Google's Gemini API.

Issue #71 requests a certified Gemini provider adapter that follows Nous Core's
updated provider adapter specification.

### Expected Behavior

The completed Gemini provider should:

- Implement `invoke`, `stream`, and `getConfig`.
- Validate input with `TextModelInputSchema`.
- Handle Gemini's streaming API format.
- Use the certified provider leaf structure under
  `self/subcortex/providers/src/providers/gemini/`.
- Be discoverable through the generated provider catalogs.
- Include tests that cover Gemini's real API shape.

### Scope Boundary

The issue explicitly states that `IModelProvider` and `TextModelInputSchema`
should not be modified.

### Affected Components

- `self/subcortex/providers/src/providers/gemini/`
- Generated provider catalogs
- Provider adapter tests

---

## Week 1 Activity

### Issue Selection

I selected [orthogonalhq/nous-core issue #71](https://github.com/orthogonalhq/nous-core/issues/71),
 titled **“Adapter: Google Gemini Model Provider.”**

The issue is labeled `good first issue` and `adapter`.

### Initial Outreach

I commented on the issue to introduce myself, explain that I wanted to work on
it as part of my CodePath AI Capstone Project, and ask the maintainer for
assignment and any additional guidance.

**Comment:** [Issue comment #4639510920](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4639510920)

### Maintainer Response

The maintainer first explained that the provider adapter surface was being
refactored and asked me to wait for the updated contributor workflow.

**Comment:** [Maintainer update](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4644395718)

The maintainer later confirmed that the updated provider adapter specification
and contributor documentation were available. They provided the following
guidance:

- Create a certified provider leaf under
  `self/subcortex/providers/src/providers/<vendor>/`.
- Use the provider adapter documentation as the source of truth.
- Do not hand-edit generated provider catalogs.
- Use `feat/contributor-friendly-inference-provider-surface` as the pull request
  target instead of `dev`.
- Flag suspicious, contradictory, stale, or confusing project behavior.

**Comment:** [Provider adapter specification update](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4684774025)

The issue is now formally assigned to me.

---

## Week 2: Reproduce and Plan

### Local Development Environment

I cloned the Nous Core repository and prepared a clean contribution branch:

- **PR target:** `feat/contributor-friendly-inference-provider-surface`
- **Working branch:** `google-gemini-model-provider`
- **Starting commit:** `182b74f0`

I verified that the working branch was created directly from the requested PR
target with no additional commits inherited from earlier experiments.

### Reproducing the Issue

Issue #71 is a feature request rather than a bug, so reproduction means
confirming that the requested Gemini provider does not exist on the active
integration branch.

I inspected the provider package and confirmed:

- There is no registered Gemini provider implementation in the target branch.
- The provider package does not publicly export a Gemini provider.
- The provider definitions catalog contains Anthropic, OpenAI-compatible, and
  Ollama providers, but not Gemini.
- The provider registry has no Gemini factory or creation path.
- There are no Gemini provider tests in the existing provider test suite.

This confirms the missing behavior described by issue #71.

### Codebase Investigation

I reviewed the existing provider architecture to understand the project’s
patterns and contracts:

- `anthropic-provider.ts` for a native cloud provider reference
- `chat-completions-provider.ts` for OpenAI-compatible APIs
- `ollama-provider.ts` for another `IModelProvider` implementation
- `schemas.ts` for `TextModelInputSchema`
- `provider-definitions.ts` for provider metadata
- `provider-registry.ts` for provider construction and routing
- Existing provider and adapter tests for testing conventions

The maintainer documentation describes a newer certified provider-leaf and
generated-catalog workflow. However, the current PR target branch does not yet
contain all of that documented structure. I am treating this as a dependency
and clarification point rather than assuming the mismatch is intentional.

### Clarifying Questions Sent

I posted my proposed approach and asked the maintainer:

- Whether Gemini should use the native `generateContent` API or Google’s
  OpenAI-compatible endpoint.
- Whether native Gemini function calling belongs in this issue or whether the
  initial scope should be text generation and streaming only.

**Comment:** [Approach and clarification questions](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4686274446)

### Proposed Implementation Plan

After the API and scope questions are resolved, my plan is to:

1. Confirm the correct provider structure available on the PR target branch.
2. Add Gemini provider metadata and authentication configuration using
   `GEMINI_API_KEY`.
3. Implement `IModelProvider` methods: `getConfig`, `invoke`, and `stream`.
4. Validate provider input with the existing `TextModelInputSchema`.
5. Translate Nous messages and system prompts into the selected Gemini API
   request format.
6. Parse Gemini responses, streaming events, usage metadata, and errors.
7. Integrate Gemini using the project’s approved provider discovery workflow.
8. Add focused tests for requests, responses, streaming, authentication,
   errors, aborts, and provider discovery.
9. Run the project’s documented typecheck, generated-file check, and provider
   test suite before opening a pull request.

### Current Blockers

- Waiting for clarification on native Gemini API versus Google’s
  OpenAI-compatible endpoint.
- Waiting for clarification on whether native function calling is in scope.
- The documented certified provider-leaf workflow is not fully present on the
  current PR target branch.

### Next Step

Once the maintainer confirms the API and feature scope, I will begin Phase III
implementation on `google-gemini-model-provider`.

---

## Project Information

**Project:** Nous Core  
**Repository:** [orthogonalhq/nous-core](https://github.com/orthogonalhq/nous-core)  
**Issue:** [Adapter: Google Gemini Model Provider](https://github.com/orthogonalhq/nous-core/issues/71)  
**Issue Status:** Open  
**Maintainer Feedback:** Updated provider adapter specification and PR target provided  
**Issue Assignment:** Assigned to `ramaiyaKushal`  
**Phase II Status:** Local setup and investigation complete; awaiting clarification

---

## Resources

- [Nous Core repository](https://github.com/orthogonalhq/nous-core)
- [Nous Core issue #71](https://github.com/orthogonalhq/nous-core/issues/71)
- [My issue outreach comment](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4639510920)
- [Maintainer provider adapter update](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4684774025)
- [My approach and clarification questions](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4686274446)
- [Provider adapter contributor documentation](https://docs.nue.orthg.nl/docs/development/provider-adapters)
