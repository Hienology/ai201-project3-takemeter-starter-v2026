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

TakeMeter reads post titles from r/tennis and sorts them by how much a title tells on its own, without the image, video or link that comes with almost every post there. A `full` title states the news itself (a result, an announcement, a report, a statement or a record); a `partial` title points at specific tennis news or discussion but leaves the fact to the image, the link or the comments; a `nothing` title is a fun post, fan activity or meme that tells nothing about a tennis event. The question behind it is whether a title is "informative enough to fully know the primary stuff without need to look at the image / video and/or the comments". The classifier is `distilbert-base-uncased`, fine-tuned on 210 titles I labeled myself.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `full`

**Definition:** A title that, on its own, tells the full event and its nature.

**Example 1:**
> Shanghai SF: Davidovich Fokina def. Zverev - 5-7 6-4 6-3

**Example 2:**
> We will have the Spanish final at the Shanghai ATP, Alejandro Davidovich Fokina x Rafa Jodar

### `partial`

**Definition:** A title that is context or time sensitive, naming specific news, a result, an event or an announcement, but that on its own does not tell the full event and its nature.

**Example 1:**
> What Iga told Maja at the net…

**Example 2:**
> players with a slam final and a WTA 1000 final in 2026

### `nothing`

**Definition:** A title that isn't about serious tennis news or discussion (fun posts, fan activities, memes), so on its own it tells nothing about a tennis event, whatever names it mentions.

**Example 1:**
> Something's off

**Example 2:**
> Tennis Moments

### The hardest boundary

**Which two labels:** `full` and `partial`

**The decision rule I used every time:** If the title sentence, read alone without the image by anyone from a passing stranger to a regular, states a usable fact that is news (a result, an announcement, a report, a statement or a record), it is `full`; if it doesn't state the fact itself, or the fact is only a moment within a match (a shot, a point, a live set update), it is `partial`.
<!-- e.g. "If the post names a specific checkable fact, it's `analysis`, even
     if the tone is heated." -->



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** r/tennis (old.reddit.com), post titles only: no comments, no post text, no images. Two listings:

- **New:** 8 pages of r/tennis/new, posts from Oct 19-25, 2026, leaving out the mod bot's daily discussion threads. Most `full` and `partial` titles, and all 22 cold rows, come from here.
- **Tennis Nonsense flair:** the first 3 pages of that flair's listing, newest first (mid-September to late October 2026). `nothing` titles were about 1 in 40 on New and Best, so I went where they are. Each title was still judged by my definitions, not by its flair: the Roddick/Serena "TIL" title from this flair is `full`.

I labeled titles in listing order up to quotas of 100 `full`, 55 `partial` and 55 `nothing`; once a label was full, later titles of that label were left out, not relabeled. 55 is about the smallest count that gives each label at least 8 test titles on every seed (42, 7, 2024). Titles are as posted, except for two social-media handles I removed. One repost of the same match result was dropped; two pairs of different posts about the same final (a trophy photo and the result line) were kept.

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->
By hand, with no AI pre-labelling: every label is mine. I labeled 22 titles from New cold first, alone, before the AI saw them; then I pasted them into the chat and the AI added them to the file with `cold` in `note`. For the other 188, the AI turned the pages I pasted into numbered title lists, I answered with a label per number (`f`/`p`/`n`), and a script added them, in chunks of about 50. After each chunk a checker counted the labels and looked for duplicates, and the AI pointed out labels of mine that contradicted each other or my written definitions, without suggesting labels. I re-judged those myself: four labels changed, and I reworded the `nothing` definition to "isn't about serious tennis news or discussion". Six rows carry a `hard case` note.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `full` | 100 | 48% |
| `partial` | 55 | 26% |
| `nothing` | 55 | 26% |
| **Total** | 210 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* Elena Rybakina with her mom, & sister in Ningbo finals
>
> *Could have been:* `full` or `partial`
>
> *I chose, because:* `partial`. The winner usually poses for photos like this, so a regular might guess that Rybakina won Ningbo, but the title never says who won. My rule asks whether the title itself states the fact, and this one only hints at it.

**2.**
> *The post:* Sabalenko at ufc
>
> *Could have been:* `partial` or `nothing`
>
> *I chose, because:* `nothing`. A player's name plus an event usually makes a title `partial`, but here the misspelt name is the joke (taken from a UFC graphic), and without the image the title says nothing about any tennis event.

