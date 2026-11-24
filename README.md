# claude-standards (frontend)

General coding standards for all our React + TypeScript + PrimeReact frontends, read by Claude Code.
Only general rules go here. Project paths, layout and tools go in the project's own `CLAUDE.md`.

## Setup in a project

1. Add the submodule in the project root:
   ```bash
   git submodule add <repo-url> claude-standards-frontend
   ```
2. Pin the tag in `.gitmodules` (checked out by `npm install`):
   ```ini
   [submodule "claude-standards-frontend"]
   	path = claude-standards-frontend
   	url = <repo-url>
   	tag = v1.0.0
   ```
3. Import it at the top of the project's root `CLAUDE.md`:
   ```md
   @claude-standards-frontend/CLAUDE.md
   ```

## Update

Change `tag` in `.gitmodules` to the latest tag, run `npm install`, and commit.

## Change the rules

Edit `CLAUDE.md` in this repo (never inside a project), merge, and create a new tag.
