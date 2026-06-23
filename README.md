# -A-Study-of-the-Capabilities-of-LLMs-in-Answering-Russian-Trivia-Game-
This repository presents a systematic investigation into the reasoning and knowledge-retrieval capabilities of open-source Large Language Models (LLMs) when faced with complex, non-factual questions from the renowned Russian intellectual game **"Что? Где? Когда?"** (translated as *"What? Where? When?"*).
Unlike standard factoid QA benchmarks, ChGK questions are uniquely challenging for modern LLMs. They often require solving intricate logical puzzles, deciphering wordplay, connecting disparate cultural references, and performing deductive reasoning rather than simply retrieving memorized facts. 

To tackle this complexity, we do not rely on a single model. Instead, we compare **six distinct ensemble inference strategies**—ranging from static majority voting to interactive multi-agent debates and adaptive captain-based oversight. The core hypothesis is that **"discussion" and "coordination" among multiple models** can mimic the collective intelligence of a human expert team, leading to more accurate and robust final answers.

All experiments are run locally via **Ollama** with a fixed temperature of `0.2` to minimize randomness, ensuring that performance differences are driven purely by the ensemble strategy rather than stochastic generation noise.

## ⚙️ Experimental Setup

- **Domain**: Questions sourced from the [Parrot Benchmark](https://www.redblock.ai/resources/blog/introducing-parrot-a-new-llm-benchmark-using-television-trivia-datasets).
- **Framework**: Local inference via [Ollama](https://ollama.com/).
- **Model Pool**: A consistent set of 6 open-source language models:
  - `qwen2:7b`
  - `mistral-nemo:12b`
  - `gemma2:9b` (designated as the exclusive **"Captain"** in relevant strategies)
  - `llama3.1:8b`
  - `deepseek-r1:8b`
  - `phi3:3.8b`
- **Inference Temperature**: Fixed at `0.2` for all model calls to ensure deterministic and reproducible outputs.
- **Evaluation Metrics**: We evaluate performance using a combination of:
  - **Exact-match accuracy** against the official answer (strict string normalization).
  - **Semantic similarity** (Rouge / BERTScore) to capture paraphrased but correct answers.
  - **LLM-as-Judge** for qualitative assessment of reasoning quality when human evaluation is required.

## 🧪 The Six Experimental Strategies 

In the spirit of the game, where a team of experts discusses a question before the captain gives the final answer, our strategies explore how AI "teams" can collaborate.

| # | Strategy Name | ChGK-Inspired Description |
| :---: | :--- | :--- |
| 1 | **Baseline (Individual Models)** | Each model acts as a solo "expert" answering the question independently. This establishes a performance floor and highlights which individual model performs best on trivia and logic. |
| 2 | **Majority Voting Ensemble** | Six independent "experts" submit their answers. The final response is determined by the most common normalized answer. This simulates a static, non-interactive team consensus. |
| 3 | **Captain-Based Selection** | Five "experts" generate independent answers. `gemma2:9b` takes on the role of the **Team Captain**. Instead of answering itself, it reviews all five responses and selects the one with the most logical reasoning, acting as a human captain would when choosing among team suggestions. |
| 4 | **Debate Ensemble (Roundtable Discussion)** | A two-round "roundtable." In Round 1, all six models answer independently. They are then shown each other's reasoning and allowed to revise their answers in Round 2. The final prediction is determined by majority voting on the revised responses. This tests if peer review improves collective reasoning. |
| 5 | **Debate with Captain (Captain's Final Verdict)** | This mirrors the real ChGK rules. Five "experts" debate in two rounds (as in Strategy 4). After the debate, `gemma2:9b` acts as the Captain, reviews the debated reasoning, and delivers the **final verdict**. The Captain may either select an existing answer or synthesize a completely new final response. |
| 6 | **Adaptive Hybrid System (Efficient Intelligence)** | This system mimics a human team's time management. First, 5 experts generate answers. The system calculates **disagreement metrics** (Agreement Rate and Shannon Entropy). If consensus is high (e.g., 4/5 agree), the system skips the debate and immediately outputs the majority answer (saving compute). If disagreement is high, a debate round is triggered. If consensus is *still* not reached after the debate, the Captain (`gemma2:9b`) steps in to make the final call. This balances computational cost with the depth of reasoning required for particularly tricky ChGK questions. |

## 📂 Repository Structure
.
├── notebooks/
│   ├── 01_baseline_individual_models.ipynb
│   ├── 02_majority_voting_ensemble.ipynb
│   ├── 03_captain_based_selection.ipynb
│   ├── 04_debate_ensemble_roundtable.ipynb
│   ├── 05_debate_with_captain_verdict.ipynb
│   └── 06_adaptive_hybrid_system.ipynb
├── data/
│   └── questions_with_answers.csv
├── results/
│   ├── metrics/
│   └── figures/
├── requirements.txt
└── README.md
