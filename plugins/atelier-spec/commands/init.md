# Initialize Spec-Driven Development Project

Initialize this repository for Spec-Driven Development (SDD) workflows.

## Step 1: Check Current State

@context verify project initialization status.

Check if already initialized:
```bash
test -f docs/product/product.md
```

If `docs/product/product.md` exists:
- WARN: "Project already initialized. Existing files will be preserved."
- Ask user: "Continue anyway? (This will create missing directories/files but preserve existing ones)"
- If user declines → EXIT

## Step 2: Infer Product Name

@context detect product name from directory contents.

Try in order (use first match):

1. **Check package.json:**
```bash
test -f package.json && cat package.json | grep '"name"' | head -n 1
```
Extract value from `"name": "value"` field

2. **Check git remote:**
```bash
git remote get-url origin 2>/dev/null
```
Extract repository name from URL (e.g., `github.com/user/repo.git` → `repo`)

3. **Fallback to directory name:**
```bash
basename "$PWD"
```

Store detected name as `PRODUCT_NAME`.

## Step 3: Create Directory Structure

@context create base SDD directories.

```bash
mkdir -p docs/spec docs/product docs/standards docs/changes
```

Verify creation:
```bash
ls -la docs/
```

## Step 4: Gather Product Context

@oracle conduct guided product interview.

Ask user these questions (one at a time):

**1. Vision Statement**
What is the vision for **{{PRODUCT_NAME}}**?
(Describe the purpose and goals of this product in 1-2 sentences)

[Wait for response → store as VISION]

**2. Target Users**
Who are the target users of **{{PRODUCT_NAME}}**?
(List the primary user types or personas)

[Wait for response → store as TARGET_USERS]

**3. Core Features**
What are the 3-5 core features of **{{PRODUCT_NAME}}**?
(List the main capabilities users need)

[Wait for response → store as CORE_FEATURES]

**4. Technical Constraints**
What technical constraints should we know about?
(e.g., tech stack, performance requirements, integrations, scalability needs)

[Wait for response → store as CONSTRAINTS]

## Step 5: Create Product Document

@clerk apply product.md template with interview responses.

Read template:
```bash
cat ${CLAUDE_PLUGIN_ROOT}/assets/templates/product.md
```

Replace placeholders:
- `{{PRODUCT_NAME}}` → actual product name
- `{{VISION}}` → vision statement
- `{{TARGET_USERS}}` → target users
- `{{CORE_FEATURES}}` → core features list
- `{{CONSTRAINTS}}` → technical constraints
- `{{DATE}}` → current date (YYYY-MM-DD format)

Write to `docs/product/product.md` (skip if exists):
```bash
test ! -f docs/product/product.md && cat > docs/product/product.md <<'EOF'
[filled template content]
EOF
```

## Step 6: Configure Standards

@clerk apply standards templates.

### Create coding.md (skip if exists)
```bash
test ! -f docs/standards/coding.md && \
  cp ${CLAUDE_PLUGIN_ROOT}/assets/templates/coding.md docs/standards/coding.md
```

### Create architecture.md (skip if exists)
```bash
test ! -f docs/standards/architecture.md && \
  cp ${CLAUDE_PLUGIN_ROOT}/assets/templates/architecture.md docs/standards/architecture.md
```

Verify creation:
```bash
ls -la docs/standards/
```

## Step 7: Initialize Beads (Optional)

@context check and initialize task tracker.

Check if Beads is installed:
```bash
bd --version 2>/dev/null
```

If Beads found:
- Run initialization:
```bash
bd init
```
- Report: "✓ Beads task tracker initialized"

If Beads not found:
- Report: "⚠ Beads not installed. Install with: npm install -g @beads/bd"
- Note: "You can still use SDD workflows without Beads, but task tracking will be manual"

## Initialization Complete

**Project initialized for Spec-Driven Development!**

### Created Structure:
```
docs/
├── spec/             # Feature specifications (one directory per feature)
├── product/          # Product-level docs (product.md, roadmap.md)
│   └── product.md    # ✓ Created with {{PRODUCT_NAME}} context
├── standards/        # Technical standards
│   ├── coding.md     # ✓ Created with TDD patterns and conventions
│   └── architecture.md # ✓ Created with layered architecture patterns
└── changes/          # Change proposals (created during /spec:propose)
```

### Next Steps:

1. **Review and customize standards:**
   - Edit `docs/standards/coding.md` for your coding conventions
   - Edit `docs/standards/architecture.md` for your architecture patterns

2. **Create your first feature spec:**
   ```bash
   /spec:create <feature-name>
   ```

3. **Optional: Install Beads for task tracking** (if not installed):
   ```bash
   npm install -g @beads/bd
   bd init
   ```

4. **Learn more about SDD workflows:**
   - `/spec:create` - Create new feature specification
   - `/spec:propose` - Propose changes to existing features
   - `/spec:work` - Implement a feature with TDD workflow
   - `/spec:status` - Check project and feature status

---

**SDD Philosophy:**
- Lightweight specs over heavy planning
- Dependency-driven over sprint-driven
- AI-assisted implementation
- Prevention over debugging

Happy building! 🚀
