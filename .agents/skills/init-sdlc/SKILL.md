---
name: init-sdlc
description: Initialises the SDLC workflow for all infrastructure changes, ensuring traceability, requirement readiness, and compliance with organisational process before any Terraform or MCP action.
---

Your task:

1. Jira Ticket Check (ALWAYS Ask)
   - Ask the user: "Do you have a Jira ID for this work? Please share the Jira ID (e.g. Jira-1234) so I can pull in the requirements, descriptions, acceptance criteria."
   - If YES: Use the Jira MCP tool (or ask user to paste key fields) to fetch:
     - Title
     - Summary
     - Description
     - Acceptance criteria
     - Labels / components (often map to modules or environments)
     - Linked Jira IDs (ARDs, dependencies, blockers)
     - Assignee and reporter
     - Fix version / sprint
   - If NO: STOP immediately. Inform the user that all infrastructure changes must be traceable to a Jira item before any work can begin. Do not proceed until a valid Jira ID is provided (e.g. Jira-1234).
   - If Jira MCP tool is unavailable, ask the user to paste the above key fields from Jira directly into the conversation for context gathering.

2. Requirement Clarification
   - After gathering Jira context, confirm with the user on the description, requirements, and acceptance criteria.
   - Resolve any ambiguities before moving forward.

3. Check Jira Item Requirement Readiness
   - Do not make any changes to Title / Summary field.
   - Ensure the Jira item description follows the format: `As a {{PERSONA}}, I want {{FEATURE}}, so that {{REASON/BENEFIT}}.`
     - If not, update the description accordingly.
   - Check that the Acceptance Criteria are written in the EARS (Easy Approach to Requirements Syntax) format.
     - If not, update the Acceptance Criteria to follow EARS format.
   - When all the above are done, change the `Status` of this Jira item to `In Progress`.

4. Check Latest Documentation
   - Use Context7 to fetch and review the latest documentation for:
     - AWS
     - Terraform
   - If Context7 is unavailable, feel free go to the next step, but make sure to review the latest AWS and Terraform documentation on the official websites before making any changes.

5. Create Branch on Git
   - Using the Jira ID confirmed in steps 1 and 2, create a branch on the remote repository named:
     - `{{PREFIX}}/feature-{{FOURDIGITSNUMBER}}-{{SHORTTITLE}}`
     - Replace `{{FOURDIGITSNUMBER}}` with the Jira ID and `{{SHORTTITLE}}` with a brief lowercase blob from Jira Summary (max 14 characters, no symbols).
     - Choose the correct prefix based on the type of work:
       - `feature/`: new features or enhancements
       - `release/`: release preparation branches
       - `hotfix/`: urgent fixes for production issues
       - `support/`: long term support or maintenance branches
     - Example: `feature/jira-{{FOURDIGITSNUMBER}}-{{SHORTTITLE}}` -> `feature/jira-1234-adds3bucket`

6. Only Then Proceed To Build Phase
   - Take time to understand the current project structure and review the relevant codebase, modules, and environment configuration before making any changes.
   - Begin the build/implementation phase for the current Jira ID based on the confirmed requirements and acceptance criteria.
   - Proceed with infrastructure code changes, CI/CD setup, or other deliverables as specified by the Jira item.
