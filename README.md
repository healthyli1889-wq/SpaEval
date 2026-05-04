# Spatial Keywords Are Not Spatial Understanding

> *Investigating Prompting and Modality Effects on MLLM Spatial Reasoning*

A controlled study on whether **prompt scaffolding** and **input modality** actually improve spatial reasoning in multimodal LLMs — or whether they just dress up linguistic shortcuts. We benchmark **Gemma-4-31B-it** and **Qwen-3.5B-35B-A3B** on the SpatialEval benchmark under a 2 × 2 factorial design (240 trials), and find that:

- **Augmented prompting** (a 4-step spatial scaffold) yields *zero* improvement over zero-shot — both 91.7%.
- **Text-only questions outperform visual ones** (95.0% vs. 88.3%), even though "spatial" intuitively implies vision.
- Implication: current MLLMs solve spatial problems through **linguistic inference over described relations**, not through perceptual grounding. You cannot prompt your way out of a perceptual bottleneck.

---

## Quick Start

```bash
git clone https://github.com/healthyli1889-wq/spatial-keywords-mllm.git
cd spatial-keywords-mllm
pip install -r requirements.txt
```

Run the full benchmark (240 trials across 4 conditions × 3 subtypes × 2 models):

```bash
python run_benchmark.py \
  --model gemma-4-31b-it \
  --modality tqa \
  --prompting augmented \
  --subtype distance
```

Reproduce the reported ANOVA results:

```bash
python analysis/anova.py --results results/all_trials.csv
```

---

## What's in this repo

| Component | What it does |
|---|---|
| `benchmarks/` | SpatialEval scenes covering distance, object relation, and counting |
| `prompts/` | Zero-shot vs. augmented prompt templates for both TQA and VQA |
| `models/` | Inference wrappers for Gemma-4-31B-it and Qwen-3.5B-35B-A3B |
| `analysis/` | Factorial ANOVA, accuracy breakdowns, condition-level plots |
| `results/` | Raw trial-level outputs (240 rows, fully reproducible) |

---

## Experimental Design

A clean 2 × 2 factorial — easy to extend, easy to interpret.

|  | **Zero-Shot Prompt** | **Augmented Prompt** (4-step scaffold) |
|---|---|---|
| **TQA** (text-described scene) | baseline | does verbal CoT help when input is already verbal? |
| **VQA** (image of scene) | baseline | does verbal CoT help when input is visual? |

The augmented scaffold asks the model to:

1. List every object and its position
2. Identify the spatial relationship in the question
3. Evaluate each answer choice against the layout
4. Eliminate contradictions

Every trial runs in a **fresh session** to prevent in-context contamination. Accuracy is computed as proportion correct vs. ground truth.

---

## Headline Results

```
                    Zero-Shot     Augmented
        ────────────────────────────────────
        TQA           95.0%        95.0%
        VQA           88.3%        88.3%
        ────────────────────────────────────
        Overall       91.7%        91.7%   ← identical
```

- **Prompting main effect:** n.s. across all subtypes (Distance *p* = .079; Object Relation *p* = .320; Counting *p* = .107)
- **Modality main effect:** TQA > VQA (95.0% vs. 88.3%), n.s. per subtype
- **Best condition:** Qwen Zero-Shot TQA → **100%**
- **Worst condition:** Qwen Augmented VQA → **83.3%** (same model, *with* the scaffold and *with* the image)

Full ANOVA tables, subtype breakdowns, and per-trial outputs in [`results/`](./results).

---

## Why this matters

This study contributes one specific behavioral signal to a question the field is actively wrestling with: **is MLLM spatial reasoning grounded, or is it linguistic pattern-matching dressed up as spatial cognition?**

Two findings, read together, point one direction:

- If augmented prompting added no value, the scaffold had no new information to operate on — meaning the reasoning path was already verbal.
- If TQA outperformed VQA, the model extracts spatial structure more reliably from language than from pixels — meaning the visual pathway is the bottleneck.

This aligns with recent work framing the gap as one between **linguistic abstraction** and **geometric continuity** (Yang et al., CVPR 2025; TRACE, 2026), and points future work toward **grounded reasoning interfaces** — Grounded CoT, allocentric scene representations, 3D priors — rather than additional layers of verbal scaffolding.

---

## Known Limitations

We're upfront about what this study can and can't claim:

- **N = 240** — modest sample, especially with near-ceiling Object Relation accuracy. Underpowered for small prompting effects.
- **Augmented prompting ≈ keyword scaffold.** Arguably more of a lexical cue than a true reasoning intervention. Stronger interventions (forced layout generation, explicit coordinates, 3D priors) are needed to test the deeper claim.
- **Two open-source models only.** Frontier closed models (GPT-4o, Gemini 2.5, Claude) may behave differently.

---

## Roadmap

- [ ] Add Grounded CoT–style prompting variant (force model to output positions before answering)
- [ ] Extend to GPT-4o / Gemini 2.5 / Claude for cross-frontier comparison
- [ ] Increase N to detect smaller effects on harder subtypes
- [ ] Add error-profile analysis (Gemma vs. Qwen failure modes)
- [ ] Test on out-of-distribution spatial scenes (open-world, not synthetic)

---

## Citation

If you use this benchmark or build on these findings, please cite:

```bibtex
@misc{li2025spatialkeywords,
  author = {Li, Healthy and [collaborators]},
  title  = {Spatial Keywords Are Not Spatial Understanding:
            Investigating Prompting and Modality Effects on MLLM Spatial Reasoning},
  year   = {2025},
  howpublished = {\url{https://github.com/healthyli1889-wq/spatial-keywords-mllm}}
}
```

---

## Acknowledgements

Built on [SpatialEval](https://arxiv.org/abs/2406.14852). Thanks to advisors **Yijiang** and **Zaibin**, and the SPAgent research group at the University of Michigan.

---

## Contribute

Found a bug, want to add a model, or have a sharper prompting variant to test? PRs welcome — see [`CONTRIBUTING.md`](./CONTRIBUTING.md). The whole point of this repo is making the question easy for others to push on.
