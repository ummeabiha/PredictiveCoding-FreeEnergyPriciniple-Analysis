# Free-Energy Principle & Predictive Coding: Implementation and Analysis


Simplified implementations and research summaries of two related papers. The first is Friston's **Free-Energy Principle**. The second is **Predictive Coding** as a brain-inspired alternative to backpropagation.


## Repository Contents

| File | Description |
|---|---|
| `TheFreeEnergyPrinciple.ipynb` | Perceptual inference, active inference and hierarchical free-energy minimization |
| `PredictiveCoding.ipynb` | Predictive Coding vs Backpropagation on a nonlinear regression task |
| `Free Energy Principle.pdf` | Research analysis summary of Friston (2009) |
| `Brain Inspired Computional Intelligence via Predictive Coding.pdf` | Research analysis summary of Salvatori et al. |


## 1. Free-Energy Principle

The brain is modelled as a system that infers the hidden causes of its sensory input by minimizing variational free energy:

$$F = \tfrac{1}{2}\Pi_y(y - Wx)^2 + \tfrac{1}{2}\Pi_x(x - \mu)^2$$

The first term is the precision-weighted sensory prediction error. The second penalizes moving away from the prior belief.

**What the notebook shows:**

- **Perceptual inference:** a Gaussian generative model `y = Wx + z`. The hidden-state belief is updated by gradient descent on F, and the reduction in F is tracked at every step.
- **Active inference:** free energy is also reduced by acting on the environment, so observations move toward predictions.
- **Hierarchical predictive coding:** a two-level model (x₂ → x₁ → y) in which the higher level provides the prior for the lower level.

**Results:**

| Experiment | Initial F | Final F | Outcome |
|---|---|---|---|
| Perception | 78.584 | 1.749 | Belief moved from 0 to 2.80 (true state 3.0) |
| Perception + action | 78.584 | 0.283 | Belief 1.75, environment pulled from 3.0 to 1.90 |
| Hierarchical | 37.105 | 1.556 | x₁ = 2.83 (true 3.0), x₂ = 1.45 (true 2.0) |

The estimates settle slightly below the true values because the prior pulls them toward its mean.


## 2. Predictive Coding vs Backpropagation

Both models learn `y = sin(3x) + 0.1x` on 100 points in [-1, 1] with a **1-8-1** tanh network.

- **Backpropagation:** standard global gradient descent.
- **Predictive Coding:** for each sample, an inference phase (20 steps) relaxes the hidden state to reduce free energy, then a local weight update uses only nearby prediction errors.
- **Deeper PC:** the same local rules applied unchanged to a **1-8-8-1** network.

**Results (300 epochs):**

| Model | MSE |
|---|---|
| Backpropagation (1-8-1) | 0.0809 |
| Predictive Coding (1-8-1) | 0.0386 |
| Predictive Coding (1-8-8-1) | 0.0205 |

Across 5 random seeds:

| Model | MSE (mean ± std) |
|---|---|
| Backpropagation | 0.1100 ± 0.0164 |
| Predictive Coding | 0.0379 ± 0.0200 |

PC reached lower error here, but it needs **20× more forward passes per epoch** because of the inference loop, so the gain is not free. The comparison uses the same epoch count and different learning rates, so it reflects this small setup rather than a general claim.


## Key Takeaways

- Perception, action and learning can all be framed as reducing the same free-energy quantity.
- Predictive Coding learns a nonlinear function using only local prediction errors, with no global backward pass.
- The same PC rules extend to deeper networks without modification.
- PC's main cost is iterative inference, which matches the scalability concerns raised in the paper.

See the PDFs for the full analysis.


## Citation

This work implements and analyses the following papers:
```bibtex
@article{friston2009free,
  title   = {The free-energy principle: a rough guide to the brain?},
  author  = {Friston, Karl},
  journal = {Trends in Cognitive Sciences},
  volume  = {13},
  number  = {7},
  pages   = {293--301},
  year    = {2009},
  doi     = {10.1016/j.tics.2009.04.005}
}

@misc{salvatori2023brain,
  title         = {Brain-inspired Computational Intelligence via Predictive Coding},
  author        = {Salvatori, Tommaso and Mali, Ankur and Buckley, Christopher L. and Lukasiewicz, Thomas and Rao, Rajesh P. N. and Friston, Karl and Ororbia, Alexander},
  year          = {2023},
  eprint        = {2308.07870},
  archivePrefix = {arXiv},
  primaryClass  = {cs.AI}
}
```
