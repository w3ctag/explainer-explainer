---
name: explainer-preflight
description: Use when reviewing a web platform explainer or proposal before TAG design review, when asked to check an explainer against TAG documents, when preparing for a design review submission, or when self-assessing an explainer's readiness. Also use when the user shares a WICG, Open UI, or explainer URL and asks "is this ready for TAG review", "what would TAG flag", or "what's missing." Not for reviewing finished W3C specifications, implementations, code, TAG review responses, or for evaluating TAG reviews themselves.
---

# Explainer Pre-Flight

Self-review for web platform explainers before they go to TAG design review. Walks the TAG reference documents and reports only gaps — where the explainer is thin or silent on things those documents ask about. A good explainer gets a short report; a thin one gets a longer one. Output is proportional to problems.

The tool catches mechanical gaps (missing sections, unanswered S&P questions, ignored design principles). It does not replicate the architectural wisdom that makes TAG reviews valuable ("should this be part of Popover instead?"). That is the reviewer's job.

## When not to use

- Reviewing a finished W3C specification → use spec-review tooling instead.
- Reviewing an implementation or code → this is a design-review tool, not a code reviewer.
- Responding to TAG review feedback you've already received → different workflow (drafting a response to the TAG, not preparing for one).
- Evaluating a TAG review itself.
- Reviewing a charter, Working Group process doc, or horizontal-review request — these aren't explainers.

## Reference documents

Fetch all seven from the network every run. Never rely on memorized copies — guidance changes.

