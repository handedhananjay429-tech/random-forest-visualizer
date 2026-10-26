# 🌲 Random Forest Visualizer

An interactive, browser-based educational tool that lets you **watch a Random Forest classifier learn in real time** — tree by tree, split by split.
No installation, no dependencies: open `index.html` in any modern browser and start exploring.

---

## What the simulation shows

The app renders **three live panels side by side**:

| Panel | What you see |
|---|---|
| **Selected Tree (from the forest)** | The decision boundary of the one tree you are currently inspecting. Points that were *not* in that tree's bootstrap sample are greyed out (out-of-bag). Hover to see what *that tree alone* predicts at any point. |
| **Random Forest Boundary** | The combined boundary produced by *all* trees voting together. Opacity encodes confidence: darker = more trees agree. Hover anywhere to see the full vote breakdown (how many trees voted for each class). |
| **Tree Gallery** | Thumbnails of the first 8 trees. Because each tree is trained on a different random sample, every boundary looks slightly different. Click a thumbnail to inspect that tree in the left panel. |

Accuracy numbers (train, test, and out-of-bag) update live in the sidebar.

---

## How a Random Forest works — plain English

### 1 · Bootstrap Sampling
Instead of training every tree on the full dataset, each tree gets its own **random sample drawn with replacement** (a "bootstrap sample").
On average, about 63% of the original points are included; the remaining ~37% are called **out-of-bag (OOB)** points.
Because different trees see different slices of the data, they will disagree with each other — and that disagreement is what makes the ensemble stronger than any single tree.

### 2 · Random Feature Selection
At every split inside a tree, the algorithm only considers a **random subset of features** (here: 1 or 2 of the x/y coordinates) rather than all features.
This forces different trees to rely on different information, preventing them from all converging to the same structure.
> Without this step (and without bootstrap sampling) every tree would be nearly identical, and the forest would be no better than a single tree.

### 3 · Gini Impurity — how a split is chosen
For each candidate threshold the algorithm measures **Gini impurity**:

```
Gini = 1 − Σ pₖ²
```

where pₖ is the fraction of class k in a node.
Gini = 0 means a node is perfectly pure (one class only); Gini = 0.5 is maximum impurity (50/50 mix).
The split that minimises the *weighted* Gini of the two resulting children is chosen.

### 4 · Majority Voting
When predicting a new point, every tree in the forest casts a vote (Class A or Class B).
The class with the most votes wins — this is **majority voting**.
Because individual trees overfit in different directions, averaging their predictions cancels out much of the noise, giving a smoother and more accurate boundary than any single tree.

### 5 · Out-of-Bag (OOB) Score
Each tree is never shown its OOB points during training.
Those points can therefore be used as an **honest test set for that tree**.
Averaging each training point's OOB predictions across all trees that skipped it gives an unbiased accuracy estimate — for free, without needing a separate validation split.

---

## How to run

```
# No build step needed — just open the file:
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux
```

Or drag `index.html` into any modern browser (Chrome, Firefox, Edge, Safari).

---

## Controls

### Dataset
| Control | Description |
|---|---|
| **Shape** | Choose from Moons, Circles, Two Blobs, or XOR — classic 2-class benchmark shapes. |
| **Number of Points** | How many data points to generate (40–500). |
| **Noise** | Adds Gaussian noise to the points (0 = clean, 0.5 = very noisy). |
| **Generate New Data** | Re-generates the dataset with the current settings and resets all trees. |

### Add Points (click canvas)
Click directly on either canvas to add a point of the selected class (Class A or Class B).
The new point is immediately assigned to train (75%) or test (25%) without disturbing the existing split.

### Forest Controls
| Control | Description |
|---|---|
| **Number of Trees** | How many decision trees to include in the forest (1–100). |
| **Max Depth** | Maximum depth of each tree (1–10). Shallow trees = high bias, low variance. |
| **Max Features per Split** | 1 = consider only a random single feature at each split (more randomness); 2 = consider both x and y. |
| **Bootstrap Sampling** | Toggle on/off sampling with replacement. Off = all trees see the same data (disables OOB score). |

> ⚠️ If Bootstrap Sampling is **OFF** and Max Features is **2**, every tree will be nearly identical — the "forest" gives you no benefit over a single tree. Enable at least one source of randomness.

### Actions
| Button | Description |
|---|---|
| **Train Forest** | Trains all trees at once (non-blocking, shows a progress bar). |
| **Add One Tree** | Trains and appends a single new tree; useful for watching the boundary stabilise tree-by-tree. |
| **Next Tree** | Cycles through trees in the Selected Tree panel one at a time. |
| **Reset** | Removes all trees (keeps the dataset). |

---

## Metrics explained

| Metric | What it measures |
|---|---|
| **Train Acc** | Accuracy on the 75% of points used for training. High values alone can indicate overfitting. |
| **Test Acc** | Accuracy on the held-out 25%. This is the honest measure of generalisation. |
| **OOB Score** | Out-of-bag accuracy (see above). Should be close to Test Acc when bootstrap is enabled. |

---

## File structure

```
random-forest-visualizer/
├── index.html   ← entire app (HTML + CSS + JS, no dependencies)
└── README.md    ← this file
```

All algorithms (Gini impurity, decision tree building, bootstrap sampling,
random feature selection, majority voting, OOB scoring) are implemented from scratch
in plain JavaScript inside `index.html`, with comments explaining each step.
# random-forest-visualizer
