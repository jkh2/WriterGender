# WriterGender

A single-file web app that measures which way a piece of writing leans on the
language features that differ, on average, between male and female authors —
and shows every number behind the result.

It analyzes writing, not writers. A result is a statement about a text.

**[Live demo](https://jkh2.github.io/writergender/)** · No install, no account,
no server. Everything runs in your browser and nothing you paste leaves your device.

---

## Status

**Phase 1 of 4 complete.** The analyzer works and its method is fully documented.

**The accuracy of this tool has not been measured yet.** That is not modesty, it
is the actual state of the project. Comparable published tools report 60–70%
accuracy, but WriterGender uses its own word lists, so that figure is not
inherited and cannot be claimed. Establishing a real number is Phase 2, and the
app ships with a built-in test harness for exactly that purpose.

Until that number exists, treat output as exploratory.

---

## What it does

**Analyze** — Paste a writing sample and get a lean, a score from −1 (male) to
+1 (female), and a full breakdown of all 18 language categories that produced it.

**Compare** — Score two samples on one scale. Built for a specific question: run
the same AI model under two different prompts and see whether the measured lean
follows the prompt rather than the model.

**Test accuracy** — Add writing samples whose author you know. The app scores
them and reports how often it was right, broken down by group. This is how the
tool earns an accuracy claim instead of borrowing one.

---

## How it works

Each of 18 language categories is counted as a share of the words in the sample
and compared against how often those words appear in English generally. The
resulting ratios are log-transformed and standardized *within the text*, so any
across-the-board genre or tokenization offset cancels out and only the text's
relative profile drives the result. Each category is then weighted by the effect
size reported for it in the published research, and the weighted sum is the score.

The largest differences are in function words — pronouns, articles, prepositions —
not in what people write about. This is the central finding of the literature and
the reason the method works at all.

### Passage stability

Most tools in this space hand you one confident verdict. This one splits the text
into passages, scores each separately, and shows the range on the dial.

That turned out to matter. During development, two halves of the same text by the
same author scored 0.02 and 0.42 — one neutral, one strongly female. That is what
a small effect looks like on a short sample, and hiding it would make the tool
worse. **When the passages disagree, WriterGender reports no lean and says why.**

---

## Limits

Read these before drawing any conclusion from a result.

- **Every effect here is small.** In the source study, only five categories
  reached even the conventional threshold for a "small" effect. These are
  population averages. The authors write that predictions about individuals
  "should be made cautiously, if at all."
- **300 words minimum.** Below that the score swings on a handful of words. The
  app warns you, but the warning is easy to ignore.
- **Context moves the numbers more than you would expect.** The same method
  scored an average effect of 0.31 on fiction and 0.08 on emotional writing.
  Field, education, nationality, age, and intended audience all shift results.
  Formal and academic writing tends to read male regardless of who wrote it.
- **For AI-generated text, this measures the persona, not the model.** A model's
  style comes from its training and its prompt. If the lean changes when the
  prompt changes, the prompt is what you measured.
- **Known-bad inputs:** poetry, lyrics, lists, resumes, quoted or heavily edited
  text, and anything co-authored. Remove quoted passages before analyzing.
- **Binary categories.** The underlying research compared two groups, so the
  scale has two poles. That is a limit of the source data, not a claim about
  people.

### Not built for

This tool is not for identifying or unmasking anonymous writers. The design
reflects that: it reports a lean with a stated strength rather than a verdict, it
refuses short samples, it declines to call inconsistent texts, and it shows its
full reasoning so a result cannot be waved around as an authority. Those choices
are deliberate and should stay.

---

## Running it

It is one HTML file with no build step and no dependencies.

```
git clone https://github.com/jkh2/writergender.git
cd writergender
open index.html          # or just double-click it
```

### Deploying to GitHub Pages

1. Name the file `index.html` at the repository root.
2. Settings → Pages → Source: `Deploy from a branch` → `main` / `root`.
3. It will be live at `https://<username>.github.io/writergender/`.

The only external request the page makes is to Google Fonts. Everything else,
including the scoring engine and all word lists, is inline in the file.

---

## Method, sources, and credits

**Effect sizes and category weights** come from:

> Newman, M. L., Groom, C. J., Handelman, L. D., & Pennebaker, J. W. (2008).
> Gender Differences in Language Use: An Analysis of 14,000 Text Samples.
> *Discourse Processes, 45*(3), 211–236.
> https://doi.org/10.1080/01638530802073712

Table 1 of that paper reports means, standard deviations, and Cohen's *d* for
each language category across 14,324 text samples (8,353 by women, 5,971 by men).
Those effect sizes are the weights used here.

**The approach** follows:

> Koppel, M., Argamon, S., & Shimoni, A. R. (2003). Automatically Categorizing
> Written Texts by Author Gender. *Literary and Linguistic Computing, 17*(4), 101–108.

which first demonstrated that function-word usage could predict author gender.

**General-English baselines** were computed from the
[wordfreq](https://github.com/rspeer/wordfreq) package (Apache 2.0; bundled data
under CC BY-SA 4.0), which aggregates several corpora. Per its terms, credit is
due to the SUBTLEX authors — SUBTLEX remains freely available data — and to the
Google Books Ngram Viewer (http://books.google.com/ngrams), along with Wikipedia,
OpenSubtitles, and wordfreq's other Creative Commons sources.

**Word lists are an original compilation** written for this project and published
in full in the page source. No code, word list, or weighting table from any
existing gender-analysis tool is used here. In particular, Hacker Factor's Gender
Guesser is **not** the basis for this project; its terms prohibit redistribution
and reuse without written permission, and none was sought or needed.

---

## License

Open source under the [MIT License](LICENSE). This is not a commercial product
and is not for sale. Use it, fork it, improve it.

One note for anyone reusing the engine: the 18 baseline percentages were computed
from wordfreq's bundled data, which is CC BY-SA 4.0 — a license with a ShareAlike
clause. Aggregate statistics computed from a dataset are generally treated as
facts rather than derivative works, and the attributions above are given in full,
but this has not been reviewed by a lawyer and nothing here is legal advice. If
the question ever needs to go away entirely, those baselines can be recomputed
from a public-domain corpus; they are 18 numbers and nothing else in the project
depends on where they came from.

---

## Roadmap

- [x] **Phase 1 — Core analyzer.** Scoring engine, evidence table, passage
      stability, compare and test modes.
- [ ] **Phase 2 — Measured accuracy.** Collect labeled samples from at least 20
      writers per group, one sample per writer, consistent genre. Publish the
      real accuracy figure in the app.
- [ ] **Phase 3 — AI persona study.** Run one model under neutral and persona
      prompts to test whether measured lean follows the prompt. Write up the
      result either way.
- [ ] **Phase 4 — Public release.** Name, method page, optional plain-English
      explanation of results.

### Contributing samples

Phase 2 is the bottleneck, and it needs care more than volume:

- **One sample per writer.** Several pieces by the same person count as one
  sample — paste them together. Forty writers means forty people.
- **Keep the genre consistent** across the whole set. Mixed genres measure genre.
- **You must actually know the author.** Not inferred from a username or photo.
- **Strip quoted text, signatures, and anything edited by someone else.**
- **Label before you score.** Never run a sample through Analyze and then decide
  what to label it.

---

Built by [James Keith Harwood II](https://www.jameskeithharwood.com) with Claude.
