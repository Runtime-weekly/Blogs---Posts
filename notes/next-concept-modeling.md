# Next-Concept Modeling: why generate text in levels?

Published October 20, 2026 · RUNTIME research companion

## The idea

Next-Concept Modeling (NCM) studies a different generation schedule: predict a hierarchy of learned codes, then decode it into text. Positions within a level are predicted together; later levels depend on earlier ones. That can shorten the sequence of dependent decisions without making every part of generation simultaneous.

The codes are learned representations, not ordinary words or human-readable outlines. Our colored squares and example sentences illustrate the mechanism; they are not captured model output. [Paper, section 3](https://arxiv.org/html/2610.14162v1).

## How the two stages fit together

First, a multiscale tokenizer learns to encode and reconstruct text. Then its parameters are frozen and a transformer learns to generate its code levels. At inference, the resulting hierarchy goes through the decoder. The authors' [interactive explanation](https://hazyresearch.stanford.edu/blog/2026-10-12-ncm) shows a translation being decoded at successive levels.

## What the reported speedups mean

| Dataset / task | Reported generation speedup | Quality comparison with the diffusion baseline |
| --- | ---: | --- |
| One Billion Word (LM1B) | 21× | Better generative perplexity |
| OpenWebText | 10× | Competitive generative perplexity |
| XSum summarization | 31× | Comparable ROUGE-L |

These are the authors' comparisons against their diffusion baselines, not commercial-chatbot speed tests. Their timing setup uses one NVIDIA H100 SXM 80 GB, batch size 16 and 1,024 generated sequences. RUNTIME has not reproduced these benchmarks or run the released checkpoints. [Paper, evaluation and experimental details](https://arxiv.org/html/2610.14162v1).

Fewer model evaluations do not automatically mean proportionally lower latency: the amount of work per evaluation and decoding overhead also matter. Neither perplexity nor ROUGE-L alone proves that a response is factually correct.

## A useful next check

Open the authors' translation visualization and compare the source, reference and successive decoded outputs. Track one concrete fact, such as who is vulnerable to hacking, rather than judging fluency alone. Check whether that fact survives each revision. This is an observation exercise, not a benchmark or a claim about a failure we observed.

For implementation and released checkpoints, use the [official repository](https://github.com/HazyResearch/ncm) and [model/data collection](https://huggingface.co/collections/hazyresearch/ncm). Follow the current upstream evaluation instructions and record the checkpoint, task, hardware, batch size and quality metric when comparing timings. We have not validated that setup on other hardware.

## Sources and further viewing

- [Next-Concept Modeling paper and full author list](https://arxiv.org/abs/2610.14162), submitted October 9, 2026.
- [Full paper and appendices, version 1](https://arxiv.org/html/2610.14162v1).
- [Hazy Research explanation](https://hazyresearch.stanford.edu/blog/2026-10-12-ncm), October 12, 2026.
- [Build a Language Model: What Are We Actually Teaching It?](https://youtu.be/hSht0qBgSaE), RUNTIME's introductory lesson.

Research and linked code retain their original rights. For corrections, open an issue with the specific claim and a primary source.
