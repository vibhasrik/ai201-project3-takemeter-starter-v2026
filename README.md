# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

TakeMeter sorts text posts from r/vegan, a large public subreddit, by what a post asks of the reader. Each post gets one of four labels: `call_to_action`, `asking_for_advice`, `open_discussion`, or `personal_story`. The goal is to help a regular decide quickly whether a post wants them to act, help, weigh in, or just read. Image, video, and link-only posts are excluded because the model only sees text.

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `call_to_action`

**Definition:** The post names a specific action the reader can take to support the cause, with a concrete target (a petition, fundraiser, volunteer role, or event).

**Example 1:**

> Help save Europe's largest hospital for sick and orphaned seals [...] To prevent that from happening, they need to raise 2.8 million euros. [...] The big orange button "Go to our GoFundMe" takes you to the official fundraiser that they've set up today.

**Example 2:**

> We need the vegan communities help… [...] What we desperately need is transportation help and financial support [...] We have until this weekend. Please don't let us run out of time.

### `asking_for_advice`

**Definition:** The post asks the reader for help with the poster's own situation: a recommendation, what to do, or where to go.

**Example 1:**

> Protein suggestions? [...] I've tried tofu, and tried to spice it up several different ways. There's just one thing I can't get past with it: the texture. So if anyone has any other suggestions for a protein replacement, I'd appreciate it.

**Example 2:**

> Ethical animal sanctuaries in Costa Rica? [...] we primarily want to be in the La Fortuna area. Anyone have any recommendations??

### `open_discussion`

**Definition:** The post asks the community a general question or puts forward an idea for others to weigh in on. There's no single right answer, and the replies don't solve the poster's own problem.

**Example 1:**

> Where is Your Line? You know that style of billboard with a lot of domestic animals on it that asks "Where do you draw the line?" I want to know your answer to it, but extend the examples to all living things.

**Example 2:**

> What's the first law you'd pass if you were in charge? I'll make sure that cow's milk isn't called "milk" by default anymore.

### `personal_story`

**Definition:** The post shares an experience, perspective, rant, or argument without asking the reader for help or pushing a specific action.

**Example 1:**

> Blood work - All optimal [...] I just got my blood work done for a health insurance discount and all my blood work came back at optimal levels. [...] Even though I'm not vegan for health reasons, what an amazing side effect!

**Example 2:**

> My workplace stopped labeling the vegan option separately and suddenly everyone started eating it [...] It made me wonder how much resistance to vegan food is literally just resistance to the word vegan.

### The hardest boundary

**Which two labels:** `personal_story` and `open_discussion`

**The decision rule I used every time:** Look at what the post's question is attached to. If it's attached to an idea or claim that others can agree or disagree with, it's `open_discussion`, even if the post includes a personal story. If it's attached to a feeling or experience the poster wants validated ("does anyone else feel this way?"), it's `personal_story`.

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** They are from the /r/vegan subreddit.

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->
Labeled 20 cold, then prelabelled the rest with AI. Read through them and corrected.

**Counts per label:**

| Label               | Count | Share |
| ------------------- | ----- | ----- |
| `personal_story`    | 89    | 44.5% |
| `asking_for_advice` | 74    | 37.0% |
| `open_discussion`   | 20    | 10.0% |
| `call_to_action`    | 17    | 8.5%  |
| **Total**           | 200   | 100%  |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**

> _The post:_ "Anyone else hate animals but not enough to kill/torture them." The poster dislikes pets and wildlife but is still vegan, and argues that this combination isn't unusual.
>
> _Could have been:_ `personal_story` or `open_discussion`
>
> _I chose, because:_ `open_discussion`. The post includes personal background, but its point is to put forward a position others can agree or disagree with: you can dislike animals and still refuse to eat them. The "anyone else?" is attached to an idea, not a request for sympathy.

**2.**

> _The post:_ "NEW SANCTUARY, old life." A couple introduces their newly registered rescue for about 60 animals and asks people to follow them on social media.
>
> _Could have been:_ `call_to_action` or `personal_story`
>
> _I chose, because:_ `personal_story`. Following an account doesn't count as an action that supports the cause. Nothing is signed, donated, joined, or volunteered. It's mostly an introduction to who they are.

**3.**

