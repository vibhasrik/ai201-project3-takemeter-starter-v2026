# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. _"The model is accurate"_ is an
opinion. _"Every label has an F1 of at least 0.60 on the held-out set"_ is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — _"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"_. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area                  | A question it could answer                                            |
| --------------------- | --------------------------------------------------------------------- |
| Overall accuracy      | How often does it need to be right to be worth using?                 |
| Per-label performance | Is one label allowed to be much worse than the others?                |
| Balance               | How lopsided can your label counts get before it's a problem?         |
| Consistency           | If someone else labelled the same posts, how often should you agree?  |
| Confidence            | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1.

On the held-out set, overall accuracy is at least **0.65** on all three seeds.

**Why this target:**
`personal_story` is 44.5% of my data, so a model that always guesses it would score about 0.45. A useful model has to beat that clearly. With about 30 test posts, each post is worth about 3 points of accuracy, so 0.65 means roughly 20 of 30 correct. That's about 6 posts better than the majority guess, and it leaves room for one weak seed.

---

## 2.

`asking_for_advice` and `personal_story` each have an F1 of at least **0.60** on all three seeds.

**Why this target:**
These are my two biggest labels (74 and 89 posts), so each has about 11 to 13 test examples, and their scores should be the most stable. `asking_for_advice` also has clear text cues ("any tips," "recommend," "should I," a question aimed at the reader). If the model can't reach 0.60 on these two, it hasn't learned my taxonomy at all.

---

## 3.

`call_to_action` recall is at least **0.50** on all three seeds. In other words, the model catches at least half of the real calls to action in the test set.

**Why this target:**
This is the label a regular would least want to miss, so I care about recall more than precision. But it's my smallest label (17 posts, so about 3 in each test split), and 8 of the 17 come from one account using one template. The score will jump around between seeds, and the model may just learn to spot that one template, so I set this target looser than the others.

---

## 4.

On every seed, predictions where the model's top-label confidence is at least **0.7** are correct at least **80%** of the time. At least a third of the test predictions must clear that confidence bar, so the criterion can't be met by being confident on just two or three posts.

**Why this target:**
My overall accuracy target is 0.65, so confident predictions should do clearly better than that. Otherwise confidence tells a regular nothing about which tags to trust. About 10% of my posts are hard cases on soft boundaries (general plea vs `call_to_action`, story vs discussion), and those are exactly where I'd expect the model to be unsure. If it handles the clear-cut posts well, like advice posts with "any tips?" or fundraisers with a link and a deadline, it should be right on at least 8 of every 10 confident calls. I added the one-third minimum because with only about 30 test posts, a model confident on just 3 of them could pass by luck.

---

## 5.

My labels and the staff labels agree on at least **75%** of the posts both of us labeled.

**Why this target:**
I marked 19 of my 200 posts (about 10%) as hard cases, and most sit on two soft boundaries: general pleas vs `call_to_action`, and story vs discussion. Someone working from my written definitions will probably disagree on most of those, plus some others, so 90% isn't realistic. Below 75%, though, my definitions aren't clear enough for anyone else to apply, and then it isn't fair to judge the model against my labels either.

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