**3.**
> *The post:* TIL in 2019 Andy Roddick and Serena Williams went on TV and performed a lip-synced cover of "Finesse" by Bruno Mars and Cardi B
>
> *Could have been:* `full` or `nothing`
>
> *I chose, because:* `full`. It came from the Tennis Nonsense flair and it is fun, but the title states the whole fact (who, what, when), so I could skip the video and still get the point. The flair doesn't decide the label; the title does.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`, fully fine-tuned with the Hugging Face `Trainer` (no LoRA).

**Settings:** <!-- epochs, learning rate, batch size, seed -->
3 epochs, learning rate 2e-5, batch size 16, max length 128 tokens, seed 42. Trained on an Apple Silicon GPU (`mps`), torch 2.14.1.

**Anything I changed from the defaults, and why:** Nothing; I used the notebook's defaults. For reference, my longest title is 80 tokens (median 21), so the 128-token limit never cuts a title off.

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->
train 146 / validation 32 / test 32 (stratified 70/15/15, seed 42).

| Label | Train | Validation | Test |
|---|---|---|---|
| `full` | 70 | 15 | 15 |
| `partial` | 38 | 9 | 8 |
| `nothing` | 38 | 8 | 9 |

Every label has at least 8 test titles, but only just: in `partial`, one title moves recall by 0.125.

---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* which label split was better for about 200 titles: 50/25/25 or 60/20/20 (`full`/`partial`/`nothing`).
- *What came back:* the AI ran both through the notebook's own split code on seeds 42, 7 and 2024. Both left labels short in the test split: 100/50/50 left one label at 7 test titles on every seed, and 120/40/40 left two labels at 6. A third option, 100/55/55, gave every label 8 or more on every seed.
- *What I changed:* I dropped both of my plans and collected to 100/55/55, 210 titles.

**Moment 2**

- *What I asked for:* to add my labels for 44 Tennis Nonsense titles to the file.
- *What came back:* no label suggestions, but three groups where my labels contradicted each other or my written definitions: in-match moments labeled both `partial` and `nothing`; a stated record and two announcements labeled `nothing`, though my rule calls records and announcements `full`; and titles naming a player and an event labeled `nothing`, though my `nothing` definition then said a `nothing` title is not context or time sensitive.
- *What I changed:* I relabeled four titles (two to `partial`, two to `full`) and kept the rest, deciding that serious tennis discussion is `partial` while fun posts, fan activities and memes are `nothing`. The AI reworded the `nothing` definition from my own phrases, and I confirmed it.

**Pre-labelling disclosure:**
No pre-labelling: every label in `labels.csv` is mine, and the AI never suggested one. What the AI (Claude, in VS Code) did:

- Turned the Reddit pages I pasted into numbered title lists (dropping usernames, scores, links, and two social-media handles inside titles), and wrote my labels into `labels.csv` by number with a script. It also typed in my 22 cold rows after I had labeled them alone.
- Drafted wording I asked for, from my own words: the three label definitions and the later `nothing` rewording, the decision-rule sentence, and the "Why this target" lines for criteria 2-5. The numbers and choices in `criteria.md` are mine.
- Wrote 8 made-up boundary titles to stress-test my definitions; none of them is in `labels.csv`.
- Drafted this README's Unit 5 write-up (What This Does, The Dataset, The Training Run, How I Used AI) from my recorded decisions and reasons, at my request; I reviewed it.
- Unit 6 staff set: printed the 30 staff posts in chat for me to label, and wrote my labels into `my_staff_labels.csv`; it made no comment on any post before my file was saved. In the Agreement Report the calls and piles are mine; the AI rephrased my reasons onto the staff taxonomy's wording and showed me the case for the other label before I confirmed each one.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.250 | 0.656 | +0.406 |
| Macro F1 | 0.133 | 0.492 | +0.358 |
| F1 — `full` | 0.000 | 0.769 | +0.769 |
| F1 — `partial` | 0.400 | 0.000 | -0.400 |
| F1 — `nothing` | 0.000 | 0.706 | +0.706 |

