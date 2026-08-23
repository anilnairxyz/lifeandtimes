---
title: "Inside SimSiam: What a Non-Contrastive Objective Learns, and How it Collapses"
draft: false
date: 2026-08-17
slug: inside-simsiam
description: Training SimSiam on CIFAR-10 without a single negative example, matching SimCLR's representation quality through a completely different mechanism — then breaking it two independent ways and watching collapse happen on purpose
categories:
  - representations
tags:
  - ai
  - representational-learning
  - self-supervised-learning
  - mechanistic-interpretability
  - genai
---
[SimCLR](2026-08-06-inside-simclr.md) prevents representation collapse by contrasting with negatives. Every other image in the batch is actively pushed away, so mapping everything to one point makes the loss worse, not better. [SimSiam](2026-08-15-ssl-non-contrastive.md) does not rely on negatives at all. There is no queue, no momentum encoder, nothing pulling different images apart. Its only **defence against collapse is architectural**{: .rust}. A predictor on one branch, and a stop-gradient on the other. That seems like a decidedly weaker safety net. Does it actually work? And what happens if we take the safety net away? 

<!-- more -->

## The setup

We have the same primary encoder as SimCLR in the **CIFAR-adapted ResNet-18 with a 512-D output**{: .rust}, the same augmentation pipeline, same CIFAR-10 data, same batch size (256) and optimiser (SGD, cosine schedule with warm-up), deliberately matched so the *only* thing that differs between the two experiments is the anti-collapse mechanism itself.

