# Sample AGENTS.md file

## How to use?

### Add AGENTS.md

Create an AGENTS.md file at the root of the repository. Most coding agents can even scaffold one for you if you ask nicely.

### Cover what matters

#### Project overview

#### Build and test commands

#### Code style guidelines

#### Testing instructions

#### Security considerations

### Extra instructions

Commit messages or pull request guidelines, security gotchas, large datasets, deployment steps: anything you’d tell a new teammate belongs here too.

### Large monorepo? Use nested AGENTS.md files for subprojects

Place another AGENTS.md inside each package. Agents automatically read the nearest file in the directory tree, so the closest one takes precedence and every subproject can ship tailored instructions. For example, at time of writing the main OpenAI repo has 88 AGENTS.md files.

--

## Example

### Dev environment tips

- Use `pnpm dlx turbo run where <project_name>` to jump to a package instead of scanning with `ls`.
- Run `pnpm install --filter <project_name>` to add the package to your workspace so Vite, ESLint, and TypeScript can see it.
- Use `pnpm create vite@latest <project_name> -- --template react-ts` to spin up a new React + Vite package with TypeScript checks ready.
- Check the name field inside each package's package.json to confirm the right name—skip the top-level one.

### Testing instructions

- Find the CI plan in the .github/workflows folder.
- Run `pnpm turbo run test --filter <project_name>` to run every check defined for that package.
- From the package root you can just call `pnpm test`. The commit should pass all tests before you merge.
- To focus on one step, add the Vitest pattern: `pnpm vitest run -t "<test name>"`.
- Fix any test or type errors until the whole suite is green.
- After moving files or changing imports, run `pnpm lint --filter <project_name>` to be sure ESLint and TypeScript rules still pass.
- Add or update tests for the code you change, even if nobody asked.

### PR instructions

- Title format: [<project_name>] <Title>
- Always run `pnpm lint` and `pnpm test` before committing.

---

## Best Practices

### Keep it brief and focused

Too many instructions can confuse the coding agent. Most of OpenAI’s AGENTS.md files are less than 100 lines.

### Unlock agentic loops

Show the agent tools that it can call (linters, screen capture, tests, etc) to verify its work. If you find yourself repeatedly doing the same steps after the agent runs, add those instructions to AGENTS.md.

### Continuously update with real mistakes

Keep a living “Gotchas Codex has hit” section and evolve the file in version control (PRs, reviewers, changelog).

### Point to task-specific .md files

Reference other .md files from the main Agents.md file. For example, a Plans.md that tells the model to design and iterate on a plan before implementing or an Architecture.md that gives more details on the lay of the land.
