# Word-pair learning task

A browser-based Hungarian word-pair learning task with cued recall and
confidence ratings, implemented using jsPsych.

The task is a research prototype and requires pilot testing before use
as a validated measure.

## Task procedure

### Practice

Participants first complete two practice pairs to become familiar with
the learning, recall, and confidence-rating stages.

Practice words do not appear in the main-task stimulus pool.

### Learning phase

By default, participants study **12 word pairs**, presented sequentially
for **5 seconds per pair**.

Participants are instructed to remember which second word belongs to
each first word. No response is required during learning.

### Recall and confidence

After learning, the first word of each studied pair is presented as a
cue. Participants type the corresponding second word or select
**“I don't remember”**.

After each typed answer, participants rate their confidence in its
correctness on a scale from **0 to 100**:

- **0:** Not at all confident.
- **100:** Completely confident.

The confidence question is skipped after an “I don't remember” response.
These responses are recorded separately and are not assigned a confidence
rating of zero.

No correct-answer feedback is provided during the recall test.

## Word-pair materials and provenance

The main-task stimuli were selected from the Hungarian materials of the
**Semantic Priming Across Many Languages (SPAML)** project, conducted in
partnership with the Psychological Science Accelerator.

### Original source

- **Repository:** [SemanticPriming/SPAML](https://github.com/SemanticPriming/SPAML)
- **Release:** `v1.0.2`
- **File:** [`04_Procedure/hu/hu_words.csv`](https://github.com/SemanticPriming/SPAML/blob/v1.0.2/04_Procedure/hu/hu_words.csv)

The source file contains Hungarian cue–target pairs, condition labels,
and a `hu_cosine` semantic-similarity measure.

These materials were originally developed for semantic priming research,
not as a standardized cued-recall memory test.

**Associated publication:**

Buchanan, E. M., et al. (2026). Measuring the semantic priming effect
across many languages. *Nature Human Behaviour, 10*, 182–201.
[https://doi.org/10.1038/s41562-025-02254-x](https://doi.org/10.1038/s41562-025-02254-x)

The article was first published online on 24 September 2025.

### Selection of the 40-pair pool

The 40-pair pool was proposed by an OpenAI AI assistant accessed through
**Codex**, using **GPT-6 Astra**.

Selection was **judgment-based**, rather than a random sample from the
complete SPAML materials or a formally optimized matching procedure.

The intended selection priorities were:

- Single-word Hungarian noun pairs that appeared familiar and interpretable.
- Semantically related pairs suitable for an associative learning task.
- Preservation of the original cue–target pairings and their direction.
- No repeated words within the main-task pool.
- No overlap with the practice words.

All 40 selected pairs were checked against the source materials and occur
with the following labels:

- `type = related`
- `cue_type = word`
- `target_type = word`

The pool contains **80 distinct words**. The AI did not invent the
main-task words or recombine them into new pairings.

The exact selected pairs and their stable identifiers are recorded in
`mainPairPool` in `index.html`.

The selected pairs have `hu_cosine` values ranging from approximately
**0.281 to 0.568**. This range describes the selected pool; it should not
be interpreted as evidence of formal matching or stratified selection.

### Disclosure of AI assistance

AI was used to propose the 40-pair subset and assist with checking source
membership and documenting the selection process.

The original stimulus materials remain attributable to the SPAML project.
AI assistance does not establish the validity of the adapted memory task.
Responsibility for final stimulus approval and methodological decisions
remains with the research team.

**AI tool reference:**

OpenAI. (n.d.). *Codex* [AI assistant].
[https://openai.com/codex/](https://openai.com/codex/)

### Limitations

Familiarity and suitability were assessed through qualitative AI judgments,
not independent participant ratings.

The selected pool was **not quantitatively matched** for:

- Word frequency.
- Word length.
- Emotional valence or arousal.
- Concreteness.
- Recall difficulty.

Although the source provides semantic-similarity values, the current
selection should not be described as a formally cosine-matched or
stratified sample. Semantic similarity is not itself a measure of
cued-recall difficulty.

This 40-pair subset is a **project-specific pilot selection**, not a
separately validated stimulus set supplied by the SPAML authors.
Its suitability, difficulty, and measurement properties in this memory
task require empirical evaluation.

## Participant-level sampling and settings

The fixed pool contains **40 word pairs**. At the beginning of each task
run, the program randomly selects **12 pairs without replacement**.

Learning order and recall order are randomized separately, but both
phases use the **same selected pairs**. Words are not swapped between
pairs.

The task parameters can be changed in the `SETTINGS` object in `index.html`:

| Setting | Description | Default |
| --- | --- | --- |
| `mainPairCount` | Number of main-task pairs selected from the pool | `12` |
| `pairDisplayMs` | Learning presentation duration per pair, in milliseconds | `5000` |

`mainPairCount` must be an integer between `1` and `40` for the current
pool. The two practice pairs are separate and are not included in this
count.

## Files

`index.html` contains the task interface, stimulus pool, experimental
procedure, scoring logic, and data-saving functions.

## Online demo

**[Try the task](https://tothmercedesz2002.github.io/wordpair_memory/)**

A desktop or laptop computer with a physical keyboard is required.

### Data saving

In the GitHub Pages demo, results are downloaded locally as a CSV file
at the end of the task. The demo does not upload the collected response
data to a research server.

When the task is run through JATOS, results are submitted to the
configured JATOS server instead.

> This is a research prototype, not a standardized or validated memory test.