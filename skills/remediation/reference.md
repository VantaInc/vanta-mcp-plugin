# Remediation Reference

Detailed patterns for edge cases, IaC detection, and non-IaC handling. This file supplements SKILL.md with specifics Claude needs during remediation.

## IaC Detection Table
| Files found | IaC tool | `instructionType` value |
| --- | --- | --- |
| `*.tf`, `*.tfvars` | Terraform | `terraform` |
| `template.yaml`, `template.json` | CloudFormation | `cloudformation` |
| `cdk.json`, `cdk.out/` | AWS CDK | `cdk` |
| `Pulumi.yaml` | Pulumi | `pulumi` |
| None of the above | Unknown | Omit (server defaults to `terraform`) |
If no IaC files are found, inform the user and offer three options:
1. Open Claude Code in their IaC repo
2. Generate new Terraform files from scratch (user must place in correct repo and configure provider)
3. Provide CLI commands (with drift warning since no IaC manages the state)

## Non-IaC Test Handling

When `get_remediation_context` indicates a test is not IaC-remediable, do NOT generate code. There are three tracks:

### Option 1: IaC-remediable (code fix)
Standard workflow. Generate IaC changes and open a PR.

### Option 2: Third-party tool configuration
Requires action in an external service (AWS Console, GitHub settings, Okta admin, etc.).
- Web search is critical here. Existing Vanta remediation instructions may reference outdated UIs or deprecated workflows.
- Always supplement server guidance with a fresh web search for current documentation.
- Provide step-by-step instructions with links to the relevant admin console or docs.

### Option 3: In-Vanta action
Requires a configuration change inside Vanta itself.
- Direct the user to the specific Vanta UI page and action.
- Be as precise as possible: "Go to People > User Access Reviews" not "change a setting in Vanta." If available, give users the direct link.

### When the user pushes back ("can you just fix it?")
Explain what the test checks and why it requires manual action. Offer to draft shareable instructions for the person who manages the relevant system: "Want me to draft instructions you can share with the person who manages [system]?"

## Edge Case Patterns

### Test ID resolution
- **ID doesn't exist:** Fetch failing tests, fuzzy-match, present closest matches. "Did you mean one of these?" Never dead-end.
- **Already passing:** "No remediation needed." Show failing tests instead.
- **Ambiguous description (no ID):** Call `tests`, filter by keyword. One match = proceed. Multiple = show candidates with entity counts. None = show full list.

### URL parsing
- Extract test ID from URL path: `https://app.vanta.com/c/<slug>/tests/<testId>` -> use `<testId>`
- **Malformed URL:** "I couldn't parse a test ID from that URL." Show failing tests list.
- **URL points to non-test page:** "That URL points to a [page type], not a test." Show failing tests list.

### Repository mismatches
- **IaC files found but no matching resources:** "I found Terraform files but none manage the failing resources ([entity names])." Offer: import + fix, fix in correct repo, or CLI commands.
- **Wrong directory (\~/Desktop, \~/Documents):** "You're in [directory], which isn't an infrastructure repository. Open Claude Code in your IaC directory for code fixes. I can still show failing tests or provide guidance from here."

### Bulk operations
- **"Fix all [X] tests":** Fix sequentially, one PR each.
- **Multiple failing entities on one test:** Generate fix addressing all entities. If entities require different fixes, present grouped.

### Post-fix verification
- Explain typical re-evaluation timeline
- Provide Vanta URL for the test
- If still failing: check if new entities appeared (fix worked, new resources failing) vs same entities (sync delay, PR not merged, fix needs adjustment)

## Explaining Tests

When a user asks what a test checks (without asking to fix it):

1. Call `get_remediation_context(testId)` for description and intelligence
2. Call `list_test_entities(testId)` to see what's specifically failing
3. Present:
  - **What this test checks** (plain language)
  - **Why it's failing** (per-entity reasons)
  - **What the fix involves** (brief description)
  - **Cost impact** (if applicable, from remediation context)
4. Always end with: "Want me to fix it?"

## Prioritization Ranking

When showing tests or recommending what to fix:

1. IaC-fixable in this repo (Ready to fix)
2. Highest entity count (most resources at risk)
3. Longest time failing (from `latestFlipDate`)
