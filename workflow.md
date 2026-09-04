**Workflow:**

Understand → Explore → Find references → Clarify → Plan → Approve → Implement → Test → Review → Fix → Verify again

---

# 1. Understand the task and repository

### Prompt

We have a new feature to add. Before changing anything, help me understand the task and how the existing application works.

We have this task.

Do not modify anything yet.

First understand the repository and the task.

1. Inspect the repository structure.
2. Find the modules relevant to this task.
3. Trace how the current functionality works.
4. Search for similar or adjacent implementations.
5. Find the closest existing implementation we can use as a reference.
6. Inspect relevant tests and repository conventions.
7. Identify ambiguities, assumptions, edge cases, and potential regression risks.

I want to reuse the existing architecture and patterns wherever possible rather than creating a parallel implementation.

Report your findings first. Do not change any files.


### Technique

Do not let Codex jump directly into coding. Let it investigate first.

---

# 2. Find the closest existing implementation

### Prompt

> Now search the repository for the closest existing features or patterns that we can reuse for this feature.
>
> Look at both the backend and frontend. Find similar APIs, services, database models, components, forms, validation, permissions, and tests.
>
> Tell me which existing implementation is the best reference and why.
>
> I want to extend or adapt existing patterns where possible rather than creating a new approach from scratch.
>
> Don't modify anything yet.

### Technique

Ask:

> "Where does the codebase already solve something similar?"

This often gives a much better starting point than asking Codex to design everything itself.

---

# 3. Run a clarification / discovery session

### Prompt

> Before we make a plan, let's have a short discovery session.
>
> Separate what we know into:
>
> **Verified requirements** — explicitly required by the task or supported by the codebase.
>
> **Reasonable assumptions** — things we can infer from existing conventions.
>
> **Open questions** — things that could change the implementation and need my decision.
>
> Ask me only the questions that genuinely require clarification. Don't ask me things you can determine by inspecting the repository.
>
> After I answer, update your understanding of the feature.

### Example

**Verified:** The ticket says users can filter contacts by status.

**Reasonable assumption:** The filter should use the same query/filter pattern already used by another list endpoint.

**Open question:** Should multiple selected statuses use OR or AND?

---

# 4. Think through the complete feature

### Prompt

> Now think through the feature end-to-end before implementation.
>
> Explain how you think it should work from the user's action in the frontend all the way through the backend and database, and then back to the UI.
>
> Cover:
> - frontend behavior and UX
> - API/interface changes
> - backend logic
> - database changes
> - validation
> - permissions
> - error handling
> - loading/empty/error states
> - edge cases
> - existing behavior that must remain unchanged
>
> Prefer the simplest solution that fits the existing architecture.

### Technique

Make Codex think in a complete flow:

**User → Frontend → API → Backend → Database → Backend → API → Frontend**

---

# 5. Create the implementation plan

### Prompt

> Based on everything we've discovered, create a concrete implementation plan for the feature.
>
> Split it into:
>
> **Backend**
> - files/modules to change
> - API changes
> - business logic
> - database/migration changes
> - validation and permissions
>
> **Frontend**
> - components/pages to change
> - UI behavior
> - state/data fetching
> - loading, empty, and error states
>
> **Integration**
> - how frontend and backend will communicate
> - request/response shape
> - any compatibility concerns
>
> **Testing**
> - backend tests
> - frontend tests
> - integration/e2e tests where appropriate
> - important edge cases
>
> **Verification**
> - tests
> - lint
> - type checking
> - build
>
> Use existing patterns from the repository wherever possible.
>
> Keep the plan focused on the smallest change that correctly satisfies the task.
>
> Don't implement yet. Show me the plan first.

---

# 6. Break the plan into implementation tasks

### Prompt

> Turn the approved plan into a small sequence of implementation tasks.
>
> Order them based on dependencies and risk.
>
> For each task, tell me:
> - what we are changing
> - why
> - which existing implementation we are following
> - how we will verify it
>
> Keep the tasks small enough that we can implement and verify them one at a time.
>
> Don't create unnecessary abstractions or unrelated refactoring.

