# geo-ml-research skill

`geo-ml-research` is a Chinese-first Codex skill for geotechnical engineering and machine-learning research workflows. It routes research tasks across literature search, paper reading, experiment logging, figure planning, manuscript writing, citation support, pre-submission review, and rebuttal preparation.

This skill is designed as a workflow guide for research agents. It does not contain datasets, model checkpoints, training outputs, manuscript drafts, private notes, or paper-specific intermediate artifacts.

## What it is for

- Literature discovery and source-tracked reading for geotechnical ML topics.
- Claim-evidence-boundary mapping before manuscript writing.
- Experiment/HPO/result ledger discipline for ML papers.
- Figure decision support for prediction, residual, ablation, HPO, and engineering-error plots.
- Citation support grading and reference metadata hygiene.
- Submission-risk review and reviewer-response planning.

The skill is domain-oriented, but not tied to a single paper, model, dataset, or salt-cavern case. Project-specific terms inside the skill are treated as pressure-test examples only.

## Repository layout

```text
geo-ml-research-skill/
  README.md
  LICENSE
  .gitignore
  geo-ml-research/
    SKILL.md
    agents/
      openai.yaml
    references/
      citation-support-scale.md
      domain-figure-writing-style.md
      experiment-ledger.md
      figure-decision-tree.md
      literature-workflow.md
      module-routing.md
      paper-reading-card.md
      rebuttal-playbook.md
      review-audit-rubric.md
      writing-claim-evidence.md
```

## Installation

Copy the `geo-ml-research` folder into your local skills directory, then restart Codex.

Common locations:

```powershell
~\.codex\skills\geo-ml-research
```

or:

```powershell
~\.agents\skills\geo-ml-research
```

After restart, trigger it with research tasks such as:

```text
Use geo-ml-research to plan a literature search for geotechnical machine learning.
Use geo-ml-research to audit the claim-evidence chain in this Results draft.
Use geo-ml-research to prepare a rebuttal plan from these reviewer comments.
```

## Design principles

- Evidence before prose: do not write strong claims without data, figures, code results, or literature support.
- Explicit engineering boundaries: define object, stage, geometry, unit, data source, and validation scope.
- Auditable ML results: distinguish baseline, tuned, selected, split strategy, HPO, seeds, and test-set use.
- Figure-first reasoning: each figure should serve a claim, not just display a metric.
- Citation discipline: support sentence-level claims, avoid title-only relevance, and mark missing sources.
- Confirmation gates: do not infer author declarations, target-venue requirements, release policy, or validation routes.

## Design inspirations and attribution

This repository is a personal, rewritten workflow skill. It was informed by a review of public research-skill projects and local research-agent patterns, but it does not redistribute upstream source code, scripts, long-form templates, datasets, or manuscript text.

The main external design inspirations were:

| Source | What was abstracted |
| --- | --- |
| [`Yuan1z0825/nature-skills`](https://github.com/Yuan1z0825/nature-skills) | Modular research skills for writing, figures, citations, reading, data, and responses. |
| [`Imbad0202/academic-research-skills`](https://github.com/Imbad0202/academic-research-skills) | End-to-end academic workflow structure, quality gates, provenance thinking, and claim auditing. |
| [`O0000-code/paper-search-pro`](https://github.com/O0000-code/paper-search-pro) | Multi-source literature discovery, search-depth tiers, metadata outputs, and reproducible search logs. |
| [`Haojae/scipilot-figure-skill`](https://github.com/Haojae/scipilot-figure-skill) | Scientific figure planning, chart-type selection, visual QA, and publication-oriented figure discipline. |
| [`SNL-UCSB/paper-writing-skill`](https://github.com/SNL-UCSB/paper-writing-skill) | Structured paper-writing flow from ideas to sections, with emphasis on claims and section moves. |
| [`M1n-n9/paper-lifecycle`](https://github.com/M1n-n9/paper-lifecycle) | Review/revision/rebuttal lifecycle thinking and reviewer-style risk diagnosis. |
| [`kesslerio/academic-deep-research-clawhub-skill`](https://github.com/kesslerio/academic-deep-research-clawhub-skill) | Deep research framing, evidence grading, and review-oriented synthesis patterns. |

Some module names and ideas also reflect commonly used research-agent patterns such as `nature-reader`, `nature-writing`, `nature-figure`, `nature-citation`, `nature-response`, `academic-paper-reviewer`, and `academic-pipeline`. These are referenced as conceptual workflow categories, not as redistributed content.

### Reuse boundary

- This repository contains original, personal workflow text written for `geo-ml-research`.
- It does not copy upstream files, scripts, prompts, templates, datasets, or long passages.
- If you reuse material from any upstream project directly, check that project's license separately.
- If an upstream project has no license or a non-commercial license, treat it as inspiration only unless you obtain permission.

## License

This repository is released under the MIT License. See [`LICENSE`](LICENSE).

