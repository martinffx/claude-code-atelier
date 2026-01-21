# Feature Status: $ARGUMENTS

## Step 1: Check Beads Installation

@context verify Beads is available.

```bash
bd --version
```
- If command fails → ERROR: "Beads not found. Install with: npm install -g @beads/bd, then run: bd init"

## Step 2: Query Feature Tasks

@product analyze progress using Beads.

**If feature specified:**
```bash
bd list --label $ARGUMENTS --json
```

**If no arguments (all features):**
```bash
bd list --json
```

Parse JSON output to get task data.

## Step 3: Calculate Metrics

@product compute progress metrics.

### Task Breakdown
- Total tasks
- Completed tasks (`status: done`)
- In progress tasks (`status: in_progress`)
- Blocked tasks (has blocking dependencies)
- Ready tasks (`bd ready --label <feature>`)

### Progress Calculation
```
completion = (completed_tasks / total_tasks) × 100
```

### Velocity (if multiple completed tasks)
```
velocity = completed_tasks / elapsed_time
remaining_estimate = remaining_tasks / velocity
```

### Critical Path Analysis
```bash
bd dep list --json
```
Identify task chains and potential bottlenecks.

## Step 4: Generate Report

Format output:

### Single Feature Report

```
Feature: <feature_name>
Epic: <epic-id>
Progress: <X>% complete (<completed>/<total> tasks)

Status:
✓ Completed: <completed_count>
🚧 In Progress: <in_progress_count>
⏳ Ready: <ready_count>
🔒 Blocked: <blocked_count>

Next Ready Tasks:
  1. <task-id>: <task-description>
  2. <task-id>: <task-description>

Estimated Remaining: ~<hours> hours (based on velocity)

Recommendation: /spec:work <feature>
```

### All Features Report

```
Project Status

Overall Progress: <X>% complete

Features:
  ✓ <feature1>: 100% (5/5 tasks)
  🚧 <feature2>: 60% (3/5 tasks)
  ⏳ <feature3>: 0% (0/4 tasks)

Active Work:
  - <task-id> (<feature>): <description>

Blockers:
  - <task-id> (<feature>): Blocked by <blocking-task-id>

Next Actions:
  1. /spec:work <feature2>  # Continue active feature
  2. /spec:create <feature3>  # Start new feature
```

## Step 5: Identify Blockers and Recommendations

@product provide strategic recommendations.

### Blocker Analysis
For each blocked task:
```bash
bd dep list <task-id>
```
Show dependency chain and suggest actions.

### Next Action Logic
- Feature has Beads tasks + ready tasks → `/spec:work <feature>`
- Feature has spec but no Beads tasks:
  - If no design → `/spec:design <feature>`
  - If design but no plan → `/spec:plan <feature>`
- Change has proposal but no Beads tasks:
  - If no design → `/spec:design <feature> <change>`
  - If design but no plan → `/spec:plan <feature> <change>`
- Feature complete → `/spec:create <next_feature>` or `/spec:propose <feature> <change>`
- Tasks blocked → Work on parallel feature or resolve dependencies

## Status Report Complete

Use this information to:
- Track progress across features
- Identify bottlenecks and blockers
- Plan next development activities
- Update stakeholders on status

**Next steps:**

1. Continue work: `/spec:work [feature]`
2. Review spec: `docs/spec/<feature>/spec.md`
