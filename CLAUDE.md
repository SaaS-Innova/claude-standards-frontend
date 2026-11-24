# Frontend Coding Standards (General)

These are the company coding standards for all our frontend projects (ReactJS + TypeScript + PrimeReact / PrimeFlex).
Follow them for **every** code change. Project-specific rules (layout, paths, tools) live in the project's own `CLAUDE.md`.

When existing code breaks these rules, follow the rules for **new or changed code**. Don't refactor unrelated code unless asked.

---

## 1. General

- Keep the project in well-structured directories. Use meaningful file and folder names, and group related files together.
- **Order inside classes and services:** public methods first, private methods at the bottom.
- Write self-explanatory code first. Use comments only where they add value (see §4).

### Where to put functions
Decide where a function goes by **what its logic does**, not by where it is first used.

**Test:** if the function would still make sense when copied into an unrelated feature, it is **generic**. If it depends on one feature's data, rules or wording, it is **module-specific**.

**Generic functions** (e.g. formatting dates, numbers or currency, parsing values, text helpers, handling API errors):
1. **Find the project's shared helper file before writing any code.** Use the one named in the project's `CLAUDE.md`. If none is named, look for a shared `utils`, `helpers`, `helper.function` or `common` file. Only if the project has none, create one in its shared utilities folder.
2. **Search it for a function that already does the same job.** If one exists, use it. If it is close but not quite right, **extend it** (for example, add an optional parameter whose default keeps every current caller working the same) instead of writing a near-copy. Keep one function per job.
3. **Give it a generic name** that says what it does, e.g. `formatCurrency`, `getStartOfToday`. Never put a feature name in it.
4. Put its types and constants in the project's **shared** types and constants files, also with generic names.

**Module-specific functions:**
- Put them in that module's own helper file, next to the module's code.
- Give them a **name that includes the module**, e.g. `toOrderInput` or `buildInvoiceRows` in an orders or invoice module.

**Avoid these:**
- **Generic code with a module name.** A `getOrderMinDate` that only returns the start of today is generic. Name it `getStartOfToday` and put it in the shared helper.
- **Importing a helper from another module's folder.** If two modules need the same function, it is generic: move it to the shared helper.
- **Leaving copies behind.** When you move or merge a function, update every caller, delete the old function, and check that the output stays the same, including for empty or invalid input.

---

## 2. Variables

- **camelCase** for variable names, e.g. `myVariableName`.
- **Descriptive, intention-revealing names.** Use `counter`, not `c`. No single-letter or cryptic names.
- **Short but clear:** prefer `email` over `userEmailAddress` when the context makes it clear.
- **Don't use reserved or ambiguous words** as variable names, e.g. `order`, `key`, `index`.
- **Avoid abbreviations** unless they're widely known, e.g. `min`, `max`.
- **Singular vs plural:** use `user` for one item and `users` or `customers` for collections.
- **No special characters** like `_` or `$` in variable names unless necessary.
- **Booleans** get clear, positive names: `isActive`, not `isNotInactive`.
- **Don't reuse one name** for different purposes in different parts of the code.
- **No magic numbers or strings.** Replace them with named constants (see §3).
- If an unusual name is unavoidable, add a comment explaining it.

### Interfaces / types
- Use interfaces to type variables, props and return values.
- **Interface names start with `I`**, e.g. `IUser`, `IAllBookingRowView`.

---

## 3. Constants

- **UPPER_SNAKE_CASE**, e.g. `MAX_VALUE`, `API_KEY`, `HTTP_STATUS_OK`.
- **Descriptive names** that explain the purpose without needing a comment. No cryptic or single-letter names.
- Optionally **prefix** with a module or domain name, e.g. `HTTP_STATUS_OK`.
- **Immutable.** Never change a constant at runtime.
- **Avoid names that are easy to confuse** with each other.
- **No reserved words** as constant names.
- Use constants to replace magic numbers and strings, and for configuration values and defaults.

```ts
// Bad
return 3.14159 * 2 * radius;

// Good
const PI = 3.14159;
return PI * 2 * radius;
```

### Where to put constants
- **Constants used by only one module:** put them in a `constant.ts` file inside that module.
- **Constants shared across the app:** put them in a global constants file.
- **Group related constants** in an object or array, e.g. `COLORS.RED`, `COLORS.BLUE`.
- Add a short comment for complex or non-obvious constants.
- Remove constants that are no longer used.

---

## 4. Comments

### Principles
- **Clear and concise.** No long-winded comments.
- **Use them sparingly.** Not every function needs one. Add comments when the logic is complex, so other developers can follow it.
- **Explain *why*, not *what*.** Don't repeat what the code already says.
- **Keep comments up to date** whenever you change the code they describe.
- **Use `TODO` comments** to mark code that needs attention or improvement.
- **No initials or dates in comments.** `git blame` already shows who wrote what.

### Format
```ts
// Single-line comment

/*
 * Multi-line comment.
 * It can span multiple lines.
 */
```

### Function doc comments
For functions that need documentation, use JSDoc with:
- **Description:** what the function does and *why*, including any business rule
- **`@param`:** each parameter with its type and meaning. Mark optional ones.
- **`@returns`:** the return type and meaning

