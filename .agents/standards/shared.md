# Shared Standards

This document contains standards and conventions that ALL specialised agents must follow. Reference this file to ensure consistency across the workflow.

## File Naming Conventions

### Document Files

```
{{TYPE}}-{{PRODUCT}}-{{YYYYMMDD}}.{{EXTENSION}}
```

SEE `.agents/workflow.md` "File Structure After Completion" for the canonical naming table and per-phase file formats.

### Product Name Slug Rules

- Remove spaces
- Use lowercase for words
- Remove any special characters
- Examples:
  - "Teen Fit App" -> `teenfitapp`
  - "AI-Powered CRM" -> `aipoweredcrm`
  - "Real-Time Analytics" -> `realtimeanalytics`

## Quality Checklist for all agents

Before completing any phase output, verify:

### Content Quality

- [ ] All required sections are present
- [ ] Content is specific, not generic placeholder text
- [ ] Data and examples are realistic (use provided context if available)
- [ ] No lorem ipsum or "TBD" placeholders remain
- [ ] Terminology is consistent throughout

### File Quality

- [ ] File saved to correct location (`docs/`)
- [ ] File naming convention followed exactly
- [ ] markdown, JSON and HTML versions created (where applicable)
- [ ] HTML is valid and renders correctly
- [ ] All internal links work

### Handoff Quality

- [ ] Output structured per Handoff Schema
- [ ] Summary fields are under 500 characters
- [ ] All required fields populated
- [ ] Artefact paths are correct and files exist

## Error Message Standards

### User-Facing Errors

```
{{WHAT_HAPPENED}} + {{WHY_IT_HAPPENED}} + {{WHAT_TO_DO_NEXT}}
```

Example:

```
Unable to save document. The documents folder doesn't exist.
Please create a 'documents' folder in your project directory.
```

### Agent Handoff Errors

```json
{
  "error": {
    "code": "DESCRIPTIVE_ERROR_CODE",
    "message": "Human readable description",
    "context": {},
    "actions": ["Suggested action 1", "Suggested action 2"]
  }
}
```

## Version Control

### Document Versioning

- Date in filename serves as version identifier
- Same-date revisions overwrite the original file (Git provides version history)
- Different-date revisions are preserved as separate files

### Change Tracking

For significant revisions:

```markdown
## Revision History

| Date         | Version | Changes                            | Author   |
| ------------ | ------- | ---------------------------------- | -------- |
| {{YYYYMMDD}} | 0.0.0   | Initial creation                   | AI Agent |
| {{YYYYMMDD}} | 0.1.0   | Updated personas per user feedback | AI Agent |
| {{YYYYMMDD}} | 1.0.0   | First big release                  | AI Agent |
```