> _The post:_ "Watched 'Earthlings'. Humans are so cruel." The poster describes watching the documentary and ends with "Everyone should watch 'Earthlings'."
>
> _Could have been:_ `call_to_action` or `personal_story`
>
> _I chose, because:_ `personal_story`. The ending is an imperative, but it's a general plea with no concrete target. A `call_to_action` needs a specific thing to do, like a petition, a fundraiser, or an event. Most of the post is the poster's reaction to the film.

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** <!-- epochs, learning rate, batch size, seed -->
3 epochs, learning rate 2e-5, batch size 16, max length 128
tokens, seed 42 (plus 7 and 2024 for the three-seed run). Trained on Apple
Silicon GPU (MPS), torch 2.14.1.

**Anything I changed from the defaults, and why:** Nothing.

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->

200 labelled posts, stratified 70/15/15 → train 139 / val 31 /
test 30. Test split per label: `personal_story` 13, `asking_for_advice` 11,
`open_discussion` 3, `call_to_action` 3. The split was identical for all
three seeds.

`open_discussion` and `call_to_action` have only 3 test posts each, well
under 8. One right or wrong guess moves their F1 by about a third, so expect
big swings on those two labels next unit. They're also my thinnest labels
overall (20 and 17 of 200 posts), which likely explains the low macro F1
(0.25 on seed 42) next to 0.50 accuracy.

---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- _What I asked for:_ I asked for it to convert the raw JSON file I got from pages like this: "https://www.reddit.com/r/vegan/new.json?limit=100&after=t3_1wzvdig" into one csv file.
- _What came back:_ It gave me the correctly formatted csv file.
- _What I changed:_ I just had to fill in the labels.

**Moment 2**

- _What I asked for:_ I asked for help with a kernel issue in my takemeter.ipynb.
- _What came back:_ It helped me refresh my Vscode so the venv for this project showed up in the possible kernels to choose from for the takemeter.ipynb notebook.
- _What I changed:_ I didn't really change anything, I just followed the instructions.

**Pre-labelling disclosure:** Labeled 20 myself and then used AI to prelabel and then check the rest of the 200.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure                  | Baseline | Trained | Difference |
| ------------------------ | -------- | ------- | ---------- |
| Overall accuracy         | 0.833    |         |            |
| Macro F1                 | 0.657    |         |            |
| F1 — `asking_for_advice` | 1.000    |         |            |
| F1 — `call_to_action`    | 0.800    |         |            |
| F1 - `open_discussion`   | 0.000    |         |            |
| F1 - `personal_story`    | 0.828    |         |            |

**What I predicted before I looked:**

<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

I predict that after training, my overall accuracy will increase to more than 90%. I hope it will reach 93%

**What the gap actually means:**

<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.65 | 0.500 | 0.433 | 0.433 | MISSED |
| 2. F1 `asking_for_advice` / `personal_story` | each ≥ 0.60 | 0.375 / 0.632 | 0.000 / 0.605 | 0.000 / 0.605 | MISSED |
| 3. `call_to_action` recall | ≥ 0.50 | 0.00 | 0.00 | 0.00 | MISSED |
| 4. Confident predictions (≥ 0.7) correct, on ≥ ⅓ of test | ≥ 0.80 on ≥ 10/30 | top-third acc 0.70 (mean conf 0.42) | top-third acc 0.50 (mean conf 0.42) | top-third acc 0.40 (mean conf 0.42) | MISSED |
| 5. Agreement with staff labels | ≥ 75% | 97% | 97% | 97% | MET |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted      | call_to_action | asking_for_advice | open_discussion | personal_story |
| --------------------- | -------------- | ----------------- | --------------- | -------------- |
| **call_to_action**    | 0              | 1                 | 0               | 2              |
| **asking_for_advice** | 0              | 3                 | 0               | 8              |
| **open_discussion**   | 0              | 0                 | 0               | 3              |
| **personal_story**    | 0              | 1                 | 0               | 12             |

**My biggest off-diagonal number, and what it means:**
Biggest confusion: 8 real `asking_for_advice` posts called `personal_story`.
The other direction: 1 real `personal_story` posts called `asking_for_advice`.

That's lopsided — the model leans toward `personal_story` on this pair.
A direction like that usually points at your labelling rather than the model.

