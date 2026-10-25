# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

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

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

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

<!-- Your criterion. It must name a number. -->
On the held-out test split, F1 is at least 0.75 for `full`, 0.50 for `partial`
and 0.75 for `nothing`, on each of seeds 42, 7 and 2024.



**Why this target:**
"0.5 much lower than 0.75 since it is ambiguous by itself, you can ask question on how much is enough, how significant of this over that is enough. I don't know how seeds work here, I can set 0.8 but seeds value might not bring the same results so I went with 0.75"


---

## 2.

<!-- Your criterion. -->
Accuracy on the held-out test split is at least 0.78 (25 of 32 titles) on each
of seeds 42, 7 and 2024.



**Why this target:**
I'd need 25 of the 32 test titles right (I wished for 30+) before trusting the model to tell a scroller which titles they can read without clicking. Always guessing `full` already gets 15 of 32 right (0.47), so 25 means 10 titles of real skill beyond that.



---

## 3.

<!-- Your criterion. -->
On the held-out test split, the number of titles the model predicts as each
label (`full`, `partial`, `nothing`) is within 2 of that label's real count,
on each of seeds 42, 7 and 2024.



**Why this target:**
Missing is worse than an extra click: if the model calls a title `full` when it isn't, a scroller skips a post they actually needed, while opening a post whose title was already complete still shows the comments, so it isn't wasted. A lean of up to 2 of 32 titles (about 6%) can be noise; 3 or more means the model is really leaning.




---

## 4.

<!-- Your criterion. -->
Under `data/staff_taxonomy.md`, my labels on the 30 staff posts agree with
staff's on at least 20 of 30 (0.67), as reported by `agreement.py`. Measured
once; it doesn't depend on the seed.



**Why this target:**
Two in three feels right: I'll be applying staff's rules for the first time without knowing how simple or complex they are, and even with my own rules my stress-test labels matched the intended ones on only 4 of 8 boundary titles before I tightened them. Random guessing would agree on about 10 of 30, so 20 is well above chance.




---

## 5.

<!-- Your criterion. -->
On each of seeds 42, 7 and 2024, for each of `full`, `partial` and `nothing`,
the trained model's F1 on that seed's held-out test titles is at least 0.20
higher than the zero-shot baseline's (`baseline.py`, using my
`label_definitions.txt`) on the same titles.



**Why this target:**
A 20-point F1 gap means the trained model gets about 3 more `full` titles and about 2 more `partial` and `nothing` titles right per 32-title test split than a model that only read my definitions; that's what makes labeling 210 titles worth it to me.




---

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
