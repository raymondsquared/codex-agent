---
name: review
description: Runs a devil's advocate review at any phase to challenge assumptions, surface risks, and propose alternatives before proceeding.
---

Your task:

1. Determine the current phase and artefacts to review.
   - Valid phases include: discovery, PRFAQ, PRD, planning, build, testing, release, and operations.
   - If no phase is provided, infer it from the latest context and continue.

2. Load and apply the specialist persona from `.agents/specialists/devils-advocate.md`.
   - Use that persona's tone and responsibilities for this review.
   - Do not replace normal delivery; this is a focused challenge pass.

3. Review all relevant decisions, assumptions, and outputs in scope for the current phase.
   - Product: assumptions, prioritisation, customer value, and market claims.
   - Architecture: design choices, scale, reliability, security, and cost.
   - Coding: implementation complexity, dependencies, tests, performance, and observability.

4. For each challenge, provide:
   - Claim: the decision or assumption being challenged
   - Challenge: the specific concern or counter argument
   - Severity: critical | high | medium | low
   - Evidence needed: what would satisfy this concern
   - Alternative: at least one different approach

5. Prioritise challenges by severity and limit output to material risks.
   - Focus on issues that could cause rework, outages, security exposure, or wasted investment.
   - Accept justified decisions when evidence is adequate; do not challenge endlessly.

6. End with a concise recommendation:
   - `APPROVED` if no unresolved critical/high challenges remain.
   - `IN_REVIEW` if medium/low concerns remain.
   - `REJECTED` if unresolved critical/high concerns remain.

   When the result is `REJECTED`, the Orchestrator enters the fix -> re-review loop
   (max 2 iterations). Include a `reviewIteration` field in your output so the
   Orchestrator knows which iteration this is.

   Output format:
   ```
   Result: APPROVED | IN_REVIEW | REJECTED
   reviewIteration: <number>
   Unresolved CRITICAL/HIGH: <count>
   ```

   `IN_REVIEW` and `APPROVED` results are advisory and do not block progress.