<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| #   | Criterion | Target | Verdict | How I decided |
| --- | --------- | ------ | ------- | ------------- |
| 1   | Overall accuracy | ≥ 0.65 | MISSED | Best seed was 0.500; all three below target |
| 2   | F1 `asking_for_advice` and `personal_story` | each ≥ 0.60 | MISSED | `personal_story` passed on all seeds (0.605–0.632), but `asking_for_advice` was 0.375, 0.000, 0.000 |
| 3   | `call_to_action` recall | ≥ 0.50 | MISSED | 0 of 3 caught on every seed |
| 4   | Confident predictions ≥ 80% correct, on ≥ ⅓ of test | ≥ 0.80 on ≥ 10/30 | MISSED | The 10 most confident predictions were only 0.70 / 0.50 / 0.40 correct, below 0.80 on every seed, and mean top-label confidence was just 0.42. Per-prediction confidences weren't saved, so I couldn't count exactly how many cleared 0.7; the verdict is inferred from these summaries |
| 5   | Agreement with staff labels | ≥ 75% | MET | 29 / 30 = 97% from `agreement.py`; one run, not seed-dependent |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

**1. Overall accuracy — cause: the model was undertrained and never got past the majority label.**
Seeds 7 and 2024 predicted `personal_story` for all 30 test posts, so their 0.433 is exactly the majority share (13/30). Seed 42 only escaped that a little. With 139 training posts at batch size 16, three epochs is only 27 optimizer steps. Training loss ended around 1.20, barely under the 1.39 of random guessing across four labels, and validation accuracy stayed flat at 0.42–0.48 across all three epochs. It isn't my labelling: I matched staff on 97% of their posts applying their written rules, and the zero-shot baseline (no training at all) reached the same 0.50 accuracy.

**2. `asking_for_advice` F1 — cause: undertraining, not an inconsistent boundary.**
The confusion matrix is lopsided (8 advice posts called `personal_story`, only 1 the other way), which can point at labelling. Two things say it doesn't here. First, the zero-shot baseline scored 0.92 F1 on `asking_for_advice` using my labels, so these posts are clearly separable from their text. Second, my 97% agreement shows I apply written definitions consistently. With 74 examples this label isn't thin; the fine-tuned model just never trained long enough to predict anything but `personal_story` on two of three seeds. My one staff-set disagreement was a copy-paste slip, so it's still worth rereading those 8 posts for the same kind of mislabel.

**3. `call_to_action` recall — cause: too few examples for the label.**
Only 17 of 200 posts (11 in training, 3 in test), and 8 of the 17 come from one account's template. The model predicted `call_to_action` zero times on every seed. With 3 test posts, each one is worth 33 points of recall, so this was always going to be the jumpiest score.

**4. Confidence — cause: the same as 1.**
A model that hasn't learned to separate the labels can't be confident. Its mean top-label confidence was 0.42 whether it was right or wrong (0.416 vs 0.409 on seed 42), so confidence carried no information about correctness. This miss follows from the first, not from a separate problem.

---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** **29 / 30 = 97**%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**

> _The post:_ sp_014 — "Times are down about 3% since the shoe rules changed, so obviously the shoes are the whole story and the sport has a problem."
>
> _Staff said / I said:_ Staff `hot_take` / I said `analysis`
>
> _My call, and why:_ Staff is right, and so was my intended label: I meant `hot_take` and pasted the wrong label from the row above while working through the file. Under Rule 1 the 3% is decoration: "obviously" and "the whole story" are certainty the number can't support, and "the sport has a problem" is a conclusion it doesn't touch at all.
>
> _Which pile:_ (a), but as a clerical slip, not a misreading of the rule.

**What the pattern in my disagreements tells me:**

My only disagreement wasn't a misreading of the rules. It was a copy-paste slip. That's a way labels go wrong even when the definitions are clear, and I labelled my own 200 posts the same way. Some of my `labels.csv` rows could carry the same kind of error. A slip like that would look like inconsistent labelling when I diagnose the model's misses, so I should check a sample of the posts the model got wrong for mislabels before blaming the boundaries.

---

## The Improvement

**What I changed:** `EPOCHS` from 3 to 10 in the notebook. Nothing else: same data, same learning rate (2e-5), batch size (16), max length (128) and seeds (42, 7, 2024).

