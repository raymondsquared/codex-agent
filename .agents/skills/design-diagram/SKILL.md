---
name: design-diagram
description: Analyses current changes and generates a Mermaid diagram in Markdown format. Use this skill whenever the user asks to "visualise changes", "create a diagram", "design diagram", "draw the flow", "make a mermaid", or wants any kind of flowchart, sequence, class, or architecture diagram from code, diffs, or described workflows.
---

Your task:

1. ALWAYS check for changes in the `environments`, `lib` and `modules` folders first for all the changes. Do not move to the next step until you have thoroughly analysed all changes in those folders. This is the primary focus area for this skill, and it is crucial to understand all modifications there before proceeding.

2. Analyse all current changes provided (diff, code, files, or description),

- Identify all new or modified entities (classes, functions, services, components, tables).
- Trace relationships between them (calls, imports, extends, sends data to).
- Map the sequence of operations (what triggers what, in what order).
- Note any decision points, error paths, or external systems.

3. Create a temporary file called `diagram.md` in the working directory. Use this file as a dumping ground for the Mermaid diagram draft in the next steps. This helps keep the main output clean and allows for iterative editing before final confirmation.

4. Confirm your understanding with the user before generating anything.

   Present a do point summary in this format:

   Items created / modified:
   - `EntityName`: what it is and what changed

   Flow of changes:
   - 1: what initiates or starts
   - 2: what happens next
   - 3: outcome or destination

   Diagram type I'll use: flowchart / sequenceDiagram / classDiagram / erDiagram / stateDiagram
   Reason: one sentence explaining why.
   - Do NOT generate any Mermaid syntax yet.
   - Wait for the user to confirm or request corrections before proceeding.

5. Generate the Mermaid diagram in a fenced Markdown code block.
   - Use the confirmed entities, names, and flow exactly, do not invent names.
   - Choose the direction that best suits the content (TD for hierarchies, LR for pipelines).
   - If there are more than 15 nodes, use subgraphs to group related items.
   - Wrap the output in a fenced block with the `mermaid` language tag.

   Example format:

   ```mermaid
   flowchart TD
       A[Start] --> B{Decision}
       B -->|Yes| C[Action]
       B -->|No| D[Other]
   ```

   - After the diagram, add 1–2 sentences noting any simplifications made (e.g. error paths omitted).
   - Do NOT push, save, or commit anything, output only.

6. Verify Mermaid syntax and diagram type:
   - Check that the generated Mermaid code is valid and renders without errors.
   - Ensure the diagram uses the correct type (e.g., flowchart, sequenceDiagram) as required by the user or context.