On top of the primary sits a **3-layer projector**{: .rust} (`Linear → BN → ReLU`, twice, then a final `Linear → BN` with no learnable affine parameters, a specific detail from the original paper's own implementation) and a 2-layer bottleneck predictor (`Linear → BN → ReLU → Linear`, 128 → 64 → 128). The loss is symmetrised negative cosine similarity: each branch's *prediction* is matched against the *other* branch's *target*, with the target detached from the graph so no gradient flows back into it.

$$
\mathcal{L} = \tfrac{1}{2}\big[D(p_1, \operatorname{sg}(z_2)) + D(p_2, \operatorname{sg}(z_1))\big], \qquad D(p, z) = -\frac{p}{\lVert p\rVert}\cdot\frac{z}{\lVert z\rVert}
$$

That `sg` (stop-gradient) is the entire collapse prevention mechanism.

???+ warning "A different init check, on purpose"

    NT-Xent's random-init value was $\log(2N-1)$, a large positive number, the *worst* possible score, which is what makes contrastive collapse structurally impossible. SimSiam's loss is bounded in $[-1, 1]$, and at init, with $p$ and $z$ pointing in roughly random directions, cosine similarity concentrates near zero. So the expected value is close to **0**, not the worst score on the scale. Ours read **0.085**. A loss near its floor at init doesn't mean anything is wrong here, but it does mean the loss alone will never be enough to distinguish a healthy run from a broken one.

## Does it actually match SimCLR?

| | random floor | SimCLR, 200 epochs | SimSiam, 200 epochs |
| --- | --- | --- | --- |
| linear probe | 37.9% | 88.1% | **86.6%** |
| k-NN | 28.6% | 86.6% | **85.0%** |

Within a point and a half of SimCLR on both metrics, with an objective that never compares one image to another. This is pretty much what we expected going in. A non-contrastive mechanism **isn't a *better* answer to collapse than a contrastive one**{: .rust}, just a *different* one, so parity rather than a clear win is the honest prediction. It's still impressive though! An objective that never explicitly says "these two images are different" produces a representation that separates ten classes almost as well as one that does, purely from getting the optimisation dynamics right.

## Comparison at a granular level

The aggregate numbers being close is one thing. The per-class breakdown paints an even more congruous picture.

| class      | SimCLR accuracy | SimSiam accuracy |
| ---------- | --------------- | ---------------- |
| automobile | 96.8%           | 97.0%            |
| truck      | 93.7%           | 94.0%            |
| ship       | 93.5%           | 94.6%            |
| airplane   | 92.5%           | 90.6%            |
| frog       | 92.5%           | 91.6%            |
| horse      | 89.8%           | 89.4%            |
| deer       | 86.2%           | 86.0%            |
| bird       | 80.9%           | 77.0%            |
| dog        | 80.0%           | 75.2%            |
| cat        | 75.4%           | 70.7%            |

Almost the identical ranking, class for class, from two objectives that share no design principle. In *both* runs, independently, **every single class's top confusion stays inside its own broad category**{: .rust}. Vehicles only ever get mistaken for other vehicles, animals only for other animals. And in both runs the single hardest pair is the same one: cats and dogs, confused with each other three to five times more often than any other pair, and confused almost symmetrically in each direction (SimCLR: 12.4% / 13.3%; SimSiam: 13.2% / 15.3%).

![](../images/simsiam_scatter.png)

Two mechanisms that don't agree on how to prevent collapse arrived at the same picture of what's easy and what's hard. That's evidence the structure being found here is a property of the data, of how visually similar CIFAR-10's classes genuinely are to each other rather than an artefact of either training procedure.

![](../images/simsiam_per_class_accuracy.png)

## What SimCLR couldn't show us: Collapse

SimCLR structurally cannot collapse. Mapping every image to the same vector makes the negatives-based loss *worse*, so gradient descent is pushed away from that solution automatically. SimSiam's non-collapse isn't loss-driven at all. It's a side effect of the stop-gradient asymmetry, which means it is possible, on purpose, to remove it and watch the trivial solution win.

Doing that exposes something interesting: **the loss cannot tell you when this has gone wrong**{: .rust}.

| epoch | healthy loss | broken loss (no stop-gradient) |
| ----- | ------------ | ------------------------------ |
| 1     | −0.60        | −0.80                          |
| 2     | −0.75        | −0.98                          |
| 4     | −0.70        | −0.98                          |
| 8     | −0.76        | −0.98                          |
| 16    | −0.82        | −0.99                          |

Both curves are deeply negative by epoch 16. If anything, the broken run's loss looks *more* converged. Watching only the loss, this run would read as a clean success, arguably a better one than the healthy run, since it's closer to the theoretical floor of −1.

The number that actually tells the story is the **effective rank of the representation**{: .rust} i.e. how many of the primary's 512 output dimensions carry independent information, measured as $(\sum \lambda_i)^2 / \sum \lambda_i^2$ over the covariance eigenvalues $\lambda_i$. It is bounded in $[1, 512]$. 1 means every image maps to (almost) the same vector, 512 means all 512 dimensions carry equal, independent variance.

???+ info "Why not just threshold the raw variance?"

    The obvious first attempt to count dimensions whose raw variance across the dataset exceeds some cutoff turns out to fail here for two compounding reasons. First, a cosine-similarity loss is invariant to the overall *magnitude* of the primary's output, so weight decay steadily shrinks that magnitude over hundreds of epochs with nothing anchoring it, driving every dimension's raw variance toward zero even while the representation's *direction*, the only thing the loss and the linear probe actually use, stays fully informative. Second, and more fundamentally, a fixed threshold doesn't generalise across dimensionality at all: a healthy, isotropically-spread 512-D vector has roughly $1/512$ of its variance in each individual dimension purely from being spread over 512 directions, comfortably under any threshold calibrated for a much lower-dimensional space. The eigenvalue ratio above sidesteps both problems at once, multiplying every feature by a constant leaves the ratio exactly unchanged, and it needs no threshold to calibrate in the first place.

Healthy SimSiam's effective rank, checked at epoch 200 turns out to be **31.1 out of 512**{: .rust}. Not maximally spread, but nowhere near collapsed, and in a similar ballpark to the ~30 active dimensions [the VAE](2026-08-01-inside-a-vae.md) found useful for a completely different dataset and objective, a resonance worth noting without over-reading into. The broken run, with the stop-gradient removed:

| epoch | 1 | 2 | 4 | 8 | 16 | 20 |
| --- | --- | --- | --- | --- | --- | --- |
| effective rank | 1.19 | 1.24 | 1.07 | 1.52 | 1.00 | 1.03 |

Pinned at the collapsed end of the scale from the very first epoch onward. Two objectives with loss curves that look, if anything, more converged than the healthy run's but with a representation that never escaped total collapse.

## Worse than doing nothing at all

The linear probe over the same run tells the sharpest version of this story:

$$
40.5\% \rightarrow 41.6\% \rightarrow 36.2\% \rightarrow 34.4\% \rightarrow 30.0\% \rightarrow \mathbf{29.4\%}
$$

Not flat but declining. And the final number, **29.4% linear probe / 25.3% k-NN**, sits *below* the completely untrained, random-initialisation floor of 37.9% / 28.6% from the very first experiment in this series. Twenty epochs without the stop-gradient didn't just fail to learn, they actively destroyed the crude signal a random network already provided for free, because once the target branch stops holding still, minimising the loss and minimising usefulness point in the same direction.

The per-class picture confirms the failure is structural. In the healthy runs, confusion was localised with nearly every class under 5%, with cat/dog as the one clear exception, and the vehicle/animal boundary held without a single crossing. In the collapsed run, every class shows substantial confusion (12% to 27%), and that boundary breaks down too: horse's top confusion becomes **truck** (19.1%), an animal mistaken for a vehicle, something that never happened across either healthy run. Collapse doesn't just erode fine-grained accuracy, it erases the coarse structure underneath it as well.

![](../images/simsiam_collapsed_per_class_accuracy.png)

![](../images/simsiam_collapsed_scatter.png)

## A second, independent way to break it

Removing the stop-gradient isn't the only way to collapse SimSiam. The predictor exists specifically to give the "predicting" branch a role the target branch doesn't share. Swap it for the identity function ($p = z$) and that asymmetry disappears too, even with the stop-gradient still in place.

This ablation starts from a worse position than the first one. Even a completely untrained primary produces highly correlated outputs for different random images, a known signal-propagation artefact of deep, untrained CNNs, unrelated to SimSiam itself. So with no predictor to break that symmetry, the loss sits close to its collapsed value before a single gradient step is taken.

| epoch          | 1    | 2    | 4    | 8    | 16   | 20   |
| -------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| effective rank | 1.04 | 1.04 | 1.17 | 1.34 | 1.15 | 1.08 |

Pinned even tighter to the collapsed floor than the stop-gradient ablation was, from the very first checkpoint onward. Final numbers: **33.8% linear probe, 27.8% k-NN** which is again below the untrained random floor, a representation actively made worse than doing nothing at all.

![](../images/simsiam_nopred_per_class_accuracy.png)

![](../images/simsiam_nopred_scatter.png)

The per-class picture adds two more findings:

1. One specific confusion recurs in both: horse's top confusion is truck in the stop-gradient ablation, and it's truck again here. Two unrelated ways of breaking the same model landed on the exact same cross-category mistake. Perhaps whatever faint residual signal survives total collapse in either failure mode may be tracking something crude like colour or outdoor-background statistics, similar enough between horses and trucks to survive even when everything else is destroyed.

2. This run's aggregate accuracy (33.8%) is *higher* than the stop-gradient ablation's (29.4%), but its coarse category structure is *more* broken. Two cross-superclass confusions here (`bird → airplane` joins `horse → truck`) against only one in the other ablation. Aggregate accuracy and structural integrity are not the same axis. A collapsed representation can claw back a point or two of raw accuracy by spreading its one surviving direction a little more evenly across classes, while simultaneously losing more of the coarse boundary that both healthy runs preserved without a single exception. Reading only the headline probe number would rank this run as the less damaged one; the per-class picture says the opposite.

## What the experiment teaches

SimCLR and SimSiam land in the same place by two unrelated routes, genuine evidence that what's being discovered is a fact about the data, not a quirk of either method. But only SimSiam can be taken apart to show *why* it works. Pull out either half of the asymmetry that prevents collapse, the stop-gradient or the predictor and a representation that matched SimCLR almost point for point collapses to a single effective dimension, while its training loss still seems to insist that everything is fine both times. The representation-space diagnostic if it disagrees with the loss curve, is the metric to trust.

Representational learning, particularly [VICReg](2026-08-20-barlow-twins-vicreg.md) is the next to observe, and it gets its own version of this experiment. Rather than an architectural asymmetry, its anti-collapse mechanism is an explicit variance term in the loss, and it can be broken just as directly. Zero out that one term and watch the same story play out.