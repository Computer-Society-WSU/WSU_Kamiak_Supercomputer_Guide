## 🛠️ A Beginner-Friendly Development Guide for Researchers

*Target audience: Non-CS scientists and analysts using Windows WSL, Linux, or macOS.*

This guide covers:

1. **Version control & collaboration**: Git, branches, PRs, contributions
2. **Project organization**: folder/file structure for code, models, data, graphs
3. **Code style & documentation**: Python examples, comments, README guidelines

---

### 1. Version Control & Collaboration with GitHub

**Why Git?**

* Keeps track of changes over time
* Enables collaboration without overwriting each other’s work

#### 1.1 Setup & Common Commands

| OS                | Command to install Git                                                                         |
| ----------------- | ---------------------------------------------------------------------------------------------- |
| **Linux**         | `sudo apt-get install git`                                                                     |
| **macOS**         | Requires Homebrew (install via [Install Homebrew](#install-homebrew)), then `brew install git` |
| **Windows (WSL)** | Already in Ubuntu WSL: `sudo apt-get install git`                                              |

#### 1.2 Cloning & Branch Management (WSL)\*\* | Already in Ubuntu WSL: `sudo apt-get install git` |

Configure your identity:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@wsu.edu"
```

#### 1.2 Cloning & Branch Management

```bash
# Clone a repo
git clone https://github.com/your-org/project.git
cd project

# Create & switch to a new branch for your feature or fix
git checkout -b feature/my-experiment
```

**Tips**:

* Use descriptive branch names: `feature/add-data-loader`, `bugfix/fix-plot-labels`
* Keep branches small and focused on one task

#### 1.3 Making Changes & Committing

```bash
# Stage files you’ve modified or added
git add data_loader.py analysis/plot.py
# Commit with a clear message
git commit -m "Add data loader module and sample plot"
```

**Good commit messages**:

* Short summary (<=50 chars)
* Optional blank line + more detail

#### 1.4 Pushing & Pull Requests (PRs)

```bash
# Push your branch to GitHub
git push origin feature/my-experiment
```

1. Go to the GitHub repo in your browser.
2. Click **Compare & pull request** for your branch.
3. Add description, link any issues, request reviewers.
4. Click **Create pull request**.

**Context & Tips**:

* Assign a friendly reviewer (lab colleague).
* Explain why the change matters: reproducibility, bugfix, new feature.

---

### 2. Project Structure & File Naming

A clear structure helps you and collaborators find things quickly.

```text
project/                     # root
├── README.md                # overview + instructions
├── data/                    # raw and processed data
│   ├── raw/
│   └── processed/
├── models/                  # saved AI/ML models
│   └── my_model.pkl
├── notebooks/               # exploratory Jupyter notebooks
│   └── analysis.ipynb
├── scripts/                 # reusable scripts and modules
│   ├── data_loader.py
│   ├── train_model.py
│   └── plot_results.py
├── graphs/                  # static figures for papers/reports
│   └── fig1_experiment.png
├── requirements.txt         # Python dependencies
└── .gitignore               # ignore logs, large files, env folders
```

#### 2.1 README.md Contents

* **Project title & summary**
* **Prerequisites**: Git, Python version, required modules
* **Quickstart**: clone, install, run basic script/notebook
* **Folder descriptions** (like above)
* **How to contribute** section

#### 2.2 Naming Conventions

* **Folders**: lowercase, underscores (`data_loader/`)
* **Python files**: lowercase, underscores (e.g., `train_model.py`)
* **Notebooks**: prefix with number for order (`01_data_cleaning.ipynb`)
* **Graphs/figures**: `fig<number>_description.png`

---

### 3. Code Style & Documentation (Python Examples)

Consistent style makes code easier to read and maintain.

#### 3.1 Python Style (PEP 8 Basics)

* **Indent**: 4 spaces, no tabs
* **Line length**: max 79 characters for code, 72 for comments
* **Imports**: one per line, grouped: standard library, third-party, local
* **Naming**:

  * Functions and variables: `lowercase_with_underscores`
  * Classes: `CapWords`
  * Constants: `ALL_CAPS`

```python
# Good example
import os
import pandas as pd

from my_project.scripts.data_loader import load_data


def analyze_data(path: str) -> pd.DataFrame:
    """
    Load and analyze dataset from given path.

    Args:
        path: Path to CSV file.

    Returns:
        DataFrame with summary statistics.
    """
    df = load_data(path)
    summary = df.describe()
    return summary
```

#### 3.2 Comments & Docstrings

* **Inline comments**: use sparingly, explain why (not what)
* **Docstrings**: use triple quotes, include Args, Returns, and Examples if needed

```python
# BAD: redundant comment
x = x + 1  # increment x by 1

# GOOD: explain rationale
# Adjust for sensor offset from calibration experiment
y_calibrated = y_raw - offset
```

#### 3.3 Automated Style Checks

* **Flake8**: linting
* **Black**: auto-format
* **Pre-commit hook**: run checks before each commit

```bash
pip install flake8 black pre-commit
pre-commit install
```

Add `.pre-commit-config.yaml` in root:

```yaml
repos:
  - repo: https://github.com/psf/black
    rev: stable
    hooks:
      - id: black
  - repo: https://gitlab.com/pycqa/flake8
    rev: 4.0.1
    hooks:
      - id: flake8
```

---

### 👉 Next Steps & Tips

---

## Install Homebrew on macOS {#install-homebrew}

If you don’t have Homebrew, the de facto package manager for macOS, install it by running:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Follow the on-screen prompts. Once installed, ensure your shell is configured by adding Homebrew to your PATH (usually displayed at the end of the install script).

---

### 👉 Next Steps & Tips

* Keep branches focused on a single experiment or fix.
* Regularly pull updates from `main` to avoid merge conflicts: `git pull origin main`.
* Document assumptions and data provenance in README or notebooks.
* Celebrate each successful PR merge—progress matters!

Now you’re set to version-control your research projects professionally and reproducibly. Happy coding! 🎉
