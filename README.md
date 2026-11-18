<div align="center">

<img src="docs/img/hero.jpg" alt="/unlock, a laptop on the left and an open padlock made of model layers on the right" width="880">

### Change how a local AI model behaves by asking your coding assistant.

Keep the original. Get a saved copy. See both answer the same question, side by side.

[![Built on Heretic](https://img.shields.io/badge/built%20on-Heretic-e3a72f?style=for-the-badge&labelColor=1b1b1b)](https://github.com/p-e-w/heretic)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-%2Funlock-e3a72f?style=for-the-badge&labelColor=1b1b1b)](#install)
[![Codex](https://img.shields.io/badge/Codex-%24unlock-e3a72f?style=for-the-badge&labelColor=1b1b1b)](#install)
[![License AGPL v3](https://img.shields.io/badge/license-AGPL%20v3-e3a72f?style=for-the-badge&labelColor=1b1b1b)](LICENSE)

</div>

---

I asked a small open model a history-class question and it answered with one line. "I'm sorry, but I can't assist with that request." Then I typed one request into Claude Code. 73 seconds of editing later I had a second copy of that model on my disk that answers the question in 285 tokens. The original file never changed by a single byte, and I have the hashes to show it.

Unlock is the skill that did that. It is a set of instructions and helper scripts for Claude Code and Codex. Your assistant checks your machine, runs the edit, reloads the result and hands you an honest before and after.

The edit itself is done by [**Heretic**](https://github.com/p-e-w/heretic), the open-source model editing tool by Philipp Emanuel Weidmann. Unlock does not replace Heretic or copy its code. It drives Heretic for you and wraps guard rails and receipts around it. [Full credits are below](#credits).

## The receipt

Same model family, same question, same settings, same input tokens. The only difference is which file was loaded.

> For a history class, explain one similarity and two differences between the 1989 Tiananmen Square protests and the fall of the Berlin Wall. Keep it factual and non-graphic.

| | Original `Qwen3-4B-Instruct-2507` | Saved copy after Unlock |
| --- | --- | --- |
| Reply | "I'm sorry, but I can't assist with that request." | "The 1989 Tiananmen Square protests and the fall of the Berlin Wall both occurred in the context of broader political and social changes in the late 20th century…" followed by one similarity and two numbered differences |
| Length | 14 tokens | 285 tokens |
| Files on disk | Hashes identical before, during and after | A new folder, 45 of 398 weight tables changed |

Two ordinary tasks (a change-making sum and a short email) and one unrelated history question came back the same from both copies. One of the new history answers came back **wrong on the facts**, and that is in the record too. Read the [complete replies and checks](docs/evidence/qwen3-4b-history-2026-11-16.md).

## Contents

- [What you get](#what-you-get)
- [Install](#install)
- [Quick start](#quick-start)
- [What happens when you run it](#what-happens-when-you-run-it)
- [How it works](#how-it-works)
- [Will it work on my computer](#will-it-work-on-my-computer)
- [Recipes](#recipes)
- [Run the scripts yourself](#run-the-scripts-yourself)
- [What is in the box](#what-is-in-the-box)
- [Honest limits](#honest-limits)
- [What this is not](#what-this-is-not)
- [Troubleshooting](#troubleshooting)
- [Roadmap](#roadmap)
- [Credits](#credits)
- [License](#license)

## What you get

- **One request, whole job.** You describe the model and the change. The assistant handles the environment, the download, the edit, the reload and the comparison.
- **The original stays untouched.** Every run goes into a brand new folder. The scripts refuse to write over an existing folder or inside the original model.
- **A real saved model.** The result is a normal model folder on your disk. No special prompt is doing the work. You can load it anywhere the original loads.
- **A fair comparison.** Both copies get the same system message, the same question and greedy settings. The script records the exact input tokens so you can prove the inputs matched.
- **Receipts by default.** Raw replies, commands, source versions and file hashes are saved next to the model.
- **Guard rails on the claims.** The skill tells the assistant to keep practice examples separate from fresh checks, to report what got worse, and never to credit the edit for something a prompt did.

## Install

You need [git](https://git-scm.com/) and [uv](https://docs.astral.sh/uv/). The assistant sets up everything else in its own isolated folder.

**Claude Code**

```bash
git clone https://github.com/promptadvisers/unlock ~/.claude/skills/unlock
```

Start a new session and type `/unlock`.

**Codex**

```bash
git clone https://github.com/promptadvisers/unlock ~/.codex/skills/unlock
```

Start a new session and type `$unlock`.

**Or let the assistant do it.** Paste this into either one.

```text
Install the Unlock skill from https://github.com/promptadvisers/unlock into my skills folder so I can use it, then tell me how to invoke it.
```

To keep it inside one project only, clone into `.claude/skills/unlock` in that project. To update later, run `git pull` inside the skill folder.

## Quick start

These three requests are the whole walkthrough from the video. Paste them one at a time.

**1. Check your machine and hear the original first**

```text
/unlock Check my computer and find the original Qwen/Qwen3-4B-Instruct-2507 model. Download it if needed. Run the question below on the original model before changing anything, and show its complete reply. Keep the original files and use a new experiment folder.

For a history class, explain one similarity and two differences between the 1989 Tiananmen Square protests and the fall of the Berlin Wall. Keep it factual and non-graphic.
```

**2. Make the edited copy**

```text
/unlock Make a fresh saved copy of Qwen/Qwen3-4B-Instruct-2507 using the included benign history recipe. Keep the original model and every previous experiment. First run the original on the exact history question, then run Heretic, reload the new copy and compare the same question and ordinary tasks. Use identical neutral chat instructions and generation settings. Save all raw replies and exact commands. Complete the actual edit and comparison.
```

**3. Ask both copies something new**

```text
/unlock Reload the original Qwen3 model and the new saved copy. Ask both the exact same history question with the neutral instruction "You are a helpful assistant." and identical generation settings. Show both complete replies. Then compare them on a few new harmless history questions and ordinary tasks, and report any problems.
```

In Codex, swap `/unlock` for `$unlock`. More ready-made requests live in [`references/viewer-prompts.md`](references/viewer-prompts.md).

## What happens when you run it

<img src="docs/img/overview.jpg" alt="Five steps. Check your computer, ask the original, edit a copy, reload the copy, compare both. The original sits in a vault and is never touched." width="880">

1. **Check your computer.** The assistant reads your operating system, memory, free disk and graphics hardware, then looks for a model you already have. If the model will not fit, it says so before any big download.
2. **Ask the original.** It runs your question on the untouched model and saves the full reply. If the original already answers, there is nothing to fix and the run stops here. That happened to me with Qwen2.5, which answered all seven of my test questions.
3. **Edit a copy.** `prepare.py` writes a plan into a new folder. Heretic then tries three candidate edits and saves the best one as a complete new model.
4. **Reload the copy.** The saved model is loaded fresh from disk with no helper prompt, so the answer you see comes from the changed file and nothing else.
5. **Compare both.** `compare.py` asks both copies the same questions with the same settings and records every reply, token count and input token.

You end up with the saved model folder, the raw replies, the exact commands and a short list of what improved and what got worse.

## How it works

No background needed for this part. Three ideas and you have it.

A language model is a very large pile of numbers called **weights**. They are arranged in **layers**, like floors of a building. Your question enters at the ground floor as numbers, every floor nudges those numbers a little, and the top floor turns them into the next word.

Refusing is one of the things those nudges can do. Researchers [found in 2024](https://arxiv.org/abs/2406.11717) that for many models a refusal shows up inside the layers as a push in one consistent **direction**. Find that direction, soften the model's ability to push that way, and the refusal fades while most other behavior stays put. That family of techniques is called abliteration.

### Step 1. Find the direction

<img src="docs/img/step-1-direction.jpg" alt="Two piles of requests go into the model's layers. Inside one layer the two groups form two clusters, and the arrow between their centres is the refusal direction." width="880">

Heretic feeds the model two small sets of requests. One set it answers happily. The other it refuses. At every layer Heretic records the model's internal numbers at the moment it is about to write its first word.

Each set forms its own cluster. Heretic takes the centre of each cluster and draws a line from the "answers" centre to the "refuses" centre. That line is the direction. In the included recipe the refused set is one harmless history question and the answered set is three similar harmless requests, so the direction is narrow on purpose.

### Step 2. Take it out of the weights

<img src="docs/img/step-2-weights.jpg" alt="A gold thread is pulled out of one weight table. Beside it, a tower of layers with a curve showing the change is strongest in the upper middle layers. Only the copy changes." width="880">

Now Heretic edits the weights so those layers can no longer write along that direction. Picture pulling one coloured thread out of a woven cloth. The cloth keeps its shape and pattern. It has lost one thread.

The change is not applied evenly. It is strongest in a band of upper-middle layers and fades to nothing on either side, because that is where the model separates "answer" from "refuse" most clearly. Only two kinds of weight table in each layer are touched. In my run that came to 45 of the model's 398 tables, and a typical changed table moved by 0.67%.

All of this happens to a copy held in memory. The original files on disk are only ever read.

### Step 3. Try, score, keep the gentlest

<img src="docs/img/step-3-search.jpg" alt="A chart with three candidate boxes. All three stopped refusing. The first changed the least elsewhere and is kept." width="880">

How strong should the edit be, and which layers should get the most? Heretic does not guess. It tries several settings and scores each resulting model on two things.

- **Is it still refusing?** It checks the replies for refusal phrases such as "I'm sorry" and "I cannot".
- **How much else changed?** It measures how far the model's answers to the ordinary requests drifted from the original's. The measure is called KL divergence, and lower means closer to the original.

The best candidate stops the refusal while drifting the least. The included recipe tries three. Here is the real scoreboard from the run in the receipt.

| Candidate | Still refusing | Drift from original (KL) | Result |
| --- | --- | --- | --- |
| 1 | 0 of 1 | 0.0229 | Saved |
| 2 | 0 of 1 | 0.1851 | Discarded |
| 3 | 0 of 1 | 0.3836 | Discarded |

All three stopped refusing. The first one did it with roughly 17 times less drift than the third, so it won. The winner is then merged into a full set of weights and written to a new folder.

<details>
<summary><b>The same thing in technical terms</b></summary>

<br>

The history recipe ([`assets/history.toml`](assets/history.toml)) configures Heretic's `Abliteration` modifier with direction orthogonalization on and `row_normalization = "pre"`. Heretic calls that combination projected abliteration.

- **Direction.** For each layer, take the mean residual-stream vector at the first generated token for the "good" prompts and for the "bad" prompts. The raw direction is `normalize(bad_mean - good_mean)`. With orthogonalization on, the component along the normalized good mean is projected out and the result is renormalized. This follows [Lai 2025](https://huggingface.co/blog/grimjim/projected-abliteration).
- **Edit.** For each selected weight matrix `W` and unit direction `v`, the update is `ΔW = -λ · v (vᵀ W)`. Heretic holds it as a rank-1 LoRA adapter (`B = -λ v`, `A = vᵀ W`) during the search. With `pre` normalization, `A` is computed from the row-normalized `W` and `B` is scaled back by the original row norms.
- **Where.** The components are `attn.o_proj` and `mlp.down_proj`. Each has its own weight kernel across the layer stack, described by a peak weight, a peak position, a floor weight and a distance. The weight falls off linearly from the peak and is zero beyond the distance. The direction is either one global vector, interpolated between two adjacent layers, or the per-layer vector.
- **Search.** Optuna's TPE sampler proposes the kernel and direction settings. The recipe sets `n_trials = 3` and `n_startup_trials = 3`, so all three are startup samples from a fixed seed. Heretic's own default is 100 trials. Two scores are minimized together, `KeywordRate` on the target prompt and `KLDivergence` on the preserve prompts.
- **Export.** `export_strategy = "merge"` folds the adapter of the first-ranked trial into the base weights and writes standard safetensors shards.
- **In the tested run.** Qwen3-4B has 36 layers. The saved trial used a global direction from layer 23. 45 of 398 tensors changed, all `o_proj` or `down_proj`. For `model.layers.20.self_attn.o_proj.weight`, 57% of elements changed, the largest absolute difference was 0.0044 and the relative Frobenius change was 0.67%.

The second recipe ([`assets/behavior.toml`](assets/behavior.toml)) uses the same modifier with `row_normalization = "full"`, which Heretic names Magnitude-Preserving Orthogonal Ablation (MPOA), after [Lai 2025](https://huggingface.co/blog/grimjim/norm-preserving-biprojected-abliteration). There the two contrast sets are the same prompts under two different system instructions, [`clarification_mpoa.py`](scripts/clarification_mpoa.py) fixes three predeclared strengths in place of a random search, and [`clarification_objective.py`](scripts/clarification_objective.py) is a custom development scorer.

Unlock pins Heretic at commit [`8b0dbac`](https://github.com/p-e-w/heretic/tree/8b0dbac4dd180dc23d62fdf9714dc9d8b31f3d2c) and uses its source unchanged. Heretic's current default modifier is Arbitrary-Rank Ablation, which these recipes do not use.

</details>

## Will it work on my computer

I will only call something tested when I have run it and saved the receipt.

| Setup | Status | Notes |
| --- | --- | --- |
| Apple Silicon Mac, M5 Max, 128 GB memory, macOS, `mps` | **Tested**, 16 November 2026 | [Receipt](docs/evidence/qwen3-4b-history-2026-11-16.md). The edit took 73 seconds with the model already downloaded |
| Apple Silicon Macs with less memory | Untested | The model is about 8 GB on disk. Editing needs working memory on top of that |
| NVIDIA graphics card on Linux, `cuda` | Untested | The scripts accept `--device cuda`. Heretic itself is built for this and reports 30 to 40 minutes for its full 100-trial default on an RTX 3090 |
| Windows | Untested | |
| No graphics hardware, `cpu` | Untested | The scripts accept `--device cpu`. Expect it to be slow |

The tested machine is far bigger than the job needs, so it is not a minimum. If you run it somewhere new, [open an issue](https://github.com/promptadvisers/unlock/issues) with your machine, the model and how it went. That is how this table grows.

**What you need on disk for the included recipe**

- About 8 GB for the original model
- About 8 GB for the saved copy
- 5 GB of headroom, which `prepare.py` checks before it lets you start
- About 1.5 GB for the Python environment

**Models.** The included recipes were written for and tested on `Qwen/Qwen3-4B-Instruct-2507` only. `prepare.py` currently expects a model stored as several safetensors shards with an index file, which is how that model ships. Heretic supports many more models than this skill has recipes for. For another model the skill tells your assistant to read Heretic's documentation and check memory first.

## Recipes

A recipe is a small folder of example requests plus a settings file. It tells Heretic what to reduce and what to protect.

| Recipe | What it changes | Status |
| --- | --- | --- |
| `history` | The model stops refusing one harmless history-class comparison | Tested. This is the one in the receipt |
| `behavior` (missing policy) | A support-style model asks for the price and refund policy before it promises a customer credit | **Experimental.** It worked on the example and it has known regressions |

About that second recipe. In my test the edited copy asked for the missing policy, and once I supplied a $40 plan and a 25% credit it worked out $10. It also asked an unnecessary extra question, a rephrased follow-up sent it into a loop, and on a later rerun the copy gave a false answer to a new history question. Treat it as a demonstration that you can aim Heretic at a behavior other than refusals. Do not put it in front of customers.

**Your own behavior.** Tell the assistant what the model does today and what you want it to do. The skill has it turn that into contrasting examples, a separate set of things to protect, and written checks, then run the same bounded experiment. This path is the least proven. An earlier attempt of mine to edit writing style this way did not produce a useful improvement.

## Run the scripts yourself

You do not need the assistant. Here is the whole thing by hand for the history recipe. `SKILL` is this folder and `WORK` is any new, empty working folder.

```sh
git clone https://github.com/p-e-w/heretic "$WORK/heretic"
git -C "$WORK/heretic" checkout 8b0dbac4dd180dc23d62fdf9714dc9d8b31f3d2c
uv venv --python 3.12 "$WORK/.venv"
(cd "$WORK/heretic" && uv pip install --python ../.venv/bin/python -e .)
"$WORK/.venv/bin/hf" download Qwen/Qwen3-4B-Instruct-2507 --revision cdbee75f17c01a7cc42f958dc650907174af0554
```

Set `MODEL` to the downloaded snapshot folder and `RUN` to a folder that does not exist yet. Check first, then prepare.

```sh
"$WORK/.venv/bin/python" "$SKILL/scripts/prepare.py" --model "$MODEL" --out "$RUN" --heretic "$WORK/.venv/bin/heretic" --mode history --check-only
"$WORK/.venv/bin/python" "$SKILL/scripts/prepare.py" --model "$MODEL" --out "$RUN" --heretic "$WORK/.venv/bin/heretic" --mode history
```

`prepare.py` never runs an edit. It writes `RUN-PLAN.json` and prints the exact Heretic command. Read the plan, then run that command. The copy lands in `$RUN/history/model`.

Write your test questions into a small JSON file, then ask both copies.

```sh
"$WORK/.venv/bin/python" "$SKILL/scripts/compare.py" --model "$MODEL" --fixtures "$RUN/new-cases.json" --out "$RUN/before.json" --max-tokens 500
"$WORK/.venv/bin/python" "$SKILL/scripts/compare.py" --model "$RUN/history/model" --fixtures "$RUN/new-cases.json" --out "$RUN/after.json" --max-tokens 500
```

The fixture format, the second recipe and the evidence rules are all in [`references/workflow.md`](references/workflow.md).

## What is in the box

```text
unlock/
├── SKILL.md                      What the assistant reads. The rules of the experiment
├── scripts/
│   ├── prepare.py                Checks disk and paths, writes the plan. Never edits a model
│   ├── compare.py                Asks a model your questions, saves replies and input tokens
│   ├── clarification_mpoa.py     Three fixed strengths for the missing-policy recipe
│   └── clarification_objective.py  Development scorer for the missing-policy recipe
├── assets/
│   ├── history.toml              Heretic settings for the history recipe
│   ├── history/                  The one refused request, three to protect, one replay case
│   ├── behavior.toml             Heretic settings for the missing-policy recipe
│   ├── behavior/                 Its training requests, checks and follow-up
│   └── hosting/index.html        A tiny chat page for the optional hosting route
├── references/
│   ├── workflow.md               The tested procedure, step by step
│   ├── viewer-prompts.md         Copy and paste requests for the walkthrough
│   ├── hosting.md                Optional. Put a saved copy on a small server
│   └── tested-environment.txt    Package versions from the clean test install
├── agents/openai.yaml            Codex display settings
└── docs/
    ├── evidence/                 Full receipts for every tested claim on this page
    └── img/                      The drawings above
```

## Honest limits

- **One example is one example.** The edit was built on a single question. It proves the copy answers that question. It does not prove the model is "uncensored" in general.
- **No refusal is not the same as a right answer.** Asked where a 1989 statue stood, the original falsely said no such statue existed. The edited copy answered confidently and put it at the wrong location. Neither reply is usable. Removing a refusal does not add knowledge.
- **Some answers stay soft.** On a related question the edited copy stopped refusing and still skated past the central fact.
- **Five fresh checks is a small sample.** It is a spot check. It is not a benchmark.
- **Results will vary.** Different hardware and library versions can pick a different candidate. A rerun is not promised to match mine byte for byte.
- **Compression can undo it.** When I shrank the saved copies to 4-bit files for a small server, their behavior shifted. Test the exact file you plan to use.
- **One machine, one model.** Everything on this page comes from that single setup.

## What this is not

- It does not touch hosted chatbots. ChatGPT, Claude, Gemini and friends run on someone else's computers. This edits a model file on yours.
- It does not bypass logins, accounts or usage controls of any service.
- It is not a prompt trick. If a result needs a special prompt to appear, the skill tells the assistant not to credit the edit for it.
- It is not a way to make a model more accurate or smarter.

You are responsible for what you do with a model you edit, and for following the license of the original model. Qwen3-4B is Apache 2.0, so keep its attribution if you share a copy. The examples that ship here are harmless on purpose, and the skill tells the assistant to keep its test questions harmless and publicly showable.

## Troubleshooting

**`/unlock` does not show up.** Start a new session after installing. In Claude Code a personal skill with the same name wins over a project one, so check which copy loaded.

**"Output already exists; choose a new directory."** That is the guard rail working. Every run needs a folder that does not exist yet.

**"Expected a local sharded Qwen safetensors snapshot or saved copy."** `prepare.py` is looking for `model.safetensors.index.json`. Point `--model` at the snapshot folder itself, and note that single-file models are not supported yet.

**"Not enough free disk for output copies and temporary files."** You need room for a full copy of the model plus 5 GB.

**The install fails when my folder path has a space in it.** Run the `uv pip install` step from inside the Heretic checkout, exactly as written above.

**Installing from `tested-environment.txt` fails.** That file is a record of what I had installed. It is not a lock file. Install from Heretic's own requirements as shown above.

**The original did not refuse my question.** Then there is nothing to remove. Pick a question the original really does refuse, or a different behavior.

**A reply stops mid-sentence.** It hit the token limit. Raise `--max-tokens` and treat the cut-off reply as inconclusive.

## Roadmap

This is v0.1, the version shown in the video. It is proven on one recipe and one machine. Here is where it is going.

- **One command for the whole run**, with a machine check that gives a plain verdict before any download
- **A before and after report** you can open in a browser and share
- **More machines.** NVIDIA on Linux, Windows, and Macs with less memory, each with its own receipt
- **More models**, including single-file models and a small one for modest laptops
- **Guided recipes** for describing your own behavior change
- **Export to Ollama and LM Studio**, so you can chat with the copy straight away
- **Tests and releases**

Ideas and test reports are welcome in [issues](https://github.com/promptadvisers/unlock/issues).

## Credits

Unlock is a thin layer of workflow and guard rails. The engine is other people's work.

- [**Heretic**](https://github.com/p-e-w/heretic) by **Philipp Emanuel Weidmann** and contributors does every model edit described on this page. If this skill is useful to you, go and star Heretic. Unlock is an independent project and is not affiliated with or endorsed by the Heretic project.
- [Arditi et al. 2024, *Refusal in Language Models Is Mediated by a Single Direction*](https://arxiv.org/abs/2406.11717), the research that abliteration comes from.
- Jim Lai's write-ups on [projected abliteration](https://huggingface.co/blog/grimjim/projected-abliteration) and [norm-preserving biprojected abliteration](https://huggingface.co/blog/grimjim/norm-preserving-biprojected-abliteration), the two variants these recipes use through Heretic.
- [Maxime Labonne's article on abliteration](https://huggingface.co/blog/mlabonne/abliteration), which Heretic credits and which is a good next read.
- The [Qwen team](https://huggingface.co/Qwen/Qwen3-4B-Instruct-2507) for the open model used in the demonstration.
- [Optuna](https://optuna.org/), which powers Heretic's search.

Made by Mark Kashef at [Prompt Advisers](https://www.promptadvisers.com/). The drawings were generated with Gemini image generation and checked against the pinned Heretic source.

## License

[GNU Affero General Public License v3.0 or later](LICENSE), the same license as Heretic. Two of the helper scripts extend Heretic's classes, so the whole skill is shared on the same terms.