Seed 42's test split (32 titles, `test_split.csv`), baseline using my `label_definitions.txt`. The baseline answered `partial` for all 32 titles. It did the same on seed 7's and seed 2024's test splits (`baseline_results_seed7.json`, `baseline_results_seed2024.json`, used for criterion 5).

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->
I expect the trained model to beat the baseline by about 20 points of accuracy, with the biggest gain on `full`, because a `full` title is the event itself, with clear, strict boundaries, and needs no reading of context or intent the way `partial` and `nothing` titles do. I had already seen the trained model's section 5 scores (F1: `full` 0.769, `partial` 0.000, `nothing` 0.706) when I wrote this.

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->
Most of the +0.41 is the baseline failing, not the trained model succeeding: the baseline answered `partial` for every title (0.250, below the 0.47 of always answering `full`), so it set no boundary at all. Fine-tuning did learn something real (`full` F1 0.77-0.82 on all three seeds, `nothing` on two of three), but it never learned the `full`/`partial` boundary: on seeds 42 and 2024 it predicted `partial` zero times, and overall it beats always answering `full` by only 6 titles (21 vs 15). So it's a success on `full` and a failure on `partial`, the label that depends on context: whether a title states the fact itself or only points at it.



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. F1 per label (`full` / `partial` / `nothing`) | ≥ 0.75 / 0.50 / 0.75 | 0.769 / 0.000 / 0.706 | 0.788 / 0.435 / 0.000 | 0.824 / 0.000 / 0.636 | MISSED |
| 2. Accuracy | ≥ 0.78 (25 of 32) | 0.656 (21) | 0.562 (18) | 0.656 (21) | MISSED |
| 3. Predicted count vs real count (`full` / `partial` / `nothing`) | each within 2 | 24/0/8 vs 15/8/9 | 18/14/0 vs 15/9/8 | 19/0/13 vs 15/8/9 | MISSED |
| 4. Staff agreement | ≥ 20 of 30 | 24/30 (no seed) | 24/30 (no seed) | 24/30 (no seed) | MET |
| 5. Trained F1 minus baseline F1 (`full` / `partial` / `nothing`) | each ≥ +0.20 | +0.769 / -0.400 / +0.706 | +0.788 / -0.004 / 0.000 | +0.824 / -0.400 / +0.636 | MISSED |

Device: `mps` (Apple Silicon GPU), torch 2.14.1. Numbers from `results_three_seeds_before.json` and `baseline_results*.json`.

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

Seed 42 only (section 7 prints the first seed). Rows are true labels, columns are predictions.

