# QwenGyre: learning from extended project work

Published October 14, 2026 · RUNTIME research companion

## What the research addresses

QwenGyre is Alibaba Token Hub's training framework for agents working on
multi-hour software tasks. An elastic scheduler moves GPU capacity between
serving agents and updating the model without ending live project sessions.
A trajectory processor turns branching histories into bounded training samples,
preserving each response's context and avoiding duplicate training targets.
[Paper, sections 3–5](https://arxiv.org/html/2609.33848v1).

## Reported results

For Qwen 3.8 2.4T on NL2RepoBench, the authors compare 48 training steps with
matched GPU budgets and scheduling-staleness constraints:

| System | Training time |
| --- | ---: |
| Async | 134.55 hours |
| Colocate | 91.47 hours |
| QwenGyre | 75.42 hours |

The reported evaluation pass rate rises from 52.48% to 58.54% during training.
Similar scores across schedulers mean this is not an isolated intelligence gain
from GPU scheduling. The approximately 700K tokens per run aggregate multiple
calls, including repeated context; they are not one prompt.
[Paper, section 6](https://arxiv.org/html/2609.33848v1).

## What this companion does and does not demonstrate

RUNTIME's appointment-site example and animations explain the ideas. We did not
reproduce this training experiment. Neither these illustrations nor the reported
benchmark establishes reliable completion of arbitrary real projects.

## A practical way to think about it

When observing your own agent, record three things separately:

1. What it attempted and what the model could see at that moment.
2. Time spent generating, using tools, testing, and waiting.
3. Whether the resulting work passed an explicit check.

For example, a calendar page appearing is a different outcome from successfully
booking an appointment and preventing a conflicting booking. Decide which outcome
you care about before comparing attempts. These are suggested observation habits,
not a QwenGyre reproduction or a training recipe.

## Sources and further viewing

- [QwenGyre paper and authors](https://arxiv.org/abs/2609.33848), version 1, September 27, 2026.
- [Full paper, figures, methods and limitations](https://arxiv.org/html/2609.33848v1).
- [RUNTIME's local xLLM-Loop cache test](https://youtu.be/apypDwmOwyI): a separate inference experiment, not validation of QwenGyre.
- [RUNTIME channel](https://www.youtube.com/@runtime-weekly).

This page is the research companion. A direct episode link will be added when
the video is publicly available. No training implementation or model weights
are included here, and linked research retains its original rights.

For corrections, open an issue with the specific claim and a primary source.
