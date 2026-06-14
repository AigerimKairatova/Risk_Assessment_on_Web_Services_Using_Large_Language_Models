Risk Assessment on Web Services Using Large Language Models

Supplementary materials for the master's thesis *Risk Assessment on Web Services Using Large Language Models* (University of Gdańsk, Business Informatics).

This repository contains the data, code, prompts, and computed results supporting the empirical study. The work evaluates how four large language models identify GDPR compliance risk indicators in cookie banners and privacy policies, and how consistently and reliably they do so.

## Study at a glance

- **Sample:** 20 European e-commerce websites (manually collected privacy policies and cookie-banner texts; banner screenshots for the visual indicator).
- **Models:** GPT-4o, Claude Sonnet, Gemini 2.5 Flash, and Llama 3.3 70B, all run at `temperature = 0` for reproducibility.
- **Framework:** 18 GDPR risk indicators (R01–R18), grouped into Validity of Consent, Transparency, and Accountability & Technical Signals. Each indicator carries a severity weight (1–3).
- **Research questions:**
  - **RQ1** — cross-model agreement per indicator (Fleiss' Kappa, pairwise agreement, indicator-level divergence).
  - **RQ2** — inter-model divergence in compliance conclusions (unweighted and severity-weighted risk scores, risk-tier classification).
  - **RQ3** — output reliability (stability across repeated runs, hallucination rate via evidence-quote verification, human–LLM agreement).

## Methodology pipeline

The analysis follows the three-stage workflow described in Chapter 2.6 of the thesis:

1. **Stage 1 — Automated assessment (Python).** Each site's policy is chunked, sent to all four models with a single integrated prompt returning all 18 indicators as JSON, and aggregated per site. Outputs include per-indicator score, evidence quote, and interpretation, with automatic structure validation and evidence verification against the source text.
2. **Stage 2 — Human validation (Excel).** Each model classification is manually reviewed and labelled (Verified, Misclassification, Hallucination, Absent Evidence) with an optional human score.
3. **Stage 3 — Metric computation (Python).** The human-validated workbook is read back in to compute agreement, divergence, hallucination, stability, and trustworthiness metrics.

The reliability analysis uses a main run (20 sites) plus two repeat runs on a 10-site subset, enabling stability checks across three runs.

### Indicator R17 (visual analysis)

R17 (*equal prominence of choices*) cannot be judged from text alone. In the text pipeline it is always scored 0 with the placeholder `VISUAL_INDICATOR_NOT_ASSESSED_IN_TEXT_MODE`. Its real value comes from a separate vision prompt run on banner screenshots, which then replaces the placeholder in the final results. Llama is excluded from R17 metrics (no vision run).

## Repository structure

```
input/
  data.zip                                            Source texts: per-site privacy.txt + banner.txt
notebooks/
  Appendix B - Python Pipeline.ipynb                  Full analysis pipeline (Stages 1 and 3)
prompts/
  Appendix A - Main_Prompt.md                         Integrated 18-indicator text prompt
  Appendix F - Visual_Prompt.md                       R17 visual (screenshot) prompt
outputs/
  Appendix C with_human_validation_results_main_all.xlsx   Main results with human validation
  output.zip                                          Raw model outputs (main run)
  repeat.zip                                          Raw model outputs (repeat runs)
  visual.zip                                          Visual (R17) run outputs
metrics/
  Appendix D python_metrics_main_with_human_validation.xlsx   Agreement, divergence, hallucination, trustworthiness
  Appendix E repeat_stability_with_visual_r17.xlsx            Stability across three runs
  Appendix G chapter3_tables_calculated.xlsx                  Calculated tables used in Chapter 3
README.md
```

## Reproducing the results

The notebook is written for Google Colab. To reproduce from scratch:

1. Provide API keys for the four model providers (OpenAI, Anthropic, Google, Together) via Colab Secrets.
2. Upload `input/data.zip` and run the pipeline blocks in order. Runs are organised per model and per batch of 5 sites to control cost and allow checkpointing.
3. For the visual indicator, run the vision blocks on banner screenshots to produce the R17 results.

To reproduce only the metrics from the already-generated outputs (without re-calling any model), upload the human-validated results file and run the metric-computation blocks. This path is documented in the notebook and does not require API keys.

Prompt version used for the main run: `text_prompt_18_indicators_2026_05_10`.

## Notes

- All model outputs in this repository were generated at `temperature = 0`; minor run-to-run variation can still occur, which is precisely what the stability analysis measures.
- Evidence quotes are verified verbatim against the source text; quotes that cannot be matched are flagged as potential hallucinations before human review.
- Site texts were collected manually to ensure consistency; the sample is scoped to the EU regulatory context and is not a random sample (see thesis Chapter 4.3 for limitations).
  
