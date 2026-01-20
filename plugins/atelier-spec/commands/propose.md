# Propose Change: $ARGUMENTS

Format: `<feature_name> <change_name>`

## Step 1: Validate Prerequisites

@context check for existing spec and Beads installation.

Check if spec exists:
- If `docs/spec/$FEATURE/spec.md` doesn't exist → ERROR: "Feature spec not found. Create with: /spec:create $FEATURE"

Check Beads installation:
```bash
bd --version
```
- If command fails → ERROR: "Beads not found. Install with: npm install -g @beads/bd, then run: bd init"

## Step 2: Load Current State

@context retrieve current feature specification.

Read files:
- `docs/spec/$FEATURE/spec.md` → current requirements and design
- `docs/standards/` → architectural patterns

Extract current state:
- Existing entities, services, repositories, routes
- Current business rules and constraints
- API endpoints and data models

## Step 3: Gather Change Requirements

@analyst conduct lightweight interview for change.

Show current feature implementation context.

**Change Questions:**

**What needs to change?**
[Wait for response]

**Why is this change needed?** (motivation)
[Wait for response]

**What components are affected?**
[Wait for response]

**Are there breaking changes?**
[Wait for response]

**What's the implementation approach?**
[Wait for response]

## Step 4: Generate Draft Artifacts

@architect analyze impact and generate draft proposal and delta.

Read:
- `docs/spec/$FEATURE/spec.md` → current design
- User responses → proposed changes

Determine impact:
- Which layers need changes (Entity, Repository, Service, Router)
- New methods, fields, or components
- Modified business rules
- Breaking changes

<parallel>
  <agent type="scaffold">
    Create draft proposal:
    - Create directory: `docs/changes/$FEATURE/$CHANGE/`
    - Generate `docs/changes/$FEATURE/$CHANGE/proposal.md` using template `${CLAUDE_PLUGIN_ROOT}/assets/templates/proposal.md`
      - Set STATUS to "Draft"
      - Include: motivation, impact analysis, implementation approach

    Return: proposal_path
  </agent>

  <agent type="scaffold">
    Create draft delta:
    - Generate `docs/changes/$FEATURE/$CHANGE/delta.md` using template `${CLAUDE_PLUGIN_ROOT}/assets/templates/delta.md`
      - ADDED: New components, methods, fields
      - MODIFIED: Changed components, methods, rules
      - REMOVED: Deprecated items

    Return: delta_path
  </agent>
</parallel>

Display generated draft artifacts to user with clear indication they are in DRAFT status.

## Step 5: Edit Phase

Present drafts for user review and modification.

**Generated draft artifacts:**
- `docs/changes/$FEATURE/$CHANGE/proposal.md` (Status: Draft)
- `docs/changes/$FEATURE/$CHANGE/delta.md`

**Review checklist:**
- [ ] Motivation accurately captures the "why"
- [ ] Impact analysis covers all affected components
- [ ] Delta correctly identifies ADDED/MODIFIED/REMOVED items
- [ ] Breaking changes are documented
- [ ] Implementation approach is feasible

**Options:**

1. **Edit proposal.md** - Modify motivation, approach, or risks
2. **Edit delta.md** - Adjust technical changes
3. **Continue to review** - Proceed to validation phase

[Wait for user to either edit the files or confirm to proceed to review]

## Step 6: Review Phase

@oracle validate the proposal before finalizing.

**Technical Validation:**

<agent type="oracle">
  Analyze the proposed change for technical soundness:

  Read:
  - `docs/changes/$FEATURE/$CHANGE/proposal.md`
  - `docs/changes/$FEATURE/$CHANGE/delta.md`
  - `docs/spec/$FEATURE/spec.md` (current spec)

  1. **Consistency Check:**
     - Does the delta align with the current spec?
     - Are all affected layers identified?
     - Are dependencies between changes correct?

  2. **Completeness Check:**
     - Are migration steps sufficient?
     - Is rollback plan viable?
     - Are breaking changes properly documented?
     - Are all ADDED items fully specified?
     - Do MODIFIED items clearly show before/after?

  3. **Risk Assessment:**
     - Identify potential issues with the approach
     - Flag any concerns about implementation
     - Check for unintended side effects

  Output:
  - validation_result: PASS or CONCERNS
  - issues_list: List of identified issues (if any)
  - recommendations: Suggested improvements
</agent>

**Display validation results to user.**

If validation raises CONCERNS:
- Display issues and recommendations
- Offer options:
  1. Return to Edit Phase (Step 5)
  2. Proceed anyway (user accepts risks)
  3. Cancel proposal

[Wait for user decision]

**User Approval:**

If validation PASSED or user chose to proceed:
- Present validation summary
- Request explicit approval to create implementation tasks

**Approval required to proceed:**
- [ ] I have reviewed the proposal and delta
- [ ] I understand the scope and impact
- [ ] I approve creating Beads tasks for implementation

[Wait for user approval]

## Step 7: Finalize Change Proposal

@architect finalize artifacts and create Beads epic after user approval.

Update proposal status:
- Edit `docs/changes/$FEATURE/$CHANGE/proposal.md`
- Change **Status:** from "Draft" to "Approved"

Create Beads epic for change:
- Analyze delta to identify affected layers
- Create epic: `bd create "Change: $FEATURE - $CHANGE" -t epic -p 1 -l $FEATURE,change`
- Create tasks for **only the affected layers** (Entity, Repository, Service, Router, etc.)
- Set dependencies based on change scope (bottom-up)
- Examples:
  - Service + router: service blocks router
  - Full-stack: entity → repository → service → router with blocking dependencies

Store epic_id, task_count, layers_affected for output.

## Change Proposal Complete

Created and approved:
- `docs/changes/$FEATURE/$CHANGE/proposal.md` (Status: Approved)
- `docs/changes/$FEATURE/$CHANGE/delta.md`

Beads Epic: {{epic_id}}

The change proposal includes:
- Impact analysis and motivation
- Technical delta (ADDED/MODIFIED/REMOVED)
- Contextual task breakdown ({{task_count}} tasks for {{layers_affected}} layers)
- Dependency-ordered implementation plan

**Next steps:**

1. Begin implementation: `/spec:work $FEATURE`
2. When complete: `/spec:complete $FEATURE $CHANGE`
