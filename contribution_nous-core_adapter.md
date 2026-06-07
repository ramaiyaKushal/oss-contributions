# Contribution 1: Google Gemini Model Provider

**Contribution Number:** 2  
**Student:** Kushal Atul Ramaiya  
**Issue:** [orthogonalhq/nous-core#71](https://github.com/orthogonalhq/nous-core/issues/71)  
**Week:** 1  
**Status:** Phase I Complete

---

## Why I Chose This Issue

The issue
matches my interest in AI model integrations and gives me an opportunity to learn how different model providers connect to a shared application interface.

I also chose it because the issue has a clearly defined scope and acceptance criteria, making it suitable for a focused open-source contribution.

---

## Understanding the Issue

### Problem Description

Nous Core supports multiple AI model providers through a shared
`IModelProvider` interface. However, it does not currently include a provider implementation for Google's Gemini API.

Issue #71 requests a new Gemini provider that follows the patterns used by the existing OpenAI and Ollama providers.

### Expected Behavior

The completed Gemini provider should:

- Implement `invoke`, `stream`, and `getConfig`.
- Validate input with `TextModelInputSchema`.
- Handle Gemini's streaming API format.
- Be exported from `self/subcortex/providers/src/index.ts`.
- Include tests in `self/subcortex/providers/src/__tests__/`.

### Scope Boundary

The issue explicitly states that `IModelProvider` and `TextModelInputSchema` should not be modified.

### Affected Components

- `self/subcortex/providers/src/gemini-provider.ts`
- `self/subcortex/providers/src/index.ts`
- `self/subcortex/providers/src/__tests__/`

---

## Week 1 Progress

### Issue Selection

I selected [orthogonalhq/nous-core issue #71](https://github.com/orthogonalhq/nous-core/issues/71), titled **“Adapter: Google Gemini Model Provider.”**

### Initial Outreach

I commented on the issue to introduce myself, explain that I wanted to work on it as part of my CodePath AI Capstone Project, and ask the maintainer for assignment and any additional guidance.

**Comment:** [Issue comment #4639510920](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4639510920)

At the end of Week 1, I have not yet received a maintainer response or formal assignment.

---

## Project Information

**Project:** Nous Core  
**Repository:** [orthogonalhq/nous-core](https://github.com/orthogonalhq/nous-core)  
**Issue:** [Adapter: Google Gemini Model Provider](https://github.com/orthogonalhq/nous-core/issues/71)  
**Issue Status:** Open  
**Maintainer Feedback:** No response received yet  
**Issue Assignment:** Awaiting maintainer response

---

## Resources

- [Nous Core repository](https://github.com/orthogonalhq/nous-core)
- [Nous Core issue #71](https://github.com/orthogonalhq/nous-core/issues/71)
- [My issue outreach comment](https://github.com/orthogonalhq/nous-core/issues/71#issuecomment-4639510920)