| true \ predicted | `full` | `partial` | `nothing` |
|---|---|---|---|
| **`full`** | 15 | 0 | 0 |
| **`partial`** | 6 | 0 | 2 |
| **`nothing`** | 3 | 0 | 6 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->
The `full`/`partial` boundary fails in one direction only: 6 of 8 `partial` titles were called `full`, and 0 of 15 `full` titles were called `partial`. A `full` title has a strict test (it states the result, announcement or record itself), so the model never doubts those; a `partial` title names the same players and events but leaves the fact out, so to the model it looks like a `full` title, and it falls back to `full`, which is also the biggest label (70 of 146 training titles).



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | F1 per label, all three seeds | `full` ≥ 0.75, `partial` ≥ 0.50, `nothing` ≥ 0.75 | MISSED | `partial` 0.000 / 0.435 / 0.000 and `nothing` 0.706 / 0.000 / 0.636 are below target on every seed; only `full` (lowest 0.769) holds |
| 2 | Accuracy, all three seeds | ≥ 0.78 (25 of 32) | MISSED | Best seed 0.656 (21 of 32); seed 7 0.562 (18) |
| 3 | Predicted count within 2 of real count, per label, all three seeds | gap ≤ 2 | MISSED | Only seed 42 `nothing` holds (8 vs 9); worst gaps are 9 (`full`, seed 42) and 8 (`partial` seeds 42 and 2024, `nothing` seed 7) |
| 4 | Staff agreement (`agreement.py`) | ≥ 20 of 30 | MET | 24 of 30 (0.80); measured once, no seed |
| 5 | Trained F1 minus baseline F1, per label, all three seeds | each ≥ +0.20 | MISSED | `partial` −0.400 / −0.004 / −0.400 on every seed, and `nothing` 0.000 on seed 7; only `full` (lowest +0.769) holds |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->
**Shared cause (#1, #2, #3, #5): too few examples for the two smaller labels, plus a genuinely hard pair (`full`/`partial`).** On every seed the model drops one small label completely: `partial` on seeds 42 and 2024, `nothing` on seed 7 (each predicted 0 times).

*Evidence:*
- `partial` and `nothing` have 38 training titles each against 70 for `full`. Across seeds, `partial` F1 runs 0-0.435 and `nothing` F1 runs 0-0.706, far past the notebook's 0.10 stability line.
- The seed-42 matrix fails in one direction only: 6 `partial` titles called `full`, 0 the other way.
- `partial` and `full` titles name the same players and events; what separates them is whether the fact is stated, which mostly shows as what's missing.

*Labelling problem or model problem:* my agreement report (24/30, 3 posts in pile (a)) shows I apply a written rule loosely about 1 time in 10, so some noise at my hardest boundary, `full`/`partial`, is likely. But noise can't make a label get predicted zero times. That is the model with too few examples.

**Per miss:**
- **#1:** `partial` and `nothing` fall below target on every seed because the dropped label scores 0 and the other small label's titles leak into `full`.
- **#2:** on seed 42, 8 of the 11 errors are `partial` titles; getting them right would have reached 29 of 32. Accuracy loses whichever label was dropped.
- **#3:** the dropped label's count is 0 and its titles swell another label: `full` 24 vs 15 (seed 42), `partial` 14 vs 9 (seed 7), `nothing` 13 vs 9 (seed 2024).
- **#5:** the baseline gets `partial` F1 0.40 for free by calling every title `partial`, while the trained model drops `partial` on two seeds and `nothing` on seed 7 (0.000 vs 0.000). The same cause, made worse by the baseline's collapse.



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** 24 / 30 = 80%

I labelled 29 of the 30 before seeing the staff labels. On `sp_002` I was undecided between `analysis` and `hot_take` when the staff labels arrived, and I picked `hot_take` afterwards.

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

**1. `sp_002`**
> *The post:* "I tracked sleep against session quality for eleven weeks. Under seven hours the next day's intervals were 6-8 seconds per mile slower, every time bar one. Small sample, one runner, but it's consistent enough that I've stopped scheduling hard days after late shifts."
>
> *Staff said / I said:* `analysis` / `hot_take`
>
> *My call, and why:* I stand by `hot_take`. The `analysis` definition needs a *checkable* fact; this post's numbers come from one runner's private training log, which no reader can check, so the conclusion rests on the writer's word. It isn't `reaction` either (rule 2): it reports eleven weeks of tracking, not something that just happened and how it felt.
>
> *Which pile:* (b). The page doesn't say whether one person's own records count as a checkable fact.

**2. `sp_005`**
> *The post:* "Carbon plates aren't the whole story. My times dropped 2% in the shoes and another 2% the following year in the same pair, when the only thing that changed was going from 35 to 50 miles a week."
>
> *Staff said / I said:* `analysis` / `hot_take`
>
> *My call, and why:* I stand by `hot_take`, for the same reason as `sp_002`. The `analysis` definition needs a *checkable* fact; the two 2% drops are the writer's own times, which no reader can check, so "aren't the whole story" rests on the writer's word.
>
> *Which pile:* (b), the same gap as `sp_002`.

**3. `sp_007`**
> *The post:* "Ran the same loop in 4C and in 24C within a fortnight, same effort by heart rate. 90 seconds slower in the heat over six miles. People keep saying heat costs you 'a bit' and it's more than that."
>
> *Staff said / I said:* `analysis` / `hot_take`
>
> *My call, and why:* I stand by `hot_take`, for the same reason as `sp_002`. The 90 seconds comes from two of the writer's own runs, which no reader can check, so the *checkable* fact the `analysis` definition asks for isn't there; "more than 'a bit'" rests on the writer's word.
>
> *Which pile:* (b), the same gap as `sp_002`.

**4. `sp_009`**
> *The post:* "The 10K route was remeasured in 2021 and came out 60 metres long. Everyone comparing PBs from before and after that is comparing two different races, which is about 12 seconds at club pace."
>
> *Staff said / I said:* `analysis` / `hot_take`
>
> *My call, and why:* Staff is right. The remeasurement is a public, checkable fact (a date, 2021, and a measurement, 60 metres), and rule 1 holds: the conclusion that PBs before and after 2021 aren't comparable rests on it, down to the 12 seconds. Unlike `sp_002`, `sp_005` and `sp_007`, the fact isn't the writer's private record, so my own reading puts it in `analysis`. I applied rule 1 loosely.
>
> *Which pile:* (a).

**5. `sp_010`**
> *The post:* "Three of us ran the same session on the same day, different shoes, within 10 seconds of each other over 5 miles. I'm not saying shoes do nothing, but whatever they do was smaller than the noise between three people."
>
> *Staff said / I said:* `analysis` / `reaction`
>
> *My call, and why:* Neither, in my reading: `hot_take`. My `reaction` was wrong: the post asserts something general about shoes rather than reporting how a run felt (rule 2). Against `analysis`: the 10 seconds comes from one session among three runners, which no reader can check, so "whatever they do was smaller than the noise" is the writer's judgement, not a checkable fact doing the work.
>
> *Which pile:* (a), for my own label: the rules covered `reaction` and I applied them loosely.

**6. `sp_012`**
> *The post:* "Heart rate zones are astrology for runners."
>
> *Staff said / I said:* `hot_take` / `reaction`
>
> *My call, and why:* Staff is right. Rule 2: `reaction` reports what just happened to the writer and how it felt; this reports nothing that happened and asserts something general about the world, with no support, so it's `hot_take`. I applied rule 2 loosely.
>
> *Which pile:* (a).

**What the pattern in my disagreements tells me:**
Four of my six disagreements (`sp_002`, `sp_005`, `sp_007`, `sp_010`) come from one reading I added: numbers from the writer's own runs aren't a checkable fact. Where the fact was public (`sp_009`) I agree it's `analysis`, so the reading is consistent, but the page never says whose data counts. The other three are pile (a), loose applications of rules 1 and 2, which is 3 of 30 posts. For my own 210 titles, both risks apply: I may hold conditions I never wrote into my rule, and I may apply a written rule loosely on about 1 title in 10.



---

## The Improvement

**What I changed:**
One setting: `EPOCHS` from 3 to 10 in section 2. Everything else stayed the same: learning rate 2e-5, batch 16, the same `labels.csv`, the same seeds 42, 7 and 2024, run with `RUN_LABEL = "after"`. Unit 5's `results.json` and `test_split.csv` were not re-run.

**Which diagnosis pointed at it:**
The shared diagnosis: on every seed the model drops one of the two smaller labels and falls back on `full`. The notebook keeps the last epoch's model, and 146 training titles at batch 16 is about 10 updates per epoch, so 3 epochs is about 30 updates in total; I expected that to stop training while the model still leaned on the biggest label. More epochs attacks that part of the diagnosis without new data. It can't fix the other part, 38 training titles per small label.

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. F1 per label (`full` / `partial` / `nothing`) | ≥ 0.75 / 0.50 / 0.75 | 0.848 / 0.500 / 0.737 | 0.812 / 0.625 / 0.625 | 0.867 / 0.545 / 0.333 | MISSED |
| 2. Accuracy | ≥ 0.78 (25 of 32) | 0.750 (24) | 0.719 (23) | 0.656 (21) | MISSED |
| 3. Predicted count vs real count (`full` / `partial` / `nothing`) | each within 2 | 18/4/10 vs 15/8/9 | 17/7/8 vs 15/9/8 | 15/14/3 vs 15/8/9 | MISSED |
| 4. Staff agreement | ≥ 20 of 30 | 24/30 (no seed) | 24/30 (no seed) | 24/30 (no seed) | MET |
| 5. Trained F1 minus baseline F1 (`full` / `partial` / `nothing`) | each ≥ +0.20 | +0.848 / +0.100 / +0.737 | +0.812 / +0.186 / +0.625 | +0.867 / +0.145 / +0.333 | MISSED |

Same three seeds and test splits as Run Log — Before; the baseline numbers are unchanged (the baseline isn't trained). Device `mps`. Numbers from `results_three_seeds_after.json`.

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->
Partly. Macro F1 rose on all three seeds (0.492 → 0.695, 0.408 → 0.688, 0.487 → 0.582), and no seed drops a label any more: `partial` F1 went from 0.000 / 0.435 / 0.000 to 0.500 / 0.625 / 0.545, so criterion 1's `partial` target now holds on every seed (seed 42 exactly at 0.50). Accuracy rose on seeds 42 and 7 (21 → 24 and 18 → 23 of 32) and stayed at 21 on seed 2024. But all four misses are still misses, and seed 2024 got worse on `nothing` (0.636 → 0.333): 6 of its 9 `nothing` titles now go to `partial`, so there the over-calling moved from `full` to `partial` instead of going away.

How I know it was the change: my first "after" run accidentally kept 3 epochs and reproduced the before-numbers exactly, so training on my Mac is repeatable and the difference comes from the epochs, not chance. On seed 2024 the validation macro F1 peaked at epoch 5 (0.693) and fell to 0.578 by epoch 10, which looks like overfitting; that seed's drop on `nothing` fits.



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
