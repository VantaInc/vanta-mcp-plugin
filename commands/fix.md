---
description: Fix a failing Vanta compliance test by generating IaC changes and opening a pull request
argument-hint: test ID or Vanta test URL
---
Fix the failing Vanta test specified in $ARGUMENTS.

## Steps

1. **Parse the test ID.** If `$ARGUMENTS` is a URL (e.g., `https://app.vanta.com/c/<slug>/tests/<testId>`), extract the test ID from the path. If it's a plain string, use it directly as the test ID.

2. **Get remediation context.** Call `get_remediation_context` with the test ID. If the user's repository contains `.tf` files, pass `instructionType: "terraform"`. For CloudFormation templates, pass `"cloudformation"`. For CDK, pass `"cdk"`. If unsure, omit `instructionType` and let the server default to Terraform.

3. **Get failing entities.** Call `list_test_entities` for the test ID to understand which specific resources are failing and why.

4. **Follow the returned prompt.** The `get_remediation_context` response contains a system prompt and user message with test-specific remediation intelligence. Follow these instructions to scan the local repository and generate the fix.

5. **Scan the local repository.** Search for IaC files that manage the failing resources. Look for resource names, ARNs, or identifiers that match the failing entities. If no matching files exist, inform the user and offer to generate new resource definitions.

6. **Generate the minimal fix.** Make only the changes required to pass the test. Do not refactor surrounding code, add unrelated improvements, or change resources that are not failing.

7. **Show the diff and ask for approval.** Present the proposed changes clearly. Explain what each change does and why it resolves the test failure.

8. **Create a pull request.** If the user approves:
  - Create a new branch named `vanta/fix-<testId>`
  - Commit the changes
  - Open a pull request with `Fixes: test-<testId>` in the description so Vanta can link the PR and auto-trigger a re-run

9. **Handle non-IaC tests gracefully.** If `get_remediation_context` indicates the test is not IaC-remediable, do not attempt to generate code. Instead:
  - Present the guidance from the remediation context
  - Search the web for current documentation on the required action (console steps, third-party tool configuration, etc.)
  - Provide clear, actionable instructions the user can follow manually

## Edge cases

- **Test ID not found:** Call `tests` to fetch the failing tests list, fuzzy-match against the provided ID, and present the closest matches. "I couldn't find a test called `[id]`. Did you mean one of these?" Never dead-end.
- **Test is already passing:** "This test is currently passing. No remediation needed." Then show the failing tests list so the user can pick something else.
- **Malformed or non-test URL:** "I couldn't parse a test ID from that URL." Then show the failing tests list.
- **Ambiguous description (no ID):** If `$ARGUMENTS` doesn't match a test ID, call `tests` and filter by keyword. If one match, proceed. If multiple, show candidates with entity counts and ask which one. If none, show the full failing tests list.
- **No IaC files in directory:** "I have the fix for this test, but I don't see any IaC files in this directory." Offer options: open Claude Code in the right repo, generate new Terraform files, or provide CLI commands.
- **IaC files found but no matching resources:** "I found Terraform files, but none manage the failing resources." Offer: import + fix, fix in a different repo, or CLI commands.
- **User says "fix all [X] tests":** Check for co-failure clusters. If tests share a root cause, generate one unified fix. If independent, fix sequentially with one PR each.
