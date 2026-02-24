# Codex Agent

Agent setup for Codex (or any agentic framework)

## Workflow

SEE: `.agents/workflow.md` for the agentic workflow

## Commands

Workflows and automation commands are referenced in `AGENTS.md`.

## Model Recommendations

- Deep thinking model for Discovery phase (with `web_search` and `web_fetch`).
- Reasoning model for Design and Plan phases.
- Reasoning model for Review and Devil's Advocate.
- Generic cost optimised LLM model for Build phase.

## Acknowledgements

- [Spec Kit](https://github.com/github/spec-kit)
- [Sample Kiro Prompts](https://github.com/aws-samples/sample-kiro-cli-prompts-for-product-teams/)
- [AGENTS.md](https://agents.md/)
- [OpenAI Cookbook](https://developers.openai.com/cookbook/articles/codex_exec_plans/)
- [Claude](https://code.claude.com/docs/en/sub-agents#explore)
- [Copilot](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-creating)

## Notes

- Use Context7 MCP to get all documentation

## Re running a Phase

1. Identify the phase to re-run.
2. Load the handoff JSON that was input to that phase (not the one it produced).
3. Re run the phase. Same date artefacts overwrite; different date creates new files.
4. Validate the new handoff with the `validate-handoff` skill.
5. Re run any downstream phases whose inputs changed.

## TO DO:

- Link tasks.md to requirements traceability
- Handoff process

- Create deployment agent
- Create operational excellence agent
- Create cost optimisation agent
