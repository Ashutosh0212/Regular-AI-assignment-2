AI Assignment - Does the statement follow?
Ashutosh Ravi Gupta, P25CS0002
Submitted: 31 October 2026

CONTENTS
  reasoning_agent.ipynb   complete implementation: parser, forward-chaining reasoner,
                          dataset loader, experiments, trace printing, submission writer
  report.pdf              representation, reasoning algorithms, experiments on the
                          public datasets, example reasoning traces
  submission.csv          predictions (columns: id, flag) with flag = True / False
  README.txt              this file

APPROACH (one paragraph)
  English sentences are converted to logic literals of the form
  (negated, predicate, args) and if-then rules by a regex-based parser.
  Entailment is decided by forward chaining with unification until a
  fixpoint, with negation as failure under a closed-world assumption
  (RuleTaker semantics). No learned or generative model is used anywhere
  in the pipeline; the final True/False comes only from the classical
  reasoning engine. Every derived fact keeps the rule and supporting facts
  that produced it, so a proof trace can be printed for any query.

REQUIREMENTS
  Python 3.9 or later
  pandas, datasets (Hugging Face)        pip install pandas datasets
  Standard library only otherwise (re, time, csv, os, random, collections).
  The notebook installs `datasets` itself if it is missing.
  Internet access is needed once, to download the public datasets for the
  experiments. The private-test prediction cell needs no internet.

HOW TO RUN
  1. Open reasoning_agent.ipynb (tested on Kaggle; also runs locally in Jupyter).
  2. Set TEST_PATH in the "submission.csv" cell to the private test file.
     Default: /kaggle/input/test.csv
     Accepted formats: .csv, .json, .jsonl, .parquet
  3. Run all cells top to bottom.
  4. submission.csv is written to the working directory with columns id, flag.
     Total inference time and (if a label column is present) accuracy are
     printed by the same cell.

  Column detection is automatic. The loader looks for
     context   in: context, theory, premises, premise, passage, story, kb, given_info
     statement in: statement, question, conclusion, hypothesis, query, claim
     id        in: id, ID, Id, idx, index   (row number is used if none is found)
  If the test file is missing, the cell falls back to writing predictions for
  the public dataset so the output format can still be checked. The included
  submission.csv was produced this way (3,403 public examples).

PUBLIC-DATASET RESULTS (from the notebook; details in report.pdf)
  Dataset        n      Accuracy   Time
  RuleTaker      2,000  100.00%    1.38 s
  FOLIO            203   61.58%    0.07 s
  CounterBench   1,200   50.42%    0.21 s
  in the Google form we were asked for the avg, hence i submitted (100 + 61.58 + 50.42)/3 = 70.67% 
  Overall        3,403   80.22%    1.66 s   (0.49 ms per query, Kaggle CPU) this is teh sample weighted overall.

NOTES AND KNOWN LIMITATIONS
  - If a query cannot be parsed, the system falls back to exact string
    matching against the context sentences. This is a lexical fallback,
    not reasoning.
  - Every indefinite ("someone", "something", "it") maps to one variable ?x,
    so rules relating two different quantified entities are not represented.
  - Negation is evaluated round by round rather than by stratification, so
    a conclusion drawn from an assumed negation can persist if the positive
    fact is derived later.
  - Disjunction, relative clauses, probabilistic statements and multi-word
    named entities are outside the current parser; this is why FOLIO and
    CounterBench scores are low.
  - The private test set was not used during development, and no predictions
    were created manually.
