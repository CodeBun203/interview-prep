# Tutorial: Machine Learning — scikit-learn, PyTorch, TensorFlow, JAX

> **Read the honesty note first.** Your real AI strength is **LLM inference + RAG** (Ollama,
> LangChain, FAISS) — which you genuinely do in production and can explain better than most. You do
> **not** train deep nets. The plan: do ONE real **scikit-learn** project (genuine, defensible ML),
> and learn enough **PyTorch** to discuss it. Never claim you train models from scratch in
> production — it's the easiest overclaim to expose.

---

## PART 1 — scikit-learn (do this for real — it's achievable & honest)

### Mental model
"Classical" ML on tabular data: regression, classification, clustering. Consistent API everywhere:
`model.fit(X, y)` then `model.predict(X)`. You have perfect data: **predict lead conversion** from
your CRM features.

### A complete, real project (lead-conversion classifier)
```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.preprocessing import StandardScaler
from sklearn.pipeline import Pipeline
from sklearn.metrics import classification_report, roc_auc_score

# 1. data: features about each lead, label = did it convert (1/0)
df = pd.read_csv("/mnt/raid1/exports/leads.csv")
X = df[["lead_source_code", "response_time_min", "deal_value", "num_followups"]]
y = df["converted"]

# 2. split — never evaluate on data you trained on
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# 3. pipeline: scale features, then classify
model = Pipeline([
    ("scale", StandardScaler()),
    ("clf", RandomForestClassifier(n_estimators=200, random_state=42)),
])
model.fit(X_train, y_train)            # train

# 4. evaluate
preds = model.predict(X_test)
print(classification_report(y_test, preds))                 # precision/recall/f1
print("AUC:", roc_auc_score(y_test, model.predict_proba(X_test)[:, 1]))

# 5. feature importance — which signals drive conversion (great for the business!)
importances = sorted(zip(X.columns, model.named_steps["clf"].feature_importances_),
                     key=lambda t: -t[1])
print(importances)
```

### Key terms to own
- **Supervised learning** (labeled), **features (X) / target (y)**, **train/test split**,
  **overfitting** (memorizing train data; detect via train-vs-test gap), **cross-validation**,
  **classification vs. regression**, **pipeline**, **feature scaling/engineering**,
  **hyperparameters**, **metrics: precision/recall/F1, ROC-AUC, confusion matrix**,
  **class imbalance**, **feature importance**.
- Models to name: **RandomForest, GradientBoosting/XGBoost, LogisticRegression, SVM, KMeans
  (clustering), PCA (dimensionality reduction)**.

### Testing ML code
- Test the **plumbing**, not model accuracy: shapes, no data leakage, pipeline runs, predictions in
  range.
```python
def test_pipeline_outputs_binary(trained_model, sample_X):
    preds = trained_model.predict(sample_X)
    assert set(preds).issubset({0, 1})
    assert len(preds) == len(sample_X)
```
- Pin `random_state` for reproducibility. Validate input schemas with **Pydantic/pandera**.

### Common libraries
**scikit-learn, pandas, NumPy** (the core trio), **XGBoost/LightGBM** (gradient boosting — wins
Kaggle), **matplotlib/seaborn** (viz), **joblib** (save/load models), **pandera** (data validation).

### Honest soundbite
"I built a lead-conversion classifier on our CRM data with scikit-learn — RandomForest in a pipeline
with scaling, train/test split, evaluated on precision/recall and AUC, and used feature importance to
tell sales which signals matter. That's real, applied ML that drove a business decision."

---

## PART 2 — PyTorch (learn enough to discuss, don't overclaim)

### Mental model
Deep-learning framework. Core objects: **tensors** (n-dim arrays, like NumPy but GPU-accelerated +
**autograd** for gradients). You define a model (`nn.Module`), a **loss**, an **optimizer**, and
loop: forward → loss → `backward()` (autograd) → `optimizer.step()`.

### The canonical training loop (understand every line)
```python
import torch
import torch.nn as nn

model = nn.Sequential(nn.Linear(4, 16), nn.ReLU(), nn.Linear(16, 1), nn.Sigmoid())
loss_fn = nn.BCELoss()
optimizer = torch.optim.Adam(model.parameters(), lr=1e-3)

for epoch in range(100):
    optimizer.zero_grad()          # clear old gradients
    preds = model(X)               # forward pass
    loss = loss_fn(preds, y)       # compute loss
    loss.backward()                # autograd: compute gradients
    optimizer.step()               # update weights
```

### Key terms to own
- **Tensor, autograd/backpropagation, gradient, loss function, optimizer (Adam/SGD), epoch/batch,
  learning rate, forward/backward pass, activation (ReLU/sigmoid), overfitting, GPU/CUDA, layers
  (`nn.Linear`, `nn.Conv2d`), training vs. eval mode.**

### Testing
- Assert output **shapes** and that loss **decreases** over a few steps on a tiny fixture; use
  `torch.manual_seed()`. `pytest` as usual.

### TensorFlow / Keras & JAX (one paragraph each — name-level only)
- **TensorFlow/Keras:** Google's framework; **Keras** is the high-level API (`model.compile/fit`).
  Conceptually parallel to PyTorch. Historically more production/deployment tooling (TF Serving,
  TFLite).
- **JAX:** NumPy + autodiff + JIT compilation (`jax.grad`, `jax.jit`, `vmap`); functional style;
  popular in research. Know it as "composable function transforms over NumPy-like arrays."

### Honest soundbite (the whole AI section)
"My production AI work is LLM **inference and RAG** — Llama 3.1 via Ollama, LangChain, FAISS — not
training deep nets. I've done applied ML with scikit-learn on our real data, and I understand the
PyTorch training loop — tensors, autograd, optimizers — conceptually. I'm precise about that line
because I'd rather be the person who deeply understands RAG and retrieval than someone who claims to
train models they don't." **← This answer earns trust. Use it.**
