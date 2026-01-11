# Product Status & Roadmap Management

Arguments: `[update|verbose]` (optional)

## Step 1: Load Project State

@context gather comprehensive project data.

Retrieve:
- Current roadmap from `docs/product/roadmap.md`
- All feature specifications from `docs/spec/*/`
- Feature completion status from Beads (`bd list --json`)
- Available features from `docs/product/product.md`

Load all project state data for analysis.

## Step 2: Analyze Progress & Calculate Metrics

@product perform comprehensive progress analysis using the context data.

Calculate:
- Feature completion percentages across all specs
- Development velocity metrics (tasks/day, features/week)
- Progress against current roadmap priorities
- Code quality metrics and test coverage
- Timeline projections and risk assessment
- Priority recommendations based on business value and dependencies

Generate comprehensive progress report with strategic insights.

## Step 3: Display Current Status

Based on progress analysis, display current product status:

### Current Status Summary

- Overall roadmap progress: [X]% complete
- Features in progress: [Count and status]
- Completed this period: [List]
- Blockers identified: [Any issues]

### Velocity & Quality Metrics

- Development velocity: [Tasks per day with AI assistance]
- Code quality indicators: [Test coverage, passing tests]
- AI efficiency multiplier: [Actual vs estimated time]

### Current Roadmap Priorities

- Priority 1: [Feature] - [Status]
- Priority 2: [Feature] - [Status]
- Priority 3: [Feature] - [Status]

## Step 4: Priority Management (update mode only)

If argument is `update`, proceed with priority interview:

### Available Features

- [Features from product.md not yet started]

### Recommended Next 3 Priorities

1. **[Product agent recommendation 1]** - [Reasoning]
2. **[Product agent recommendation 2]** - [Reasoning]
3. **[Product agent recommendation 3]** - [Reasoning]

**Confirm or adjust these priorities:** [Interactive confirmation needed]

@scaffold if priorities confirmed, update `docs/product/roadmap.md` with:
- Confirmed next priorities
- Current status summary
- Updated timeline projections
- Risk assessments

## Step 5: Strategic Recommendations

Based on analysis, provide strategic recommendations:

### Recommended Actions

#### Immediate (Today)

- [Specific next command to run]
- [Any urgent items to address]

#### This Week

- [Strategic priorities for the week]
- [Process improvements to consider]

### Next Actions

1. Continue active feature: `/spec work [current-feature]`
2. Start next priority: `/spec create [next-feature]`
3. Update methodology: `/product update`

## Status Analysis Complete

**Next Action**: [Specific command recommendation]
**Alternative**: [If blocked or complete, what to do next]

---

### Usage Examples

- `/product progress` - Show current progress analysis
- `/product progress update` - Update roadmap priorities
- `/product progress verbose` - Detailed analysis with full metrics
