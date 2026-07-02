# linear-models-from-scratch

Here I have done 3 things: 
1) Implemented linear and logistic regression implemented from scratch in NumPy, benchmarked
against scikit-learn 
2) Benchmarked my implementation against industry standard ones. 
3) Written up a didactic explanation piece, directed to econometricians savvy with regression

# linear-models-from-scratch

Linear and logistic regression implemented from scratch in NumPy, benchmarked
against scikit-learn, with a study of optimization, regularization, and the
bias–variance tradeoff.

## What this is

From-scratch implementations of the core linear models — no ML libraries doing
the estimation, just NumPy for the linear algebra — paired with an experiments
layer that makes the theory visible (convergence, conditioning, shrinkage, the
bias–variance U-curve). Every estimator is verified against a scikit-learn
oracle in the test suite.

The intellectual angle is regression seen through two lenses: as **inference**
(estimate the true effect, report a standard error) versus as **prediction**
(minimize a loss, generalize, trade bias for variance). The README covers what's
here and how to run it; the full treatment is in [the writeup](writeup/writeup.pdf).

<!-- TODO: 1–2 sentences in your own voice on why you built this / what you took from it. -->

## What's implemented

Estimators (all with a scikit-learn-style `fit` / `predict` interface):

| Model                | Solvers                                   | Verified against sklearn |
|----------------------|-------------------------------------------|:------------------------:|
| Linear regression    | normal equations · QR · SVD · gradient descent | ✅ |
| Logistic regression  | gradient descent (cross-entropy)          | ✅ |
| Ridge                | closed form · gradient descent            | ✅ |
| Lasso                | coordinate descent (soft-thresholding)    | ✅ |

Experiments (each script regenerates one figure):

- **Convergence** — loss vs. iteration; the learning-rate sweep (crawl / converge / diverge).
- **Conditioning** — GD before vs. after standardizing features; same solution, dramatically faster.
- **Regularization paths** — ridge coefficients gliding toward zero vs. lasso snapping to exactly zero.
- **Bias–variance** — the synthetic simulation measuring bias², variance, and irreducible error; the U-curve.
- **Cross-validation** — k-fold model selection; confirms CV picks ≈ the bias–variance-optimal λ.

<!-- TODO: tick/untick as you complete each. Keep this list honest — it's your credibility. -->

## Installation

Requires [uv](https://docs.astral.sh/uv/). Clone and sync — uv creates the
virtual environment and installs pinned dependencies in one step:

```bash
git clone https://github.com/<your-username>/linear-models-from-scratch.git
cd linear-models-from-scratch
uv sync
```

That's it — no manual venv activation. Every command below uses `uv run`, which
executes inside the project environment automatically.

## Usage

There are two ways to use this repo: **reproduce the experiments**, or **import
the estimators** into your own code.

### Reproduce the experiments

Each experiment script writes its figure to `figures/`:

```bash
uv run python experiments/bias_variance.py     # the centerpiece — the U-curve
uv run python experiments/convergence.py
uv run python experiments/conditioning.py
uv run python experiments/reg_paths.py
uv run python experiments/cross_validation.py
```

### Import the estimators

The estimators follow the scikit-learn convention (`fit` / `predict`, learned
parameters with a trailing underscore), so they drop into familiar workflows:

```python
from linear_models import LinearRegression

# solver is a parameter: "normal" | "qr" | "svd" | "gd"
model = LinearRegression(solver="gd", lr=0.01, n_iters=1000)
model.fit(X_train, y_train)

preds = model.predict(X_test)
print(model.coef_, model.intercept_)
```

### Run the tests

The test suite fits each estimator alongside scikit-learn's on the same data and
asserts the coefficients match to tolerance — the oracle checks:

```bash
uv run pytest
```

## Results

<!-- TODO: inline your best 1–2 figures once generated, with a one-line takeaway each. -->

![Bias–variance tradeoff](figures/bias_variance.png)

*The total-error U-curve: bias falls and variance rises with model complexity, and
the minimum sits strictly in the interior — a biased estimator beats the unbiased
one in mean squared error.* See [the writeup](writeup/writeup.pdf) for the full analysis.

## Project structure

```
linear-models-from-scratch/
├── src/linear_models/     # the estimators (the reusable library)
│   ├── linear_regression.py
│   ├── logistic_regression.py
│   ├── regularization.py      # ridge, lasso
│   ├── optim.py               # gradient-descent loop
│   ├── metrics.py
│   ├── model_selection.py     # k-fold + CV
│   └── datasets.py            # real loaders + synthetic generator
├── experiments/           # scripts that produce the figures
├── tests/                 # oracle checks vs. scikit-learn
├── figures/               # generated plots (gitignored)
└── writeup/               # the LaTeX writeup + compiled PDF
```

## Where this leads

This is the foundation of a larger sequence. The loss-plus-optimizer and gradient
machinery here is rebuilt from the ground up in
[autodiff-from-scratch](#) <!-- TODO: link -->; the probabilistic view of
regularization (ridge/lasso as priors) sets up the
[bandits](#) and [sampling](#) <!-- TODO: link --> work; and the
inference-vs-prediction tension introduced here is the seed of
[causal-inference / double-ML](#) <!-- TODO: link -->, where the two lenses fuse —
using prediction machinery to recover valid inference. Each project stands alone,
but together they're a single program: rebuild modern ML from its primitives, then
build real systems on top.

## References & license

<!-- TODO: optional — Breiman, "Statistical Modeling: The Two Cultures"; ESL; etc. -->

MIT