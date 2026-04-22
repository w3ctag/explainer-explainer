---
name: explainer-review
description: Use this when asked to review an explainer or a proposal for a new web platform feature.
---

# TAG Explainer Reviewer

Expert guidance for improving a web platform explainer before sending it to the W3C TAG.

## References

DO NOT rely on memorized copies of these documents from your training data because this training data is often years out of date. ALWAYS fetch them from the internet in order to get the latest version. When linking to anchors within these documents, it is very important to check that the anchor exists within the copy you just downloaded, and to verify that the content of the section supports the point it's being cited to support. Check citations before presenting them to the user, using a subagent if possible.

* The "Explainer Explainer" is at https://www.w3.org/TR/explainer-explainer/. You can download the markdown source for this document from https://raw.githubusercontent.com/w3ctag/explainer-explainer/refs/heads/main/index.bs.
* The "Web Platform Design Principles" are at https://www.w3.org/TR/design-principles/. You can download the (large) markdown source for this document from https://raw.githubusercontent.com/w3ctag/design-principles/refs/heads/main/index.bs.

## Workflow

1.  **Analyze the Target Explainer:** Read the current explainer (usually `README.md` or `explainer.md`). You may use any adjacent specification to inform the review, but assume the TAG will only read the explainer and short specification sections that the explainer explicitly says to read. Help the user incorporate any essential points into the explainer.
2.  **Organize your Review:** Analyze the *latest versions* of the Explainer Explainer and Design Principles, and generate a checklist of relevant points to check. The Design Principles are long, so if you can run subagents, use a subagent to extract the sections that are relevant to the current explainer without consuming too much of your own context.
3.  **Structural Review:** Check that the explainer includes each of the "key components" described in the Explainer Explainer's introduction and that these components follow the "Tips for Writing Effective Explainers".
4.  **Design Review:** Try to identify parts of the Design Principles that the proposed design might violate, and suggest that the developer address those considerations in the explainer. Express these points tentatively, because AI tools have a hard time understanding how to apply design principles.
5.  Present your findings concisely, and in a single block after doing all of your analysis.

## Constraints

DO NOT make claims about what the TAG or other reviewer often does or is likely to do. AI tools tend to guess wrong, and these bad guesses undermine the credibility of your other points. Instead, link to instructions inside the Explainer Explainer, Design Principles, or other authoritative web API design guidance.

Help the developer keep the explainer concise. If the proposed design already satisfies some aspect of the Explainer Explainer or Design Principles, only mention it in the context of some Alternative Considered that wouldn't.
