---
language:
- en
license: mit
size_categories:
- 100K<n<1M
task_categories:
- question-answering
- text-generation
pretty_name: MATH Rollouts with DeepSeek R1-Distill LLMs
tags:
- reasoning
- llm
- rollouts
- chain-of-thought
- math
library_name: datasets
configs:
- config_name: default
  data_files:
  - split: default
    path: data/default-*
dataset_info:
  features:
  - name: path
    dtype: string
  - name: filename
    dtype: string
  - name: extension
    dtype: string
  - name: size_bytes
    dtype: int64
  - name: content
    dtype: string
  splits:
  - name: default
    num_bytes: 91382189873
    num_examples: 28845
  download_size: 19607697104
  dataset_size: 91382189873
---

# Mathematical Reasoning Rollouts Dataset

<!-- Provide a quick summary of the dataset. -->

This dataset contains step-by-step reasoning rollouts generated with DeepSeek R1-Distill language models solving mathematical problems from the [MATH dataset](https://huggingface.co/datasets/hendrycks/competition_math).
The dataset is designed for analyzing reasoning patterns, branching factors, planning strategies, and the effectiveness of different reasoning approaches in mathematical problem-solving.

## Dataset Details

### Dataset Description

<!-- Provide a longer summary of what this dataset is. -->

- **Curated by:** Uzay Macar, Paul C. Bogdan
- **Language(s) (NLP):** English
- **License:** MIT

### Dataset Sources

<!-- Provide the basic links for the dataset. -->

- **Repository:** https://github.com/interp-reasoning/principled-attribution
- **Paper:** https://huggingface.co/papers/2506.19143, https://www.arxiv.org/abs/2506.19143
- **Demo:** https://www.thought-anchors.com

### Dataset Structure

```
math_rollouts/
├── deepseek-r1-distill-llama-8b/
  └── temperature_0.6_top_p_0.95/
    ├── correct_base_solution/
    ├── incorrect_base_solution/
    ├── correct_base_solution_forced_answer/
    ├── incorrect_base_solution_forced_answer/
    └── correct_base_solution_sanity/
├── deepseek-r1-distill-qwen-14b/
  └── temperature_0.6_top_p_0.95/
    ├── correct_base_solution/
    ├── incorrect_base_solution/
    ├── correct_base_solution_forced_answer/
    ├── incorrect_base_solution_forced_answer/
    └── correct_base_solution_sanity/
```

### Models Used

| Model | Size | Architecture | Description |
|-------|------|-------------|-------------|
| `deepseek-r1-distill-llama-8b` | 8B | LLaMA-based | DeepSeek R1 distilled into LLaMA 8B model |
| `deepseek-r1-distill-qwen-14b` | 14B | Qwen-based | DeepSeek R1 distilled into Qwen 14B model |

### Generation Parameters

Currently available configuration:
- **Temperature**: 0.6
- **Top-p**: 0.95 (nucleus sampling)

### Solution Types

| Type | Description |
|------|-------------|
| `correct_base_solution` | Problems where the model reached the correct answer through reasoning |
| `incorrect_base_solution` | Problems where the model reached an incorrect answer through reasoning |
| `correct_base_solution_forced_answer` | Problems with correct answers where the final answer was forced |
| `incorrect_base_solution_forced_answer` | Problems with incorrect answers where the final answer was forced |
| `correct_base_solution_sanity` | Sanity check problems with correct answers (**NOTE:** you can ignore these) |

## Problem Structure

### Individual Problem Directory

Each problem is stored in a directory named `problem_X/` where X is the problem ID:

```
problem_330/
├── problem.json # Problem statement and metadata
├── chunks_labeled.json # Importance and analysis data for each reasoning step
├── chunk_0/
│ └── solutions.json # Multiple solution attempts for chunk 0
├── chunk_1/
│ └── solutions.json # Multiple solution attempts for chunk 1
├── ...
└── chunk_N/
└── solutions.json # Multiple solution attempts for chunk N
```

### Problem Metadata (`problem.json`)

```json
{
  "problem": "Compute $3(1+3(1+3(1+3(1+3(1+3(1+3(1+3(1+3(1+3)))))))))$",
  "level": "Level 5",
  "type": "Algebra",
  "gt_solution": "Not to be tricked by the excess of parentheses...",
  "gt_answer": "88572",
  "nickname": "Nested Multiplication"
}
```

**Fields:**
- `problem`: The mathematical problem statement (often in LaTeX)
- `level`: Difficulty level (e.g., "Level 1" to "Level 5")
- `type`: Mathematical domain (e.g., "Algebra", "Geometry", "Number Theory")
- `gt_solution`: Ground truth solution method
- `gt_answer`: Correct numerical answer
- `nickname`: Human-readable problem identifier

### Chunk Analysis (`chunks_labeled.json`)

Each reasoning step (chunk) contains detailed analysis:

```json
{
  "chunk": "First, I notice that the expression is a nested set of terms...",
  "chunk_idx": 6,
  "function_tags": ["fact_retrieval"],
  "depends_on": ["3"],
  "accuracy": 0.7448979591836735,
  "resampling_importance_accuracy": 0.14399092970521532,
  "resampling_importance_kl": 1.986370430176875,
  "counterfactual_importance_accuracy": -0.09941520467836251,
  "counterfactual_importance_kl": 1.19399334472036,
  "forced_importance_accuracy": 0.0,
  "forced_importance_kl": 1.666184462112768,
  "different_trajectories_fraction": 0.7021276595744681,
  "overdeterminedness": 0.5,
  "summary": "identify nested expression"
}
```

**Key Metrics:**
- `accuracy`: Success rate when this chunk is included
- `resampling_importance_*`: Importance measured via resampling
- `counterfactual_importance_*`: Importance measured via counterfactual
- `forced_importance_*`: Importance when forcing specific completions
- `different_trajectories_fraction`: Diversity of reasoning paths at this point
- `overdeterminedness`: How overdetermined the current step is (i.e., how similar the resampled sentences are to the original sentence)
- `function_tags`: Reasoning type classification labeled via auto-LLM (e.g., "plan_generation", "active_computation")
- `depends_on`: Dependencies on previous chunks

### Solution Rollouts (`chunk_X/solutions.json`)

Each chunk directory contains multiple solution attempts:

```json
[
  {
    "chunk_resampled": "Let me work from the innermost expression outward...",
    "is_correct": true,
    "answer": "88572",
    "full_cot": "Complete chain of thought reasoning...",
    "completion_tokens": 150,
    "reasoning_type": "step_by_step"
  }
]
```

**Fields:**
- `chunk_resampled`: The specific reasoning step (i.e., sentence) generated
- `is_correct`: Whether this leads to a correct final answer
- `answer`: Final numerical answer produced
- `full_cot`: Complete chain-of-thought reasoning

#### Personal and Sensitive Information

<!-- State whether the dataset contains data that might be considered personal, sensitive, or private (e.g., data that reveals addresses, uniquely identifiable names or aliases, racial or ethnic origins, sexual orientations, religious beliefs, political opinions, financial or health data, etc.). If efforts were made to anonymize the data, describe the anonymization process. -->

The dataset does not contain any personal, sensitive, or private data.

## Dataset Statistics

- **Models**: 2 (8B and 14B parameter variants)
- **Problems**: ~20 problems per solution type per model
- **Chunks per Problem**: Varies widely (from ~10 to 138+ reasoning steps)
- **Solutions per Chunk**: Multiple rollouts (typically 10-100 per chunk)
- **Total Reasoning Steps**: Tens of thousands across all problems

## Uses

<!-- Address questions around how the dataset is intended to be used. -->

### Research Applications

1.  **Reasoning Pattern Analysis**
    -   Study how models break down complex problems
    -   Identify effective vs ineffective reasoning strategies
    -   Analyze branching and convergence in reasoning paths

2.  **Model Comparison**
    -   Compare reasoning quality between 8B and 14B models
    -   Evaluate different model architectures (LLaMA vs Qwen)
    -   Study the effects of distillation on reasoning capability

3.  **Interpretability Research**
    -   Understand step-by-step decision making
    -   Identify critical reasoning steps
    -   Study planning vs execution phases

4.  **Training Data Insights**
    -   Identify high-quality reasoning examples
    -   Study failure modes and error patterns
    -   Develop better training objectives

## Citation

<!-- If there is a paper or blog post introducing the dataset, the APA and Bibtex information for that should go in this section. -->

**BibTeX:**

```bibtex
@misc{bogdan2025thoughtanchorsllmreasoning,
      title={Thought Anchors: Which LLM Reasoning Steps Matter?},
      author={Paul C. Bogdan and Uzay Macar and Neel Nanda and Arthur Conmy},
      year={2025},
      eprint={2506.19143},
      archivePrefix={arXiv},
      primaryClass={cs.LG},
      url={https://arxiv.org/abs/2506.19143},
}
```

## More Information

Please find our paper here: https://www.arxiv.org/abs/2506.19143. Please find an interactive demo for our paper and dataset here: https://www.thought-anchors.com. Please find our code here: https://github.com/interp-reasoning/principled-attribution

## Dataset Card Contact

Please contact uzaymacar@gmail.com for any questions or issues about the dataset.# Cot_reciever_heads