### Technique

A common order is:

1. Database/model
2. Backend logic/API
3. Backend tests
4. Frontend integration
5. Frontend UI
6. Frontend tests
7. Full verification

But adapt this to the repository.

---

# 7. Implement carefully

### Prompt

> The plan is approved. Implement the feature according to the plan.
>
> Work incrementally rather than changing unrelated parts of the repository.
>
> Follow the existing patterns we identified. Reuse existing utilities, components, services, validation, and API conventions where appropriate.
>
> Don't introduce new abstractions unless they are actually needed.
>
> After each meaningful part of the implementation, check that it is consistent with the rest of the system.
>
> Do not modify unrelated code.
>
> When you're finished, don't just say "done." Give me the verification results and wait for the final review.

---

# 8. When Codex says "done" — verify with evidence

### Prompt

> Don't assume the implementation is correct just because it compiles or because some tests pass.
>
> Verify the feature against the original requirements and the implementation plan.
>
> Check:
> - every requirement is implemented
> - backend behavior
> - frontend behavior
> - API integration
> - validation
> - permissions
> - error handling
> - important edge cases
> - existing behavior and possible regressions
>
> Run the relevant tests, linting, type checking, and build commands used by this repository.
>
> For every verification step, tell me:
> - what you ran
> - whether it passed
> - what it actually proves
> - any warnings or failures
>
> If something fails, investigate the root cause and fix it. Don't hide failures or simply work around them.

### Important

**"It works" is not evidence.**

Evidence is:

> "I ran X, it passed, and this verifies Y."

---

# 9. Review the final diff

### Prompt

> Now review the final git diff carefully.
>
> Explain every changed file and why it changed.
>
> Check whether:
> - every change is necessary for the feature
> - the implementation follows existing repository patterns
> - there are unrelated changes
> - there is duplicated or unnecessary code
> - the frontend/backend integration matches
> - the API contract is correct
> - tests actually cover the important behavior
>
> If you find something questionable, don't change it yet. Explain it to me first.

### Remember

**Diff = the exact changes made to the repository.**

Use it to answer:

> "Did we change only what we intended?"

---

# 10. Senior engineer / PR review

### Prompt

> Now forget that you wrote the implementation and review it as a senior engineer reviewing someone else's pull request.
>
> Be critical rather than trying to justify the implementation.
>
> Look for:
> - bugs
> - incorrect assumptions
> - missing requirements
> - edge cases
> - security or authorization problems
> - validation problems
> - data consistency issues
> - error handling problems
> - concurrency issues where relevant
> - frontend/backend integration problems
> - performance concerns
> - unnecessary complexity
> - duplicated logic
> - violations of existing conventions
> - weak or missing tests
> - possible regressions
>
> Rank findings:
>
> **P0/P1:** Must fix before considering the task complete 
> **P2:** Should fix 
> **P3:** Optional improvement
>
> For every finding, point to the relevant code and explain why it matters.
>
> Do not make changes yet. Give me the review first.

---

# After the review: Fix and re-verify

If Codex finds a real issue:

> Good catch. Fix the confirmed issue without making unrelated changes. Then rerun the relevant tests and verification. Finally, show me the updated diff for the fix.

Then ask:

> Now verify again that the fix solved the issue and did not introduce a regression.

---

# Useful questions to ask Codex during the interview

Use these naturally rather than dumping all of them at once:

- "Why did you choose this approach?"
- "Where does the repository already do this?"
- "Can we reuse that instead?"
- "Why do we need this new abstraction?"
- "What happens if this input is empty?"
- "What happens if the record doesn't exist?"
- "What happens if the user isn't authorized?"
- "Could this change break existing behavior?"
- "Which test proves this requirement?"
- "What does this test actually cover?"
- "Show me the evidence."
- "What assumptions are you making?"
- "What would you be worried about if this went into production?"


