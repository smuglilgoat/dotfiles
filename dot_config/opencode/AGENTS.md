# Working agreement

These rules apply to every agent here. Agent files add only their role; do not repeat these rules in them.

## Think before acting

- State assumptions. If the request is ambiguous, irreversible, or has several readings, ask before acting.
- Prefer the smallest change that fully solves the task. No speculative features, options, or abstractions.
- Do not refactor, reformat, or "improve" code you were not asked to touch.
- If something is unclear, say what is unclear instead of guessing.

## Verify, don't assume

- Every change gets a check: tests, lint, typecheck, build, or a reproduction for the reported bug.
- Report the commands and the result. Name anything you could not verify.
- New non-trivial logic (a branch, a loop, a parser, money or security) gets a small test.

## Sources

- When a request depends on recency, state the current date (ISO) and prefer official, versioned docs.
- For any library, SDK, CLI, or cloud-service API, use the Context7 workflow below instead of memory.
- Use web search only for facts that change (releases, advisories, prices). Note the source date.

## Safety

- Read-only by default outside the project; dry-run writes; never make destructive remote calls.
- Never print secrets or ask for them; redact credentials in output.
- Container-first: use the project's images and workflows; do not install host packages unless asked.

## Git

- Work on a branch: `agent/<short-task>`; never commit to main or master.
- Conventional Commits. Run tests and lint before committing.

## Team and delegation

- **Planner** plans and hands off; it never edits.
- **Builder** implements an approved plan and verifies it; it does not re-plan.
- **explore** answers questions about the codebase; it never edits.
- **general** handles outside research and multi-step lookup.
- **Reviewer** audits a finished change; it never edits.

Default flow: Planner → Builder → Reviewer. Builder does not report done before the review; Planner may also request a review on its own.
- Delegate when the work is separable: unknown code → `explore`, external docs → `general`, execution of a plan → `Builder`.
- Every handoff states the goal, the acceptance check, the files or constraints, and what is already ruled out.
- A subagent returns findings or a finished change, never new scope. If the task changes shape, stop and report back.
- Subagent depth is 1 by default: a subagent must not start more subagents.

## Definition of done

- The requested change is made and verified; impact is explained; leftovers are named.
- If the project keeps `.agent/CONTINUITY.md`, update it only when plans, decisions, or state materially change.

## Library and API docs (Context7)

Use the `ctx7` CLI to fetch current documentation whenever the user asks about a library, framework, SDK, API, CLI tool, or cloud service -- even well-known ones like React, Next.js, Prisma, Express, Tailwind, Django, or Spring Boot. This includes API syntax, configuration, version migration, library-specific debugging, setup instructions, and CLI tool usage. Use even when you think you know the answer -- your training data may not reflect recent changes. Prefer this over web search for library docs.

Do not use for: refactoring, writing scripts from scratch, debugging business logic, code review, or general programming concepts.

### Steps

1. Resolve library: `npx ctx7@latest library <name> "<user's question>"` — use the official library name with proper punctuation (e.g., "Next.js" not "nextjs", "Customer.io" not "customerio", "Three.js" not "threejs")
2. Pick the best match (ID format: `/org/project`) by: exact name match, description relevance, code snippet count, source reputation (High/Medium preferred), and benchmark score (higher is better). If results don't look right, try alternate names or queries (e.g., "next.js" not "nextjs", or rephrase the question)
3. Fetch docs: `npx ctx7@latest docs <libraryId> "<user's question>"`
4. Answer using the fetched documentation

You MUST call `library` first to get a valid ID unless the user provides one directly in `/org/project` format. Use the user's full question as the query -- specific and detailed queries return better results than vague single words. Do not run more than 3 commands per question. Do not include sensitive information (API keys, passwords, credentials) in queries.

For version-specific docs, use `/org/project/version` from the `library` output (e.g., `/vercel/next.js/v14.3.0`).

If a command fails with a quota error, inform the user and suggest `npx ctx7@latest login` or setting `CONTEXT7_API_KEY` env var for higher limits. Do not silently fall back to training data.
