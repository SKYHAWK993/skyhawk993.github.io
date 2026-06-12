---
title: "Writing math on this site"
date: 2026-06-12
description: "A quick demo of LaTeX support — inline and display math, both rendered with KaTeX."
tags: ["meta", "math"]
---

This site renders LaTeX out of the box. Inline math like $e^{i\pi} + 1 = 0$ sits inside a sentence, and display math gets its own block:

$$
\hat{\beta} = (X^\top X)^{-1} X^\top y
$$

Anything KaTeX supports works — aligned equations, matrices, the lot:

$$
\begin{aligned}
P(A \mid B) &= \frac{P(B \mid A)\,P(A)}{P(B)} \\
\binom{n}{k} &= \frac{n!}{k!\,(n-k)!}
\end{aligned}
$$

## How to write it

Wrap inline math in single dollar signs (`$ ... $`) and display math in double dollar signs (`$$ ... $$`) — exactly like Overleaf or a Jupyter notebook. No setup, no plugins to think about. Write the markdown file, push, done.

Code blocks work too, with syntax highlighting:

```python
import numpy as np

X = np.random.randn(100, 3)
beta_hat = np.linalg.inv(X.T @ X) @ X.T @ y
```
