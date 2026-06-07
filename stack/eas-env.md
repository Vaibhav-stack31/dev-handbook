## Environment Variables — EAS

> All commands verified personally. Last tested: June 2026

---

### Variable Types
- **String** — plain text value e.g. `https://api.example.com`
- **File** — uploads a file as the variable e.g. `google-services.json`, `.p8 key`

### Visibility Levels
- **Plain text** — visible everywhere, logs, dashboard
- **Sensitive** — masked in logs, visible in dashboard
- **Secret** — never retrievable after creation, masked everywhere

### Environments
- **development** — `eas build --profile development`
- **preview** — `eas build --profile preview`
- **production** — `eas build --profile production`
- **Other** — custom profile name defined in `eas.json`

One variable can be assigned to multiple environments at once during creation.

---

### Commands

---

#### `env:list` — List all variables

```bash
# List all variables (short format, project scope)
eas env:list

# List for a specific environment
eas env:list development

# List with sensitive values revealed
eas env:list --include-sensitive

# List with file contents shown
eas env:list --include-file-content

# Long format (shows metadata: visibility, scope, environment)
eas env:list --format long

# List account-scoped variables
eas env:list --scope account
```

**When to use:** Quick audit of what variables exist per environment.
Use `--format long` when you need to see visibility levels and scope.

---

#### `env:create` — Create a variable

```bash
# Interactive (recommended — prompts for everything)
eas env:create

# Fully non-interactive
eas env:create \
  --name API_URL \
  --value https://api.example.com \
  --type string \
  --visibility plaintext \
  --environment development \
  --environment preview

# Create a file-type variable
eas env:create --name GOOGLE_SERVICES --type file --value ./google-services.json

# Overwrite if already exists
eas env:create --name API_URL --value https://new.example.com --force

# Account-scoped (shared across all projects)
eas env:create --scope account --name SHARED_SECRET --value abc123
```

**When to use:**
- Use interactive mode when creating for the first time — safer, less error-prone
- Use flags in CI/CD scripts where there's no terminal prompt
- Use `--scope account` for variables shared across multiple EAS projects
- Use `--force` to overwrite in scripts, never manually — you'll skip the confirmation

---

#### `env:get` — View a specific variable

```bash
# Interactive (prompts for environment and variable name)
eas env:get

# Pass environment as argument, prompts for variable name
eas env:get development

# Pass variable name, prompts for environment
eas env:get --variable-name=API_URL

# Fully explicit
eas env:get development --variable-name=API_URL

# Long format (shows metadata)
eas env:get development --variable-name=API_URL --format long

# Account-scoped variable
eas env:get --variable-name=SHARED_SECRET --scope account
```

**When to use:** Verify a specific variable's value before a build.
Use `--format long` to also see its visibility level and scope.

---

#### `env:update` — Update an existing variable

```bash
# Interactive
eas env:update

# Update value only
eas env:update development --variable-name=API_URL --value https://new.example.com

# Rename a variable
eas env:update development --variable-name=API_URL --name=BASE_URL

# Change visibility
eas env:update development --variable-name=API_URL --visibility sensitive

# Move to a different environment
eas env:update development --variable-name=API_URL --environment production

# Update account-scoped variable
eas env:update --scope account --variable-name=SHARED_SECRET --value newvalue
```

**When to use:** Rotating keys, updating URLs between environments, changing
visibility after creation. Note: you cannot downgrade visibility —
`secret` stays `secret`.

---

#### `env:delete` — Delete a variable

```bash
# Interactive (prompts for environment and variable name)
eas env:delete

# Pass environment, prompts for variable name
eas env:delete development

# Fully explicit
eas env:delete development --variable-name=API_URL

# Account-scoped variable
eas env:delete --scope account --variable-name=SHARED_SECRET

# Non-interactive (for scripts)
eas env:delete development --variable-name=API_URL --non-interactive
```

**When to use:** Cleaning up stale keys, removing deprecated variables.
Always use interactive mode manually — the prompt is your safety net.
Use `--non-interactive` only in automated cleanup scripts.

---

#### `env:pull` — Pull variables from EAS into local `.env` file

```bash
# Interactive (prompts for environment), writes to .env.local by default
eas env:pull

# Pull from specific environment
eas env:pull development

# Write to custom path
eas env:pull development --path .env.development

# Pull production into default .env.local
eas env:pull production
```

**When to use:** When you need to run the app locally with the same variables
as a specific EAS environment. Default output is `.env.local`.

> Gotcha: this overwrites the file at the target path without warning.
> Commit or backup your local `.env` before pulling.

---

#### `env:push` — Push local `.env` file to EAS

```bash
# Interactive (prompts for environment), reads from .env.local by default
eas env:push

# Push to specific environment
eas env:push development

# Push from custom path
eas env:push development --path .env.development

# Overwrite existing variables without confirmation
eas env:push development --force
```

**When to use:** Bulk-creating variables from an existing `.env` file.
Useful when migrating a project to EAS for the first time.

> Gotcha: `--force` skips confirmation and overwrites everything silently.
> Never use it in production without double-checking the file first.

---

#### `env:exec` — Run a command with EAS env variables injected

```bash
# Run a script with development variables injected
eas env:exec development "node scripts/seed.js"

# Run any bash command
eas env:exec production "printenv | grep API"
```

**When to use:** Running local scripts (seed, migrate, test) that need
production or preview variables without pulling them into a `.env` file.
Cleaner than `env:pull` when you need variables temporarily for one command.

---

### Accessing in Code

Prefix with `EXPO_PUBLIC_` to expose the variable inside the app bundle:
```ts
const url = process.env.EXPO_PUBLIC_API_URL;
```
Variables **without** `EXPO_PUBLIC_` are build-time only — never in the bundle.
Use for secrets like signing keys, internal tokens.

---

### Gotchas
- `secret` variables cannot be retrieved after creation — save the value externally before creating
- You cannot downgrade visibility — once `secret`, always `secret`
- A variable scoped to `production` won't be available in a `preview` build
- Changes require a new build — no hot reload
- `eas env:pull` default output is `.env.local`, not `.env` — check your `.gitignore`
- `eas env:push --force` overwrites silently — never use on production without verifying the file