**Which diagnosis pointed at it:** Diagnoses 1 and 2. Three epochs was only 27 optimizer steps, training loss ended at 1.20 (random guessing is 1.39), and two of three seeds predicted `personal_story` for every post. Meanwhile the zero-shot baseline got 0.92 F1 on `asking_for_advice` from the same text, so the signal was there and the model hadn't trained long enough to find it. My 97% staff agreement argued against relabelling, and adding examples wouldn't help a label like `asking_for_advice` that already had 74.

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.65 | 0.633 | 0.567 | 0.567 | MISSED |
| 2. F1 `asking_for_advice` / `personal_story` | each ≥ 0.60 | 0.769 / 0.643 | 0.636 / 0.625 | 0.696 / 0.581 | MISSED |
| 3. `call_to_action` recall | ≥ 0.50 | 0.00 | 0.00 | 0.00 | MISSED |
| 4. Confident predictions (≥ 0.7) correct, on ≥ ⅓ of test | ≥ 0.80 on ≥ 10/30 | top-third acc 0.80 | top-third acc 0.80 | top-third acc 0.60 | MISSED |
| 5. Agreement with staff labels | ≥ 75% | 97% | 97% | 97% | MET |

**Did it help, and how do I know:**

Yes, but not enough to flip any verdict. Every seed improved: accuracy went from 0.500 / 0.433 / 0.433 to 0.633 / 0.567 / 0.567, and macro F1 from 0.252 / 0.151 / 0.151 to 0.353 / 0.315 / 0.319. The biggest change was `asking_for_advice`, which went from 0.375 / 0.000 / 0.000 to 0.769 / 0.636 / 0.696, now above 0.60 on all three seeds. The model stopped guessing `personal_story` for everything: on seed 42 it went from catching 3 of 11 advice posts to 10 of 11. The last logged training loss was about 0.71, down from about 1.17. That confirms diagnoses 1 and 2: the model was undertrained.

It still missed every criterion it missed before. Criterion 2 now fails only because `personal_story` slipped to 0.581 on seed 2024: the model overcorrected a little and called 4 story posts advice. Criterion 1 came within one post on seed 42 (0.633 vs 0.65) but stayed well short on the other two. Criteria 3 and 4 didn't move in a way that matters: `call_to_action` and `open_discussion` were still never predicted on any seed (3 test posts each), and seed 2024's most confident third was only 0.60 correct. Training longer can't fix a label with 11 training examples. That one needs more data, which is what diagnosis 3 said.

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->

---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->

**1. Overall accuracy (0.633 / 0.567 / 0.567, target 0.65).** Most of the remaining errors are the 6 `call_to_action` and `open_discussion` test posts, which the model never gets right. Fixing those two labels (see 3) is the most likely way to gain the 3 or so posts each seed still needs. I stopped because the unit allows one change, and I'd already used it on epochs.

**2. `asking_for_advice` / `personal_story` F1 (`personal_story` 0.581 on seed 2024).** After 10 epochs the model overcorrected and called 4 real story posts advice. Next I'd reread those posts against my decision rule to see whether they ask the reader for something. If they do, the labels are wrong; if they don't, the model is keying on question marks. I'd also try 6–7 epochs, since seed 2024's validation accuracy peaked at epoch 6. I stopped for the same one-change reason.

**3. `call_to_action` recall (0 / 0 / 0).** This needs more data, not more training. With 11 training examples, 8 from one template, there isn't enough for the model to learn the label. I'd collect and label 30 more calls to action from different accounts. I stopped because that's a whole labelling batch and the unit allows only one change.

**4. Confident predictions (most-confident third 0.80 / 0.80 / 0.60).** This should follow from 1–3: the confident wrong calls are mostly calls to action and discussions forced into the two big labels. I'd also save each prediction's confidence, so I could check the ≥ 0.7 bar directly and not infer it from thirds. I stopped because the notebook doesn't record per-prediction confidence and changing the evaluation code was outside the one change.

**The gap between what I meant my labels to capture and what the model
learned:**

I meant my labels to capture what a post is *for*: sharing an experience, asking for help, putting an idea up for debate, or getting people to act, but the model learned only my two biggest labels. In the after run (seed 42) it gets 10 of 11 advice posts and 9 of 13 stories right, but sends all 3 `open_discussion` posts and 2 of 3 `call_to_action` posts to `personal_story`, so for it, "a post with personal background" means a story, whatever the post is asking for.

<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
