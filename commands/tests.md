---
description: Show failing Vanta compliance tests, prioritized by what can be fixed from this repository
---

Show the user their failing Vanta tests, ranked by what the plugin can help with.

## Steps

1. **Fetch failing tests.** Call `tests` to get all tests with status `NEEDS_ATTENTION`.

2. **Detect the local IaC tool.** Scan the working directory for `.tf` files (Terraform), `template.yaml`/`template.json` (CloudFormation), `cdk.json` (CDK), or similar. Note the detected tool for context.

3. **Categorize and rank tests.** Group the failing tests into tiers:

   **Ready to fix** — Tests where:
   - The test has AI remediation instructions available
   - The test's integration matches resources likely managed in this repo (e.g., AWS tests in a Terraform repo with AWS provider)
   - Present these first. These are one-command fixes with `/vanta:fix <testId>`.

   **Fixable with guidance** — Tests that are IaC-remediable but may not match this repo (different cloud provider, different integration). The user can still get remediation code, but may need to apply it elsewhere.

   **Manual steps needed** — Tests that require configuration changes in external tools, Vanta settings, or manual processes. The plugin can provide guidance but not generate code.

4. **Present the results.** For each tier, show:
   - Test name and ID
   - Number of failing entities
   - Integration (e.g., AWS, GitHub, Azure)
   - How long the test has been failing (from `latestFlipDate`)
   - For "Ready to fix" tests, show: `Run /vanta:fix <testId> to generate a PR`

5. **Highlight co-failure clusters.** If multiple failing tests map to the same resource type or integration, note this. For example: "5 IAM tests are failing — fixing the password policy may resolve all of them at once."

6. **Keep it scannable.** Use a table or bulleted list. Do not dump raw API responses. The user needs to quickly see what to fix first.

## Edge cases

- **No failing tests:** "All tests are passing. Nice work." Do not show an empty table.
- **User asks to filter (e.g., "show AWS tests"):** Filter by integration name. If no failures match the filter, say so and show the full list: "No failing AWS tests found. Here's what is failing across other integrations:"
- **User asks to filter by framework (e.g., "SOC 2 gaps"):** Filter by framework. "You have [N] failing tests mapped to SOC 2. Here are the ones I can help fix from this repo."
- **User asks "what should I fix first?":** Rank by impact: IaC-fixable in this repo first, then highest entity count, then longest time failing. Highlight co-failure clusters as "biggest bang for the buck."
- **Very large number of failing tests:** Group by integration and summarize counts rather than listing every test. Show the top 5-10 highest-impact items with a note: "[N] more tests failing. Want to see the full list or focus on [integration]?"