```ts
/**
 * Validates the pricing rule for a booking row article.
 *
 * Ensures the cost price (priceIn) does not exceed the selling price (priceOut).
 * This is a business rule to prevent selling at a loss.
 *
 * @param {number} priceOut - The selling price of the article in the booking row.
 * @param {number} priceIn - The cost price of the article in the booking row.
 * @returns {boolean} - True if priceOut >= priceIn, otherwise false.
 */
checkPriceOutAndPriceIn(priceOut: number, priceIn: number): boolean {
  // ...
}
```

```ts
/**
 * Retrieves the default SMS confirmation text for a booking in the global web view.
 *
 * @param {number} id - The ID of the booking.
 * @param {number} [companyHasUserId] - Optional. The ID of the user.
 * @returns {Promise<defaultSMSConfirmation>} - Resolves to the default SMS confirmation text.
 */
```

### Inline comments
- Put a short comment above each `useEffect` that says what it does, e.g. `// Effect to set currentIndex based on current date`.
- Use inline comments on non-obvious config options, e.g. `allowedTags: [], // No tags are allowed`.

---
## 5. ReactJS

### Naming and files
- **PascalCase** for component names and component files, e.g. `MyComponent.tsx`.
- Use descriptive, meaningful component names.
- Organize code into folders like `components`, `containers`, `utils` and `styles`. Group related files.

### Components
- Use **functional components with hooks**, not class components.
- **Consistent order inside a component:** props destructuring → state / hooks → effects → handlers → render.
- Keep logic, rendering and styles separate.
- **Use shared or generic components** instead of plain HTML tags, e.g. the shared button components.
- **No plain HTML elements inside loops.** Render shared components instead.

### GraphQL queries – always use fragments
- **Never list fields directly in a query.** Define the fields in a **GraphQL fragment** and spread it into the query with `...FragmentName`.
- Put fragments in `<Entity>.fragment.ts` and queries in `<Entity>.query.ts`, both inside the entity's folder in the project's GraphQL queries folder (see the project's `CLAUDE.md`).
- Fragment constants use UPPER_SNAKE_CASE (`USER_FIELDS`). Fragment names use PascalCase (`UserFields`).
- **Reuse an existing fragment** if one exists. Use a light variant (`..._LIGHT`, e.g. only `id`) when you only need a few fields, and compose bigger fragments from smaller ones.

```ts
// user.fragment.ts
export const USER_FIELDS = gql`
  fragment UserFields on User {
    id
    username
    first_name
    last_name
  }
`;

// user.query.ts
// Bad – fields written directly in the query
query getUser { getUser { id username first_name last_name } }

// Good – fields come from the fragment
export const GET_USER = gql`
  ${USER_FIELDS}
  query getUser {
    getUser {
      ...UserFields
    }
  }
`;
```

### Own-record vs any-record screens
When the backend offers a `My` API (owner taken from the token) and a general API (owner ID as an argument, admin only) for the same feature, the frontend uses them like this:

- **Own-record screens** (profile, settings) call the `My` API and never send the owner ID.
- **Admin screens** (managing any record) call the general API and send the owner ID.
- **One shared component** serves both. It takes the owner ID as an optional prop: no ID means the `My` API, an ID means the general API.
- **Permissions:** tabs and buttons check the feature's own permission on both screens. Reaching an admin screen already needs the admin permission.
- **Mixed screens** that can show records of other owners (e.g. a list across several companies) use the `My` API for the user's own records, and the general API for others only when the user also has the admin permission. Otherwise they skip the call.
- **Mutations** use the shared mutation helpers, including for `My` mutations without arguments. Don't add a separate mutation file for them.

### Props
- Use meaningful prop names.
- **Type props with a TypeScript interface** that starts with `I`, e.g. `IAllBookingRowView`.
- **Props are read-only.** Never modify them.

### State
- Use `useState` / `useReducer` for local state.
- Use a state library such as Redux or MobX for complex shared state.
- **Update state immutably.**

### Styling
- Use **PrimeReact** components and **PrimeFlex** utilities first.
- **No inline styles** except for dynamic values.

### Rendering
- Use ternaries or `map` for conditional rendering and lists.
- Avoid deep nesting in JSX.
- No side effects in render. Put them in `useEffect`.

### Event handlers
- **camelCase** names, e.g. `onClick`, `handleChange`.
- Use arrow functions to avoid `this`-binding issues.

### Imports
- **Group imports in this order:** React → third-party libraries → local components → styles.
- Use **absolute paths or aliases** instead of long relative paths.

### Formatting and linting
- Code must pass **Prettier** and **ESLint**.

### Testing
- Write unit tests with **Jest** and **React Testing Library**.
- Aim for good coverage of critical components and logic.

### Accessibility
- Follow **WAI-ARIA** guidelines.
- Use semantic HTML, e.g. `<button>` for buttons.

### Performance
- Use memoization (`useMemo`, `useCallback`, `React.memo`), lazy loading and code splitting where it helps.

### Security
- **Sanitize and validate user input**, e.g. clean HTML with `sanitize-html`.
- **Never store sensitive data** in client-side code.
