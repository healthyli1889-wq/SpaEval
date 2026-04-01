## Background
Spatial reasoning is critical for real-world applications of foundation models, ranging from embodied AI to multimodal decision systems. However, it remains a persistent bottleneck for current large language models (LLMs) and multimodal large language models (MLLMs), and the factors underlying their weak performance are still poorly understood.

Solution: The current study evaluated the spatial intelligence of MLLMs (Qwen3.5-397B-A17B and Gemini 3.1 Pro) under 2 prompting conditions: zero-shot and augmented prompting. Using gold-standard benchmark of SpatialEval to assess performance, such as object relations and compositional spatial reasoning, we aimed to examine how prompting strategy affect model's spatial reasoning capability. 

Hypotheses: 
- augmented prompting vs zero-shot: Model's spatial reasoning capability signiifcantly improved after adopting augmented prompting strategy.


## Method
In specific, the study adopted:
- SpatialEval benchmark (TQA, VTQA, VQA)
