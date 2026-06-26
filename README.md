# ml-lab

This is basically just for myself and my own learning, but I'm setting it up so you, dear reader, can view it as well. Each numbered folder is a **self-contained project** with its
own `pyproject.toml`, dependencies, and `README.md`. The goal is to familiarize myself with tensorflow and use some of the ML theory I've learned. 

## how this repo is organized

- Every project owns its dependencies, so just run `uv init` inside a new project folder. Thus it
  gets its own isolated `.venv` and lockfile. Projects do **not** share environments.
- `common/` holds only stable, boring helpers (plotting, metrics). If you're still
  iterating on a utility, copy-paste it into the project instead of importing it — see
  `common/README.md` for the reasoning.
- `data/` inside each project is gitignored. A `download.py` script reproduces it.
- Notebooks are for exploration; the keepers get rewritten into the project's `src/`.

## Per-project layout

```
NN-project-name/
├── README.md        # problem · approach · results · what I learned
├── pyproject.toml   # this project's deps (isolated)
├── src/             # importable code — the keepers
├── notebooks/       # exploration only
├── data/            # gitignored; recreated by src/download.py
└── results/         # plots, metrics, saved models
```

## bash for a new project

```bash
uv init NN-project-name
cd NN-project-name
uv add numpy matplotlib scikit-learn   # whatever this project needs
uv run python src/train.py
```

## project index

| #  | Project                      | Demonstrates                                          | Status      |
|----|------------------------------|-------------------------------------------------------|-------------|
| 01 | regression-from-scratch      | OLS as inference vs. ML; GD; ridge/lasso; bias-var    | ☐ not started |
| 02 | autograd-mlp                 | reverse-mode autodiff; backprop; PyTorch reimpl       | ☐ not started |
| 03 | bandits                      | Thompson sampling, UCB; online learning               | ☐ not started |
| 04 | causal-inference             | DiD, IV, propensity matching; double/debiased ML      | ☐ not started |
| 05 | ship-it                      | data → model → FastAPI → Docker → deployed            | ☐ not started |
| 06 | llm-app                      | attention from scratch; RAG / agent with real eval    | ☐ not started |

> Tick a box and link the project folder as you go. When a project is portfolio-ready,
> consider graduating it into its own clean standalone repo — that reads better on a
> GitHub profile than a folder in a monorepo.

## conventions

- **Python:** pinned per-project via `.python-version` (uv manages the interpreter).
- **Formatting/linting:** `uvx ruff format .` and `uvx ruff check .` (no install needed).
- **Reproducibility:** commit `uv.lock`; never commit `data/` or model checkpoints.