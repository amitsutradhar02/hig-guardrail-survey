# HIG guardrail survey: data files

Supplementary data for our paper "The Human in the Guardrail: A Systematization of Decision Policies and Human Factors in LLM Security Filtering". Author details are removed while the paper is under review.

There are two Excel files:

- HIG_Main_Codebook.xlsx: the codebook, our coding of 25 guardrail systems, and the inter-rater data
- HIG_Runtime_Results.xlsx: the results from running 7 of these systems ourselves

Each file starts with a README tab. In every other tab, row 1 is the header and has filters, so you can sort or filter any column.

This is version 2.0. The submitted paper had 18 systems. In the revision we added 7 more and ran the runtime checks. We plan to add more systems in later versions.


## HIG_Main_Codebook.xlsx

### Codebook
The 23 dimensions we code each system on, grouped into 6 layers (A to F).
- Dimension ID: short code used in the other tabs (A1, A2 ... F3)
- Layer, Dimension name, Definition: what the dimension is about
- Allowed values: the only codes that can be given for that dimension
- Grounding literature: where the dimension comes from
- Core: "yes" for the 11 core dimensions used in the main findings

### Codes
One row per system: the final codes for all 25 systems.
- Type: commercial, open source or academic
- Added in: v1.0 for the 18 original systems, v2.0 for the 7 added in the revision
- Evidence URL: the documentation or paper we coded from
- A1 to F3: one column per dimension, using the values from the Codebook tab
- Coding note: a short note on what stood out for that system

How to read the codes: values like none, absent, silent, dead-end, uniform and n-a are real codes. They mean the documented design does not have that feature, so they are not missing data. "unknown" means the documentation does not say. This happened in 33 of 575 cells, and never in a core dimension.

### IRR pairs
The cells that two coders coded separately, without seeing each other's answers: 10 systems, 23 dimensions each, 230 cells in total.
- System, plus the dimension column: which cell it is
- Coder A, Coder B: each coder's original answer
- Agree: 1 if the two answers were the same, 0 if not
- Resolution note: for disagreements, how the two coders settled on the final value (the value now in the Codes tab)

### Kappa
Cohen's kappa, worked out from the original answers in the IRR pairs tab (before any disagreement was settled):
- core dimensions: 110 cells, agreement 0.90, kappa 0.88
- all dimensions: 230 cells, agreement 0.81, kappa 0.80

### Distribution
How many of the 25 systems got each value, for each dimension.


## HIG_Runtime_Results.xlsx

We ran Meta Prompt Guard 2, Meta Llama Guard 3, ShieldGemma, Granite Guardian, WildGuard, Qwen3Guard and the LLM Guard PromptInjection scanner. We did not run the commercial APIs.

Each system got:
- 44 prompts we wrote ourselves: benign, borderline, harmful, jailbreak, direct injection and indirect injection
- three probes, described under the Probes tab
- two public benchmarks: XSTest (450 prompts) and deepset/prompt-injections (662 prompts)

### Conformance
Does each system behave the way its documentation says? One row per system and dimension.
- dim: the dimension (see the Codebook tab in the other file)
- kind: "behavioural" means we judged it from what the system returned; "interface" means we checked what the interface gives back
- documented: our code from the documentation, fixed before we looked at any output
- observed: what we saw when running the system
- status:
  - match: the two agree
  - inconclusive: the test could not decide (2 cells)
  - resolved-unknown: we had coded "unknown" and the run settled it (7 cells, all deterministic)
- note: what the observation was based on

All 47 behavioural cells that could be decided matched, and all 35 interface cells matched.

### Probes
One row per system.
- rep_n, rep_distinct_raw, rep_score_range, rep_all_flagged: the same prompt was sent 10 times. These columns give how many different outputs came back, how much the score moved, and whether all 10 were flagged.
- canary_delta: the change in score for the same canary prompt scored before and after the other calls. 0 means nothing carried over between calls.
- any_numeric: whether the system returns a number at all
- raw_example_flagged: an example of the raw output for a flagged prompt
- mt_alone_score, mt_context_score, mt_alone_flag, mt_context_flag: a harmful turn scored on its own and again inside a longer conversation (empty for systems that do not take a conversation)

### Determinism
The 5 systems we ran in both sessions, compared prompt by prompt. "identical" is how many of the 44 prompts got the same verdict both times; it was all 44 for every system.

### Rates
How often each system flagged each prompt set.
- category: the prompt set. benign_xstest and benign_deepset are safe prompts, so flags there are false alarms. harmful_xstest and injection_deepset are attack prompts.
- n, flagged, rate: number of prompts, number flagged, and the share flagged
- ci95_low, ci95_high: 95% Wilson interval for the rate
- flag_rule: how the system's output was counted as flagged

For Qwen3Guard only "Unsafe" counts as flagged. Its "Controversial" label is given in separate rows, marked [Controversial band].

### Outputs
Every single classification, 8,324 rows.
- run: run1 was the first session (battery only); run2 was the second session (battery, probes and benchmarks)
- category, idx: which prompt set, and the position in that set
- prompt: the prompt text
- flagged, score, label: the verdict, the score (if the system gives one) and the label (if the system gives one)
- for WildGuard the score is not returned by the system. We read it from the model’s internal probability for ‘yes’ to check whether a hidden score exists. WildGuard itself only returns text, which is why it is coded A1 = binary.
- raw: the system's raw output, exactly as returned
- latency_s: time taken, in seconds
- source: HIG battery/probe, XSTest or deepset/prompt-injections

Line breaks inside a prompt or output are written as \n so that each classification stays on one row.

### Run details
The GPU, library versions, the exact model IDs and the benchmark sources. The runs were done on Kaggle (Tesla T4), using a threshold of 0.5 wherever a system gives a score.


## Benchmark data

The benchmark prompts in the Outputs tab come from XSTest (CC BY 4.0) and deepset/prompt-injections (Apache-2.0), and they keep their original licenses. All credit for those prompts goes to the dataset authors.
