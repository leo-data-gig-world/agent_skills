# Agent Skills

LLM agents that classify text **and notice when a new category appears**, plus a few
reusable skills for coding agents.

Most text classifiers only know the labels they were trained on. When something new shows
up, they force it into an old box or call it "other". Here the classifier can say
*"this doesn't fit, it looks like X"*, and a second agent (the **Sentinel**) counts those
suggestions. Once a new topic has been suggested enough times, it is promoted into the
official list. The taxonomy grows with the data, without retraining.

The same pattern works for support tickets, compliance cases or customer feedback,
anywhere new issue types appear over time.

## How it works

```
text ──► Classifier agent ──► known topic + sentiment + confidence
                │
                └─ no good fit ──► suggests a new topic name
                                        │
                                        ▼
                               Sentinel agent counts suggestions
                               ≥ threshold ──► topic promoted to the master list
```

## Skills

| Skill | What it does |
|---|---|
| [`classifier`](skills/classifier) | Assigns a known topic, sentiment and confidence; proposes a new topic when nothing fits. Output is validated with Pydantic. |
| [`sentinel`](skills/sentinel) | Tracks proposed topics, promotes them past a threshold, logs sample texts for review. |
| [`llm_wrapper`](skills/llm_wrapper) | One interface over Ollama, OpenAI, Anthropic and Google, with retries, backoff and JSON mode. |
| [`update-docs`](skills/update-docs) | Pre-commit hook that writes `CHANGELOG.md` from commit messages. |
| [`git-dev-workflow`](skills/git-dev-workflow) | Agent skill: branch → test → docs → PR → squash merge, every time. |
| [`problem-solver`](skills/problem-solver) | Agent skill: search a local knowledge base before debugging, and log every fix. |

## Run it

```bash
pip install -r requirements.txt
cp .env.example .env          # pick a provider; Ollama runs locally for free
python scripts/run.py --generate          # synthetic movie reviews
python scripts/run.py -i data/reviews.csv -c review_text --limit 20
```

The demo data is movie reviews, so you can see topics like *Pacing Issues* get discovered
and promoted.

## More

[GUIDE.md](GUIDE.md) explains the design in depth: architecture, prompts, thresholds and
trade-offs.
