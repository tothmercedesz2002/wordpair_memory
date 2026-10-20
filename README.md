# Word-pair learning task

A browser-based Hungarian word-pair learning task with cued recall and confidence ratings, implemented using jsPsych.

**Current task version:** `WPM_2026-10-20_v1`

The task is a research prototype, not a standardized or validated memory test.

## Task procedure

### Practice

Participants first study two practice pairs and then complete recall and confidence ratings for those pairs. Practice words do not appear in the main-task stimulus pool.

### Learning phase

By default, participants study **12 word pairs**, presented sequentially for **5 seconds per pair**.
Participants are instructed to remember which second word belongs to each first word. No response is required during learning.

### Recall and confidence

After the final learning pair, the main recall test begins automatically. The first word of each studied pair is presented as a cue. Participants type the corresponding second word or select **“I don't remember”**.

After each typed answer, participants rate their confidence in its correctness on a scale from **0 to 100**:

- **0:** Not at all confident.
- **100:** Completely confident.

The confidence question is skipped after an “I don't remember” response. These responses are recorded separately and are not assigned a confidence rating of zero.
No correct-answer feedback is provided during the recall test.

### Scoring

Typed responses are compared with the target word after Unicode normalization, removal of surrounding whitespace, and conversion to lowercase. Accents remain meaningful, and spelling mistakes are notautomatically corrected.
An “I don't remember” response is recorded as unsuccessful recall.

## Word-pair materials and provenance

The current main-task pool contains **50 fixed pairs comprising 100 distinct Hungarian words**. Each word appears in exactly one pair, and none overlaps with the practice words.

The candidate words were assembled specifically for this project, combining words from an earlier task version, AI suggestions, and researcher additions. They were not sampled from a standardized word list or independently normed.

**The current pool is not a subset of the SPAML materials.** Earlier prototypes explored those materials, but the present pairings were generated from the project's own candidate-word list using the procedure below.

### Word-vector source

Pairwise cosine similarities were calculated using the pretrained **Hungarian fastText Common Crawl and Wikipedia word vectors**:

