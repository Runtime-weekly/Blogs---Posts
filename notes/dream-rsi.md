# Dream-RSI: what we actually tested

Published September 21, 2026 · Episode companion, not a weekly roundup

[Watch](https://youtu.be/opBzkTGTx8E) · [Read the implementation and results](https://github.com/Runtime-weekly/dream-rsi-experiment)

The idea is to improve the strategy that chooses what a coding model should try
next, while keeping the underlying model fixed. Earlier attempts form a recorded
tree. Other strategies can replay the outcomes already in that tree before the
next round of real work. Replay cannot reveal an outcome that was never recorded.
See the [official project](https://www.dream-rsi.com/) and [paper](https://arxiv.org/abs/2609.14858).

Our independent, small reconstruction made ten model requests: eight numerical
solver attempts and two proposed search-policy revisions. Both revisions tied
the original in replay. Neither was adopted. The loop ran, but this result did
not demonstrate improved exploration or reproduce the paper's performance.

We have published a [standalone advanced experiment](https://github.com/Runtime-weekly/dream-rsi-experiment)
with the implementation, pinned Python dependencies, tests and a results summary.
Start with its CPU/mock tests; they do not need a model server. Native execution
requires Linux confinement features. Raw production logs and local infrastructure
are not part of the public package.

If you are new to Python, [start with Laya or Needle](https://github.com/Runtime-weekly/runtime-tutorials/blob/main/START_HERE.md).

Sources checked for this note: [authors' repository](https://github.com/zhengkid/Dream-RSI),
[official project](https://www.dream-rsi.com/), and the recorded local experiment.
The authors' repository currently contains documentation/assets; this independent
implementation should not be mistaken for an official code release.
