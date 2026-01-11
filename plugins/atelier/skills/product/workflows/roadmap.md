# Update Roadmap

## Step 1: Analyze Current State

@context gather current project state.

Retrieve:
- Current roadmap priorities from `docs/product/roadmap.md`
- Feature completion status from Beads (`bd list --json`)
- Available features from `docs/product/product.md`
- All specs from `docs/spec/*/spec.md`

Load and analyze current state.

## Step 2: Calculate Progress Metrics

@product analyze progress and generate recommendations.

Calculate:
- Feature completion percentages
- Development velocity metrics
- Timeline assessments
- Priority recommendations based on:
  - Business value
  - Technical dependencies
  - Risk assessment
  - Team capacity

Provide strategic recommendations.

## Step 3: Priority Interview

Based on current progress analysis, confirm your next priorities:

### Current Features Status

- [Feature status from product-agent analysis]

### Available Features

- [Features from product.md not yet started]

### Recommended Next 3 Priorities

1. **[Product-agent recommendation 1]** - [Reasoning]
2. **[Product-agent recommendation 2]** - [Reasoning]
3. **[Product-agent recommendation 3]** - [Reasoning]

**Confirm or adjust these priorities:** [Waiting for user input]

## Step 4: Update Roadmap File

@scaffold update `docs/product/roadmap.md` with confirmed priorities.

Include:
- Next 3 priorities with status
- Current progress summary
- Timeline projections
- Risk assessments
- Dependencies and blockers

## Roadmap Updated

### Summary

- Progress analysis completed
- New priorities confirmed
- Roadmap file updated

### Next Actions

1. Continue active feature: `/spec work [current-feature]`
2. Start next priority: `/spec create [next-feature]`
3. Check overall progress: `/product progress`
4. Update methodology: `/product update`