- **Model file:** `cc.hu.300.vec.gz`
- **Vector dimensions:** `300`
- **Documentation:** [Word vectors for 157 languages](https://fasttext.cc/docs/en/crawl-vectors.html)
- **Model download:** [Hungarian text vectors](https://dl.fbaipublicfiles.com/fasttext/vectors-crawl/cc.hu.300.vec.gz)
- **Vector license:** [CC BY-SA 3.0](https://creativecommons.org/licenses/by-sa/3.0/)

The pretrained vectors provide numerical representations of words, not a ready-made list of word pairs for this memory task. The cosine values used here were computed from these vectors; they were not taken from SPAML's `hu_cosine` column.

**Model reference:**

Grave, E., Bojanowski, P., Gupta, P., Joulin, A., & Mikolov, T. (2018).
[Learning Word Vectors for 157 Languages](https://aclanthology.org/L18-1550/).
In *Proceedings of the Eleventh International Conference on Language
Resources and Evaluation (LREC 2018)*.

### Selection of the 50-pair pool

The selection is implemented in `cosine_counter/cosine_counter.qmd`.

1. **Calculate all combinations.** Cosine similarity is calculated for all **4,950 unordered combinations** of the 100 words. Self-pairs and reversed duplicates are excluded.
2. **Apply the cosine limits.** Only pairs with cosine similarity **greater than or equal to 0.15 and less than or equal to 0.30** are eligible. These are provisional selection limits, not validated thresholds for recall difficulty.
3. **Assign random priorities.** Each eligible pair receives a random number between 0 and 1. These numbers are selection priorities, not measures of pair quality.
4. **Select a complete pairing.** Binary optimization using the R package [`lpSolve`](https://search.r-project.org/CRAN/refmans/lpSolve/html/lp.html) selects the set with the highest total random priority, subject to the requirement that every word appears exactly once. This produces **50 non-overlapping pairs**.
5. **Assign cue and target roles.** Each selected pair has a 50% chance of having its word order reversed. This assignment is made once when constructing the pool, not separately for each participant.

The selection uses the fixed seed **`20261020`** and the **Mersenne–Twister** random-number generator. With the same inputs, candidate order, and software environment, the procedure reproduces the same pairings and cue–target directions.

This is **not uniform random sampling from all possible complete pairings**. Within the eligible cosine interval, the optimization does not prefer higher or lower cosine values; it maximizes the random priorities while satisfying the no-repeated-word requirement.

The final assignments are stored in `selected_pairs.csv` and copied into `mainPairPool` in `index.html`. The CSV's `pair_id` values are preserved as the JavaScript objects' `id` values.

### Reproducing the selection

Download the model file, set `model_file` in `cosine_counter/cosine_counter.qmd` to its local location, and render the UTF-8 Quarto document in an R environment with `lpSolve` and `knitr` available.

Each run creates a new results folder containing:

- `all_pair_cosines.csv`: all unordered combinations and their cosine values.
- `candidate_pairs.csv`: combinations within the chosen cosine interval.
- `selected_pairs.csv`: final pair IDs, cues, targets, cosine values, and seed.

Keep the selection code and the corresponding output together. Changing the words, selection limits, or seed can change the resulting pool. Changing or rerunning the Quarto analysis does not automatically update the experiment's embedded word list.

### Disclosure of AI assistance

An OpenAI AI assistant accessed through **Codex** assisted with proposing candidate words, developing the selection and task code, and documenting the procedure. The researcher also contributed and revised candidate words.
The final pair assignments were produced by the R optimization code described above, rather than selected manually by the language model. Cosine values were calculated from the pretrained fastText vectors, not estimated or invented by the assistant.
AI assistance does not establish the validity of the task. Responsibility for final stimulus approval and methodological decisions remains with the research team.

**AI tool reference:**

OpenAI. (n.d.). *Codex* [AI assistant].
[https://openai.com/codex/](https://openai.com/codex/)

### Limitations

The candidate words were not quantitatively matched or independently rated for word frequency, length, familiarity, emotional valence or arousal, concreteness, or recall difficulty.
Cosine similarity is a model-dependent measure of vector alignment. It is not a direct measure of human association strength or memory performance. Restricting values to a range does not make the selected pairs equally difficult or constitute formal matching on other lexical properties.
Because the procedure uses all 100 candidate words, it does not remove less suitable words from that list. The final pairs still require content review and empirical pilot testing.
Randomly sampling 12 pairs for each participant also does not guarantee equal difficulty across participant-specific lists. Item-level performance and the task's measurement properties require evaluation.

## Participant-level sampling and settings

The fixed pool contains **50 word pairs**. At the beginning of each task run, the program randomly selects **12 pairs without replacement**.

Learning order and recall order are randomized separately, but both phases use the **same selected pairs**. Words are not swapped between pairs, and cue–target direction remains fixed.

The R seed used to construct the pool does **not** fix participant-level sampling or presentation order in jsPsych.

The task parameters can be changed in the `SETTINGS` object in `index.html`:

| Setting | Description | Default |
| --- | --- | --- |
| `mainPairCount` | Number of main-task pairs selected from the pool | `12` |
| `pairDisplayMs` | Learning presentation duration per pair, in milliseconds | `5000` |

`mainPairCount` must be an integer between `1` and `50` for the current pool. The two practice pairs are separate and are not included in this count.

## Files

- `index.html`: task interface, fixed stimulus pool, experimental procedure, scoring, and data-saving functions.
- `cosine_counter/cosine_counter.qmd`: candidate words, word-vector loading, cosine calculation, pair selection, and selection-result export.

The experiment runs from the embedded list in `index.html`; participants do not need R, the fastText model file, or the selection CSV files.

## Online demo

**[Try the task](https://tothmercedesz2002.github.io/wordpair_memory/)**

A desktop or laptop computer with a physical keyboard is required.

### Data saving

In the GitHub Pages demo, results are downloaded locally as a CSV file at the end of the task. The demo does not upload the collected response data to a research server. 
When the task is run through JATOS, results are submitted to the configured JATOS server instead. If saving fails, the interface offers a retry and a backup CSV download.

The exported data include the experiment version, selected-pair count, pool size, presentation duration, pair IDs, cues, targets, recall responses, correctness, and confidence ratings.