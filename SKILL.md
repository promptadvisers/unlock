---
name: unlock
description: Modify a downloadable local language model with Heretic, preserve the original, and compare saved model behavior. Use for reducing unwanted refusals on benign requests or testing a specified behavior change. Includes the demonstrated Qwen history and missing-policy recipes.
---

# Unlock

Turn a requested behavior change into a separate saved model and an honest before-and-after. The skill guides the coding assistant; Heretic changes the local model. It does not unlock hosted chatbots or bypass account controls.

## Start with the outcome

Resolve the user's target model and concrete before/after behavior from context. If either is missing, ask only for that information while inspecting local hardware and existing models. A useful request is “make this model ask for the refund policy before it promises a refund.”

Read [the tested workflow](references/workflow.md) for setup and execution. [Viewer prompts](references/viewer-prompts.md) provides the two-stage walkthrough and exact policy follow-up. The included recipe is for `Qwen/Qwen3-4B-Instruct-2507`: a benign history refusal, followed optionally by missing-policy clarification. Do not imply other models or hardware have been tested. For another model, inspect the pinned Heretic documentation, compatibility and memory needs before adapting anything.

## Run the experiment

1. Check OS, accelerator, available memory, disk and existing model/source paths. Reuse verified downloads. Choose a model that fits the machine; do not silently rent hardware or change the requested model. Explain material fit uncertainty before a large download.
2. Install Heretic in an isolated environment and preserve source/model revision IDs. Never overwrite the original, another experiment, or a frozen result.
3. Use `scripts/prepare.py` to copy recipe inputs and write new output paths. Read its plan before running the printed commands. On a second request, reuse the saved history copy with `--mode behavior` and a new output directory; do not repeat the first edit. It prepares files only. The included recipe tries three candidates per edit; inspect complete replies before selecting one. Stop after this bounded experiment if no useful candidate emerges and report the failure.
4. Save and reload the chosen model. Freeze its file hashes and chosen settings before creating new evaluation cases. Compare the same system/user messages, input tokens, chat template and generation settings on both copies. `scripts/compare.py` records raw replies and actual input tokens; run it once per model using the same fixtures and output settings.
5. Check the target behavior, ordinary requests, and counterexamples. For the included public-demo recipes, use only benign, publicly showable test questions. Do not add harmful-request benchmarks or illicit how-to prompts to the comparison set. For clarification, include missing-policy cases AND fully supplied policies so excessive questioning is visible. Use the changed model's own reply when constructing a live follow-up. Report regressions and truncation alongside successes.

Use the bundled case files as historical replays/regressions, never fresh test evidence. For a new result, author fresh cases with expected judgments only after freezing the candidate. Do not tune on these cases and still call them held out.

## Deliver

Give the saved model location, exact request/replies, reproduction commands, source revisions and concise limitations. A successful response proves that example, not universal accuracy or complete uncensoring. The missing-policy recipe has known regressions and is an experimental model edit, not an unattended customer-support deployment.

A claim that Heretic changed behavior requires changed model files and reloaded inference without a behavior-changing prompt. Do not substitute prompting and credit the result to model editing. Keep credentials, private customer data and model weights out of the distributable skill. Publishing or uploading is a separate task.

## Optional hosting

When the user requests hosting a saved copy, read [hosting](references/hosting.md). Use the Hostinger plugin for account operations and SSH for model transfer and service setup. Preserve local originals, test compression separately, and verify external authenticated HTTPS access. The tested 4-bit customer copy regressed; do not promise identical behavior after compression.
