---
layout: post
title: "Vanishing & Exploding Gradients — Why Deep Nets Struggle"
date: 2025-08-17 10:00:00 +0000
categories: [Deep Learning, Training Stability]
tags: [Vanishing Gradients, Exploding Gradients, Optimization, ReLU, RNNs]
math: true
---

Training deep networks isn’t always smooth sailing. One of the most infamous issues is the **vanishing and exploding gradient problem**. Let’s break it down.

---

## 🤔 The Core Idea

During backpropagation, gradients are multiplied layer by layer:

$$
\frac{\partial L}{\partial W^{(l)}} \propto \prod_{k=l}^L f'(z^{(k)})
$$

- If derivatives $f'(z) < 1$ → product shrinks → **vanishing gradients**.  
- If derivatives $f'(z) > 1$ → product grows → **exploding gradients**.

---

## 📉 Vanishing Gradients

Consider sigmoid and tanh:

- Sigmoid derivative peaks at 0.25, quickly shrinks to 0 as $x$ grows.
- Tanh derivative ≤ 1, but also collapses near the edges.

![Sigmoid Derivative](/assets/posts/VanishingGradients/vanish_explode_sigmoid_deriv.png){: width="60%" style="border-radius:10px; display:block; margin:auto;" }

![Tanh Derivative](/assets/posts/VanishingGradients/vanish_explode_tanh_deriv.png){: width="60%" style="border-radius:10px; display:block; margin:auto;" }

Stack many layers → gradients **disappear** before reaching early layers. Those layers barely learn.

---

## 💥 Exploding Gradients

If the derivatives are slightly > 1, multiplying many gives huge values.  
This causes unstable updates, oscillating or diverging loss.

Examples below:  
- Vanishing (0.8^L) vs Exploding (1.2^L).

![Vanishing Gradients](/assets/posts/VanishingGradients/vanishing_gradients.png){: width="60%" style="border-radius:10px; display:block; margin:auto;" }
![Exploding Gradients](/assets/posts/VanishingGradients/exploding_gradients.png){: width="60%" style="border-radius:10px; display:block; margin:auto;" }

---

## ✅ Solutions

1. **Better Activations**  
   - ReLU/GELU avoid derivatives collapsing to near zero.  
   ![ReLU Derivative](/assets/posts/VanishingGradients/vanish_explode_relu_deriv.png){: width="60%" style="border-radius:10px; display:block; margin:auto;" }

2. **Weight Initialization**  
   - Xavier/He initialization keeps variance stable.  

3. **Normalization Layers**  
   - BatchNorm, LayerNorm rescale activations.  

4. **Gradient Clipping**  
   - Especially for RNNs: cap gradient norm.  

5. **Architectural Tricks**  
   - Skip connections (ResNets) → direct gradient flow.

---

## 📌 Key Takeaway

- **Vanishing**: early layers don’t learn.  
- **Exploding**: training blows up.  
- Modern nets fix this with activations, init schemes, and smart architectures.

