---
name: validate-handoff
description: Validates a handoff JSON file against the Master Handoff Envelope schema after each phase to ensure completeness, correct structure, and traceability before proceeding.
---

Your task:

1. Locate the handoff file to validate.
   - Expected path: `docs/handoffs/handoff-{{SESSIONID}}-{{SOURCE}}-{{TARGET}}-{{YYYYMMDD}}.json`
   - If no path is provided, find the most recent handoff JSON in `docs/handoffs/`.

2. Read the handoff file and verify every check below.
   - If any REQUIRED check fails, report the failure, fix the payload, and re-validate.
   - Do not proceed to the next phase until all REQUIRED checks pass.

3. Pre-Checks
   - [ ] REQUIRED — Handoff file exists at the expected path
   - [ ] REQUIRED — File contains valid JSON (parseable, no syntax errors)
   - [ ] REQUIRED — `.agents/.session` file exists and contains a UUID
   - [ ] REQUIRED — `sessionId` in the handoff JSON matches the value in `.agents/.session`

4. Top-Level Fields — all MUST be present and non-empty:
   - [ ] `handoffVersion` — string (e.g. `"1.0"`)
   - [ ] `timestamp` — ISO-8601 datetime (e.g. `"2026-04-15T12:00:00Z"`)
   - [ ] `sessionId` — UUID format (e.g. `"550e8400-e29b-41d4-a716-446655440000"`)
   - [ ] `productName` — string
   - [ ] `productNameSlug` — lowercase string, no spaces (e.g. `"smartinventory"`)
   - [ ] `sourceAgent` — object
   - [ ] `targetAgent` — object
   - [ ] `artifacts` — object
   - [ ] `payload` — object (may be empty `{}` but must be present)
   - [ ] `workflowState` — object

5. sourceAgent Sub-Fields:
   - [ ] `sourceAgent.agentType` — one of: `orchestrator`, `market-research`, `prfaq`, `prd`, `prototype`, `architect`, `security`, `reviewer`, `project-manager`, `data-engineer`, `software-engineer`, `devops-engineer`, `quality-assurance`
   - [ ] `sourceAgent.phaseCompleted` — non-empty string
   - [ ] `sourceAgent.artifactSummaryGeneratedAt` — ISO-8601 datetime, REQUIRED
   - [ ] WARNING — `artifactSummaryGeneratedAt` must not be earlier than the `last modified` time of any file listed in `artifacts.created`. If it is, the source artefact was patched after the summary was written — flag as STALE and require the sending agent to regenerate the handoff before proceeding.

6. targetAgent Sub-Fields:
   - [ ] `targetAgent.agentType` — non-empty string matching an allowed agent type
   - [ ] `targetAgent.phaseToExecute` — non-empty string

7. artifacts Structure:
   - [ ] `artifacts.created` — is an array
   - [ ] Each item in `artifacts.created` has:
     - [ ] `type` — one of: `markdown`, `html`, `json`
     - [ ] `path` — relative path from project root
     - [ ] `description` — non-empty string
   - [ ] WARNING — Each `artifacts.created[].path` should reference a file that exists in the workspace. Log a warning if not found but do not block.
   - [ ] `artifacts.referenced` — is an array (may be empty)

8. workflowState Sub-Fields:
   - [ ] `workflowState.executionMode` — one of: `manual`, `automatic`
   - [ ] `workflowState.phasesCompleted` — is an array of strings
   - [ ] `workflowState.phasesRemaining` — is an array of strings

9. Format Checks:
   - [ ] `timestamp` matches ISO-8601 pattern: `YYYY-MM-DDTHH:MM:SS` (with optional timezone)
   - [ ] `sessionId` matches UUID pattern: `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`
   - [ ] `productNameSlug` contains only lowercase letters, digits, and hyphens — no spaces or special characters

10. Filename Convention:
    - [ ] Filename matches pattern: `handoff-{{SESSIONID}}-{{SOURCE}}-{{TARGET}}-{{YYYYMMDD}}.json`
    - [ ] `{{SESSIONID}}` in filename matches `sessionId` field value
    - [ ] `{{SOURCE}}` in filename matches `sourceAgent.agentType`
    - [ ] `{{TARGET}}` in filename matches `targetAgent.agentType`
    - [ ] `{{YYYYMMDD}}` in filename matches the date portion of `timestamp`

11. Report the outcome:
    - PASS - All checks above pass. Proceed to next phase.
    - FAIL - Any check failed. List each failed check with the field name, expected value, and actual value. Fix the payload and re-validate.
