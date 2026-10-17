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

**Base model:**

**Settings:** <!-- epochs, learning rate, batch size, seed -->

**Anything I changed from the defaults, and why:**

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->

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

- _What I asked for:_
- _What came back:_
- _What I changed:_

**Pre-labelling disclosure:**

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure          | Baseline | Trained | Difference |
| ---------------- | -------- | ------- | ---------- |
| Overall accuracy |          |         |            |
| Macro F1         |          |         |            |
| F1 — `label_one` |          |         |            |
| F1 — `label_two` |          |         |            |

**What I predicted before I looked:**

<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**

<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
| --------- | ------ | ------- | ------ | --------- | ------- |
| 1.        |        |         |        |           |         |
| 2.        |        |         |        |           |         |
| 3.        |        |         |        |           |         |
| 4.        |        |         |        |           |         |
| 5.        |        |         |        |           |         |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |     |     |     |
| ---------------- | --- | --- | --- |
| \*\*\*\*         |     |     |     |
| \*\*\*\*         |     |     |     |
| \*\*\*\*         |     |     |     |

**My biggest off-diagonal number, and what it means:**

<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| #   | Criterion | Target | Verdict | How I decided |
| --- | --------- | ------ | ------- | ------------- |
| 1   |           |        |         |               |
| 2   |           |        |         |               |
| 3   |           |        |         |               |
| 4   |           |        |         |               |
| 5   |           |        |         |               |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** **_ / 30 = _**%

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

> _The post:_
>
> _Staff said / I said:_
>
> _My call, and why:_
>
> _Which pile:_

**2.**

> _The post:_
>
> _Staff said / I said:_
>
> _My call, and why:_
>
> _Which pile:_

**What the pattern in my disagreements tells me:**

---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
| --------- | ------ | ------- | ------ | --------- | ------- |
| 1.        |        |         |        |           |         |
| 2.        |        |         |        |           |         |
| 3.        |        |         |        |           |         |
| 4.        |        |         |        |           |         |
| 5.        |        |         |        |           |         |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->

---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->

**The gap between what I meant my labels to capture and what the model
learned:**

<!-- Two sentences. Your confusion matrix is the evidence. -->

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
