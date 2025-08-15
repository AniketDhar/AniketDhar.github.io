---
layout: post
title: "BatchNorm vs LayerNorm — Same Goal, Different Superpowers"
date: 2025-08-15 10:00:00 +0000
categories: [Deep Learning, Normalization]
tags: [BatchNorm, LayerNorm, Neural Networks, Transformers, CNNs]
math: true
---

Training a deep network can feel like trying to hit a moving target. As the early layers update, the distribution of values flowing into later layers keeps shifting. That constant drift forces later layers to re-learn the basics over and over. **Normalization** is our way of pinning that target down so each layer sees stable inputs and gradients behave.

---

### 🤔 Why Normalize at All?

Without normalization, activations can swing wildly in scale and offset from batch to batch. When that happens, gradients either explode or vanish, learning slows, and you end up babysitting learning rates and initializations.

Normalization keeps activations at a predictable scale, so optimizers can take confident steps, and downstream layers stop chasing a moving distribution.

---

### Batch Normalization (BN) - Normalize per Batch

BN looks across the **mini-batch** and says:  
> “For each feature, what’s the typical mean and spread right now?”

It then standardizes that feature using the batch statistics and adds learnable scale and shift so the model can still represent any distribution it wants.

✅ **How it works**:

Batch mean:
$$
\mu_j = \frac{1}{B} \sum_{i=1}^{B} x_{ij}
$$

Batch variance:
$$
\sigma^2_j = \frac{1}{B} \sum_{i=1}^{B} \big(x_{ij} - \mu_j\big)^2
$$

Standardize with ε for stability:
$$
\hat{x}_{ij} = \frac{x_{ij} - \mu_j}{\sqrt{\sigma_j^2 + \epsilon}}
$$

Learnable scale & shift (γ and β):
$$
y_{ij} = \gamma_j\, \hat{x}_{ij} + \beta_j
$$

📈 **Why BN shines**:
- Works well in CNNs
- Larger batches → stable statistics
- Implicit regularization → improved generalization
- Faster convergence

⚠️ **Where BN stumbles**:
- Tiny batches → noisy estimates
- Online learning (batch size = 1)
- Sequence models: per-timestep normalization with batch stats is awkward

---

### Layer Normalization (LN) — Normalize Per Sample

LN flips the axis. Instead of looking across the batch, it looks **across the features of a single example**.

Each sample is normalized independently. This makes its behavior **identical during training and inference** and **independent of batch size**.

✅ **How it works**:

Per‑sample mean:
$$
\mu_i = \frac{1}{F} \sum_{j=1}^{F} x_{ij}
$$

Per‑sample variance:
$$
\sigma^2_i = \frac{1}{F} \sum_{j=1}^{F} \big(x_{ij} - \mu_i\big)^2
$$

Standardize with ε:
$$
\hat{x}_{ij} = \frac{x_{ij} - \mu_i}{\sqrt{\sigma_i^2 + \epsilon}}
$$

Learnable scale & shift (per feature):
$$
y_{ij} = \gamma_j\, \hat{x}_{ij} + \beta_j
$$

📈 **Why LN shines**:
- Batch-size agnostic
- Great for variable-length sequences
- Stable during inference
- Default for Transformers & RNNs

⚠️ **When LN lags**:
- CNNs with large batches: BN may converge faster and achieve better accuracy

---
### Visual comparison

![BN vs LN comparison]({{ "/assets/posts/BNvsLN/bn_ln_visual.png" | relative_url }}){: width="720" }
*Figure: A toy activation matrix before normalization (left), after BatchNorm (middle), and after LayerNorm (right).*

---

### 🆚 When to Pick Which?

| Use Case                 | Recommended Norm |
|--------------------------|------------------|
| Large-batch CNN training | **BatchNorm**    |
| Small batches / NLP / RNNs/ Transformers | **LayerNorm** |

👉 If you're stuck with small batches in vision models, consider **GroupNorm** as a middle ground.

---

### 📌 The Takeaway

Both BN and LN chase the same goal — **stable distributions and reliable gradients** — but take different paths.  

> Match the normalization to the data shape and training setup, and you’ll get faster training, fewer hyperparameter headaches, and better generalization.

---