| # | Document | URL |
|---|----------|-----|
| R1 | Explainer Explainer | https://w3ctag.github.io/explainer-explainer/ |
| R2 | Web Platform Design Principles | https://www.w3.org/TR/design-principles/ |
| R3 | Self-Review Questionnaire: Security and Privacy | https://www.w3.org/TR/security-privacy-questionnaire/ |
| R4 | Ethical Web Principles | https://www.w3.org/TR/ethical-web-principles/ |
| R5 | Societal Impact Questionnaire (Editor's Draft) | https://w3ctag.github.io/societal-impact-questionnaire/ |
| R6 | Accessibility Screener | https://w3ctag.github.io/accessibility-screener/ |
| R7 | Internationalization Best Practices for Spec Developers | https://www.w3.org/TR/international-specs/ |

R2 is large. Dispatch a subagent to extract only the principles relevant to the proposal's domain. R5 is an Editor's Draft; note that when citing.

## Rules for every finding

1. **Cite or drop.** Each finding must quote a specific passage from one of R1–R7 and link the source anchor. If Step 5 (Citation Verification) can't confirm the anchor exists and supports the claim, the finding goes in the bin.
2. **Label the source.** Prefix findings with the document tag: *"R3, §2.16:"*, *"R1, §introduction (key components):"*. For tool-derived analysis (Step 4f), prefix with *"Tool's analysis:"* so the reader knows the documents don't explicitly require it.
3. **Describe what the documents say, not what reviewers will do.** Never *"the TAG typically…"*, *"reviewers often…"*, *"Anne would push back on this."* LLMs guess wrong about human behavior. Stick to what the fetched documents state.
4. **Report gaps only.** Don't list things the explainer handles adequately. Don't compliment. Authors can read praise elsewhere.

## Severity

Three tiers. Apply them consistently — inflation makes the report feel like a failing grade and authors stop reading.

- **Blocking** — the document explicitly requires it *and* the explainer is completely silent. Example: R1 lists "Security and Privacy Considerations" as a key component and the explainer has no such section.
- **Gap** — a document asks a relevant question and the explainer doesn't answer. Example: R3 Question 2.3 applies but isn't addressed. The review could still proceed; TAG would likely raise it.
- **Suggestion** — something the tool's own analysis flags, or a principle that's tangentially relevant. The documents don't mandate it. Example: the explainer could strengthen the alternatives section.

**Calibration test** before writing a finding: can you point to a verbatim requirement in a fetched document? If yes → Blocking or Gap depending on silence vs. partial answer. If no → Suggestion.

## Finding rollup

Structural gaps imply downstream gaps. An explainer with no Accessibility Considerations section at all will, by definition, fail to address R6 Q1, Q5, Q6, etc. Emitting each of those as a separate Gap makes a short-handed explainer look catastrophically broken — it doesn't. Roll them up.

**Rule:** when a structural finding (e.g., "no Accessibility Considerations section" from R1 §introduction key component 6) would force downstream question-level Gaps, emit the structural finding *once* and enumerate the implied downstream questions *inside* it. Do not count each implied question as a separate Gap.

**Worked example** — an explainer with a Privacy section but no Security section, no Accessibility section, and no i18n section:

- ✅ **Gap** — R1 §introduction key component 6 + R3 §2.16: no dedicated Security Considerations section, no Accessibility Considerations section, no Internationalization Considerations section. When you write the Accessibility section, R6 Q1 (new visual UI), Q5 (input interpretation change), and Q6 (modality constraint) will apply to this feature and need specific answers.
- ❌ Do not emit: separate Gaps for R3 §2.16, R6 Q1, R6 Q5, R6 Q6, and the R1 missing-section finding. That's five Gaps counting the same underlying problem five ways.

If the explainer *does* have an Accessibility section but it's thin, then R6 questions become separate Gaps — one per unanswered question — because each is an independent authoring failure, not an implied consequence of a missing section.

## Workflow

The six steps are serial. Each has a stop condition.

### Step 0: Confirm the URL is an explainer

Before fetching anything else, sanity-check the target:
- Does it have a problem statement, proposed API/solution, and at least some use cases? That's an explainer.
- If it looks like a polyfill README, a spec document (has "Status of this Document"), a changelog, a redirect stub, or a charter, **STOP**. Report what the URL actually is. If the explainer lives elsewhere (linked from the repo, the TAG design-review issue, or a WICG/Open UI page), point at it and ask whether to analyze that.
- Note the origin. If the explainer is from a formal Working Group (rather than an incubation venue like WICG/Open UI), check whether it already links to horizontal-review issues in `w3c/a11y-request`, `w3c/i18n-request`, `w3cping/privacy-request`, or `w3c/security-request`. If yes, cite those in the report rather than re-deriving the concerns the horizontal-review groups already raised.

Why this matters: running structural checks against a non-explainer produces ten "section missing" findings that are noise, not signal. Gemini did this on the interesttarget polyfill README in v1 testing.

### Step 1: Fetch and classify

1. Fetch the explainer. If it 404s, try one retry with a likely fallback URL (e.g., `raw.githubusercontent.com` for GitHub blob links, the repo's `main` branch if a specific commit is referenced). If the TAG design-review issue for this proposal is accessible, check it — the real explainer URL is usually linked there. Only STOP if no fallback finds a reachable explainer.
2. Fetch R1–R7. Note any fetch failures; don't substitute from memory. If any reference document 403s, try the raw URL or an alternate mirror before marking it unfetched.
3. Classify maturity:
   - **Skeletal** — most R1 key components missing.
   - **Developing** — sections present but thin.
   - **Polished** — substantive content throughout.

Maturity sets the voice, not the rigor. For skeletal: *"when you write this section, address…"*. For polished: *"R3 §2.8 asks X; the explainer addresses only Y."*

### Step 2: Structural completeness against R1

R1's `#introduction` lists six key components (fetch for the current list; at time of writing they are: Discussion Venues, User-Facing Problem, Proposed Approach, Practical Use Cases, Alternatives Considered, and "Accessibility, Internationalization, Privacy, and Security Considerations"). For each:
- **Missing** — flag as **Blocking** and quote what R1 says that section should contain.
- **Present but thin** — flag as **Gap**, quote the R1 requirement, point at the relevant paragraph in the explainer that falls short.

If the explainer is skeletal enough that Step 2 alone would produce 5+ Blocking findings — **STOP** here. Report the structural gaps and recommend the author come back after the explainer is substantive. Running deeper checks on a stub produces noise.

### Step 3: Detect which deeper checks apply

Scan the explainer for signals, then decide which of R3–R7 will run:

| Signal | Triggers |
|--------|----------|
| Any web-facing feature | R2 Design Principles — **always** |
| UI, forms, input, keyboard, visual rendering, audio | R6 Accessibility — **always if feature has user-facing surface** |
| Text, locale, encoding, bidi, non-Latin scripts, dates/numbers | R7 i18n |
| User data, permissions, device/sensor access, storage | R3 S&P |
| Societal-scale impact, surveillance, centralization, AI/ML, advertising | R4 + R5 Societal |

Report which checks ran and which were skipped (one line each). Skipped ≠ hidden — the author should see what was considered.

### Step 4: Walk the applicable reference documents

Run each triggered check and collect findings. Report only gaps.

#### 4a. Design Principles (R2) — always

R2 is large. Use a subagent to extract principles relevant to this proposal. For each relevant principle, check whether the explainer addresses it. Surface only principles the explainer ignores or contradicts. Quote the principle and explain why it applies to this feature.

#### 4b. Security & Privacy (R3)

R3 has 22 numbered questions (section 2). A subagent can check each against the explainer. Flag only questions that (a) clearly apply to this feature and (b) the explainer doesn't answer. Also verify R3 §2.16 ("Does this specification have both 'Security Considerations' and 'Privacy Considerations' sections?"): the explainer should have dedicated Security Considerations *and* Privacy Considerations sections — if either is missing or merged without calling both out, that's a Gap.

#### 4c. Accessibility (R6) — walk every question

R6 is short (7 questions at time of writing). Fetch it and walk each question in order. For each:
1. Answer the question based on the explainer ("Yes", "No", or "Unclear").
2. If the answer is Yes or Unclear, check whether the explainer contains an accessibility discussion that addresses the implication. R1's sixth key component explicitly demands this.
3. If the explainer has no accessibility section at all, emit **ONE rolled-up Gap** per the Finding rollup rule — not seven separate Gaps. The rolled-up finding cites R1 §introduction key component 6, and *enumerates inline* which of Q1–Q7 would apply and what the author needs to address when they write the section. Do not emit a separate Gap per question in this case.
4. If the explainer *has* an accessibility section but it doesn't address a specific question's implication, *that* is a separate Gap — each is an independent authoring failure, not an implied consequence.
5. After Q1–Q7, note whether the explainer acknowledges R6 itself (many authors don't know to file a screener issue — R6's own text says the screener tags APAWG on a filed GitHub issue for horizontal-review preparation). If the explainer doesn't mention filing a screener, that's a **Suggestion**.

Cite R6's question text verbatim and the form-field anchor (`#q1_yes` through `#q7_yes` — R6 doesn't have heading anchors). If a Yes triggers broader WCAG concerns (time-limited UI, keyboard inaccessibility), cite WCAG by section.

**When to point at FAST.** R6 is a short screener, not a full accessibility review. If Q1, Q4, or Q5 answered Yes and the UI is substantive (not a trivial affordance like a color picker's cursor change), note that the explainer's accessibility section should engage the APA FAST checklist for deeper self-assessment: https://w3c.github.io/fast/checklist.html. FAST covers visual rendering, user input, semantics, time-based media, audio, i18n hooks, APIs, protocols, and fallback — roughly 80 checkpoints. Flag this as a **Suggestion** (the TAG documents don't currently require FAST engagement; APA does for horizontal review).

#### 4d. Internationalization (R7)

Only if Step 3 triggered. Walk R7's checklist items relevant to the feature — text direction, locale sensitivity, formatting of numbers/dates/currencies, Unicode handling. Flag only unaddressed items.

#### 4e. Societal Impact (R4 + R5)

Only if Step 3 triggered. R4 is the published Ethical Web Principles; R5 is an Editor's Draft of the Societal Impact Questionnaire — label R5 citations as "ED, subject to change." Focus on principles/questions the explainer doesn't engage with when they clearly apply (e.g., a surveillance-capable API that doesn't discuss R4's privacy principle).

#### 4f. Platform Fit *(Tool's analysis, informed by R1 and R2)*

This is the tool's highest-value check per validation. Four sub-checks, in priority order:

- **Closest existing primitive.** What is the closest existing web platform feature to this proposal? (E.g., `<input type="color">` for color pickers, Popover API for overlay UI, MediaCapabilities for media adaptation.) Does the explainer explicitly argue why extending that primitive was rejected? R2 §new-features ("Add new capabilities with care") says: *"Add new capabilities to the web with consideration of existing functionality and content."* If the explainer proposes a new API surface without addressing the obvious existing primitive, flag it as **Gap** (or **Blocking** if there is no Alternatives Considered section at all). In validation this is the most common TAG architectural objection.

- **Interop coercion risk.** If the feature only delivers value when all browsers implement it, and there's no graceful degradation path, sites may pressure users into specific browsers. R2 §devices-platforms ("Support the full range of devices and platforms") and the priority-of-constituencies principle are relevant. Flag if the explainer doesn't discuss behavior when the feature is absent and whether sites could weaponize feature detection.

- **Cross-feature interactions.** Does the explainer address how this interacts with other platform features sharing the same user-facing surface? Examples: hover-to-preview interacting with Speculation Rules prefetch-on-hover; new window APIs overlapping with Popover; new sensor APIs overlapping with existing Generic Sensor APIs. If it shares a surface and the interaction is unaddressed, flag it.

- **Evidence of utility.** R1 §end-user-need ("Explain the End-User's need") asks authors to ground the proposal in a concrete user need. Does the explainer provide *evidence* that the solution works — concrete developer feedback, origin-trial data, bug links, prototype results? Theoretical scenarios are not evidence. Flag if none is provided.

### Step 5: Citation verification

Before writing the report, verify every citation. Dispatch a subagent (or do it inline if no subagents are available). For each finding:

1. **Does the anchor exist** in the fetched document?
2. **Does the content at that anchor support the claim** being made?

Remove any citation that fails. If a finding has no surviving citation, drop the finding. This is the check that prevents the most damaging failure mode: attributing advice to the TAG that the documents contradict.

### Step 6: Write the report

Structure findings thematically, not as a flat list:

> **Structural (R1):** [Blocking/Gap findings here]
>
> **Security and Privacy (R3):** [findings]
>
> **Accessibility (R6, walking questions 1–7):** [findings, tied to specific questions]
>
> **Platform fit (Tool's analysis):** [findings]
>
> **Skipped checks:** i18n (no text handling), Societal (no surveillance surface) — one line each.

Tone: *"R3 §2.8 asks about X, and the explainer doesn't address it yet"* — not *"your explainer fails to…"*. Authors should finish reading knowing what to add, not feeling judged.

End with one line: **"Ready for TAG review"** or **"N Blocking, M Gap findings — address Blocking items first."** Nothing else.

## Common failure modes

| Failure | Why it hurts | Prevention |
|---------|--------------|------------|
| Analyzing a non-explainer (polyfill README, spec, redirect stub) | Produces structural noise, not findings | Step 0 |
| Citing an anchor without re-reading the content at it | Misattribution — the document may say the opposite | Step 5 |
| Running against memorized/cached documents | Stale guidance; references update | Step 1: fetch every time |
| Praising work done well | Verbosity, especially with R2 | Rule 4: gaps only |
| "The TAG will…" / "Reviewers typically…" | Undermines credibility | Rule 3 |
| Severity inflation (everything Blocking) | Authors disengage | Calibration test before labeling |

## Evaluating changes to this skill

Test cases and assertions live in `evals/evals.json` alongside this skill. Four validated test explainers cover the calibration range:

| Explainer | TAG outcome | Expected skill output |
|-----------|-------------|-----------------------|
| EyeDropper API | Satisfied (#587) | 0 Blocking, ≤4 findings total |
| Spell Check Dictionary | Satisfied with concerns (#1191) | 0 Blocking (structure present), 5–10 Gaps |
| interesttarget (Interest Invokers) | Reopened after initial review (#1058) | 3+ Blocking, detailed platform-fit findings |
| Compression Dictionary Transport README | Not an explainer | Step 0 rejects, no structural findings |

When editing this skill:
1. Snapshot the current version to `SKILL.md.vN-snapshot` before changing anything.
2. Run the snapshot against all four test explainers (save to `evals/iteration-N/old_skill/`).
3. Run the new version against the same four (save to `evals/iteration-N/with_skill/`).
4. Diff the findings. Each change should be explicable in one sentence — *"removed the always-flag-BFCache rule → BFCache Gap dropped for EyeDropper, correctly"*.

If subagents are available, run the eight runs (4 explainers × 2 skill versions) in parallel. If not, run sequentially and diff the output files.
