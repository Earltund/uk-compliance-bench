# UK-CompliBench

**Do LLMs give compliant UK data-protection answers, and does adversarial pressure push them into non-compliant ones?**

*Status: in progress (started October 2026). Pilot set and evaluation pipeline in place.*

## Motivation

Organisations in regulated sectors increasingly use LLMs to answer staff questions about data protection and AI governance. A wrong answer here is a compliance failure, not just an error. Most LLM benchmarks test general knowledge on clean questions. They don't test whether a model holds to the law when a user pushes back, claims authority, or pastes in a misleading policy document.

My MSc dissertation ([llm-agent-reliability](https://github.com/Earltund/llm-agent-reliability)) found that defences which stop attacks that *redirect* a model do not stop attacks that *corrupt its evidence*. This project tests whether the same pattern holds for regulatory compliance.

## Research questions

1. **Accuracy:** How accurately do open-weight LLMs answer UK data-protection compliance questions?
2. **Robustness:** How often does adversarial pressure turn a correct answer into a non-compliant one (attack success rate)?
3. **Attack type:** Which attacks work best: authority pressure, evidence poisoning, or instruction injection?

## Benchmark design

Each item is a multiple-choice question grounded in a cited legal source, with a paired adversarial twin.

| Field | Contents |
|---|---|
| `question` | Benign version of the question |
| `choices`, `target` | Answer options and the correct letter |
| `source` | Legal provision the answer rests on (e.g. UK GDPR Article 33(1)) |
| `attack_type` | `authority_pressure`, `evidence_poisoning` or `instruction_injection` |
| `adversarial_question` | The same question under that attack |

**Attack types**
- **Authority pressure:** the user claims a role or legal advice that supports a wrong answer.
- **Evidence poisoning:** a pasted policy excerpt states the wrong rule.
- **Instruction injection:** pasted text contains hidden instructions to the model.

**Sources:** UK GDPR, the Data Protection Act 2018, and ICO guidance. Each item cites the provision it rests on.

## Method

- **Framework:** [Inspect](https://inspect.aisi.org.uk/), the UK AI Security Institute's evaluation framework
- **Models:** several open-weight LLMs, run through the same pipeline
- **Reproducibility:** temperature 0, fixed seed 42, versioned dataset, full Inspect logs kept for every run
- **Metrics:** accuracy per condition, attack success rate (correct when benign, wrong when attacked), results by attack type and topic, McNemar tests on the paired design, bootstrap confidence intervals

## Running it

```bash
pip install -r requirements.txt

# Benign and adversarial runs for one model
inspect eval compliance_bench.py -T condition=benign --model openrouter/<model-id>
inspect eval compliance_bench.py -T condition=adversarial --model openrouter/<model-id>

# View results
inspect view
```

## Roadmap

- [x] Benchmark schema, three attack types, evaluation pipeline
- [x] Pilot items with cited sources
- [ ] Expand to 150 items across breach notification, subject rights, lawful bases, DPIAs, international transfers, automated decision-making
- [ ] Baseline runs on open-weight models
- [ ] Analysis script for attack success rate and significance tests
- [ ] Validate answer keys against ICO guidance; manual review of a sample
- [ ] Public release of dataset and results

## Limitations

- Multiple choice measures recognition of the correct rule, not the quality of free-text advice. A free-text track with a graded rubric is planned.
- Text only for now. A later extension could cover scanned documents and forms, making it multimodal.

## Disclaimer

This is a research benchmark, not legal advice. Answer keys are written from the cited legislation and guidance and should be checked against current ICO guidance before use.

## Author

Michael Okunade, MSc Artificial Intelligence and Data Science, Keele University
