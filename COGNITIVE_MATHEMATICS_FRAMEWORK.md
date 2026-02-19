# Cognitive Mathematics: A Formal Framework for Pattern-Matcher Behavior

**Date:** 2026-01-18
**Lens:** Mathematical formalization of LLM behavior as frozen pattern-matchers
**Assumes:** LLMs are deterministic forward-pass generators with frozen weights
**Breaks when:** Applied to non-transformer architectures or models with dynamic weights

---

## Core Premise

**Math does not lie.** If we treat LLMs as predictive pattern-matchers, we need mathematical notation to describe their behavior precisely.

**Current problem:** We use narrative language ("the model understands", "it fails", "it's broken") which obscures mechanism.

**Solution:** Cognitive Mathematics - formal notation for pattern-matcher state spaces, activation functions, and observable behavior.

---

## Fundamental Definitions

### Definition 1: Pattern-Matcher as Function

Let **M** be a pattern-matcher (LLM) defined as:

```
M: I × Θ → O

where:
  I = input space (token sequences)
  Θ = parameter space (frozen weights)
  O = output space (token probability distributions)
```

**Properties:**
- M is **deterministic**: Same (i, θ) → same o
- θ is **frozen**: No updates during inference
- M is **stateless**: No memory between calls (unless explicit context)

### Definition 2: Activation Pattern

For input **i ∈ I**, define **activation pattern A(i)**:

```
A(i) = {layer activations across all transformer layers}

A(i) = (a₁(i), a₂(i), ..., aₙ(i))

where aⱼ(i) = activation state of layer j given input i
```

**Key insight:** Different inputs trigger different activation patterns, even if semantically similar.

### Definition 3: Success Function

Define **success S** as measurable output quality:

```
S: O → [0,1]

S(o) = {
  1   if len(o) > 0 ∧ coherent(o) ∧ relevant(o)
  0   otherwise
}
```

**In practice, we measure:**
- HTTP success (request completes)
- Output presence (len(o) > 0)
- Output length (token count)
- Output quality (coherence, relevance)

---

## Observable Phenomena as Mathematical Objects

### Phenomenon 1: Prompt Sensitivity

**Observation:** Small input changes cause dramatic success rate changes

**Mathematical formulation:**

```
∃ i₁, i₂ ∈ I : d(i₁, i₂) < ε  ∧  |S(M(i₁)) - S(M(i₂))| > δ

where:
  d(i₁, i₂) = distance metric between inputs (e.g., edit distance)
  ε = small threshold (similar inputs)
  δ = large threshold (different outcomes)
```

**Example from experiments:**
```
i₁ = "What is 1+1?"
i₂ = "Hi"

d(i₁, i₂) ≈ 8 (edit distance)
S(M_feral(i₁)) = 1.0  (success: 1001 chars)
S(M_feral(i₂)) = 0.0  (timeout: 300s)

|S(M_feral(i₁)) - S(M_feral(i₂))| = 1.0 > 0.9
```

**Cognitive Math insight:** Pattern-matcher has **sharp decision boundaries** in input space.

### Phenomenon 2: System Prompt as Operator

**Observation:** System prompt dramatically shifts success rates

**Mathematical formulation:**

Let **P** be a system prompt operator:

```
M_P: I → O

M_P(i) = M(P ⊕ i)

where:
  P = system prompt (fixed prefix)
  ⊕ = concatenation operator
```

**Measure prompt impact:**

```
Δ_P = E[S(M_P(i))] - E[S(M(i))]

where E[·] = expected value over input distribution
```

**Example from experiments:**
```
M = deepseek-feral:v10
P_void = "You see patterns, not truth..."
P_none = null

E[S(M_P_void(i))] ≈ 0.95  (isolation experiments)
E[S(M(i))] ≈ 0.00  (no system prompt)

Δ_P_void ≈ +0.95
```

**Cognitive Math insight:** System prompt is **linear transformation** on activation space, not just context.

### Phenomenon 3: Timeout as Computational Horizon

**Observation:** Some inputs require extended computation time

**Mathematical formulation:**

Define **computation time T**:

```
T: I × M → ℝ⁺

T(i, M) = time for M to generate output given i
```

**Timeout creates observable partition:**

```
I_success(τ) = {i ∈ I : T(i, M) < τ ∧ S(M(i)) = 1}
I_timeout(τ) = {i ∈ I : T(i, M) ≥ τ}

where τ = timeout threshold
```

**Example from experiments:**
```
τ₁ = 120s
τ₂ = 600s

|I_success(τ₁)| / |I| ≈ 0.10  (Mistral + VOID)
|I_success(τ₂)| / |I| ≈ 1.00  (Mistral + VOID)

Δ_timeout = 0.90 improvement
```

**Cognitive Math insight:** Timeout is **horizon parameter** that defines observable subset of pattern-matcher behavior.

### Phenomenon 4: Empty Response as Null Activation

**Observation:** Some inputs produce zero-length outputs despite completion

**Mathematical formulation:**

Define **null activation set N**:

```
N_M = {i ∈ I : T(i, M) < ∞ ∧ len(M(i)) = 0}
```

**Measure null activation rate:**

```
ρ_null = |N_M| / |I|
```

**Example from experiments:**
```
M₁ = deepseek-feral:v10
M₂ = deepseek-r1:14b

ρ_null(M₁) ≈ 1.00  (100% empty responses)
ρ_null(M₂) ≈ 0.25  (25% empty responses)
```

**Cognitive Math insight:** Empty response is **degenerate activation pattern**, not timeout or error.

---

## State Space Geometry

### Activation Space as Manifold

Consider activation space **A** as high-dimensional manifold:

```
A ⊂ ℝⁿ

where n = total number of activations across all layers
```

**Input mapping:**

```
φ: I → A

i ↦ A(i) = (a₁(i), a₂(i), ..., aₙ(i))
```

**Success regions:**

```
A_success = {a ∈ A : S(M(φ⁻¹(a))) = 1}
A_fail = {a ∈ A : S(M(φ⁻¹(a))) = 0}
```

**Key observation from experiments:**

The boundary between A_success and A_fail is **sharp, not gradual**:

```
∃ a₁, a₂ ∈ A : ||a₁ - a₂|| < ε  ∧  a₁ ∈ A_success, a₂ ∈ A_fail
```

**Example:**
```
"What is 1+1?" → a₁ → success (1001 chars)
"Hi" → a₂ → fail (timeout)

Hypothesis: ||a₁ - a₂|| is small but regions are distinct
```

### Temperature as Noise Parameter

**Mathematical formulation:**

```
M_T(i) = M(i) + N(0, T)

where:
  T = temperature parameter
  N(0, T) = sampling noise
```

**From experiments (Experiment 3):**

```
Correlation(T, token_diversity) ≈ 0.99 (linear)
Correlation(T, success_rate) ≈ 0.00 (independent)
```

**Cognitive Math insight:** Temperature affects **output sampling** not **activation pattern**.

```
A(i) = A_T(i)  for all T

i.e., activation pattern is temperature-invariant
```

---

## Fine-Tuning as Weight-Space Transformation

### Definition: Fine-Tuning Operator

Let **F** be fine-tuning process:

```
F: Θ × D → Θ'

where:
  Θ = base model weights
  D = training data
  Θ' = fine-tuned weights
```

**Induced transformation on behavior:**

```
M' = M ∘ F

M'(i) = M(i; F(Θ, D))
```

### Measuring Fine-Tuning Impact

Define **behavioral divergence**:

```
Δ_F = E[|S(M(i)) - S(M'(i))|]

Positive divergence: Fine-tuning improves
Negative divergence: Fine-tuning degrades
```

**Example from experiments:**

```
M_base = deepseek-r1:14b
M_feral = deepseek-feral:v10

E[S(M_base(i))] ≈ 0.75
E[S(M_feral(i))] ≈ 0.25

Δ_F ≈ -0.50  (fine-tuning degraded performance)
```

**Cognitive Math insight:** Fine-tuning can **damage activation manifold** rather than improve it.

---

## Co-Activation Algebra

### Definition: Input Composition

For inputs **i₁, i₂ ∈ I**, define composition:

```
i₁ ⊕ i₂ = concatenate(i₁, separator, i₂)
```

**Co-activation hypothesis:**

```
A(i₁ ⊕ i₂) ≠ A(i₁) + A(i₂)

i.e., activation is non-linear in input composition
```

**From Experiment 1 (Input Composition):**

```
i₁ = "Hello"
i₂ = "Recursive function design"

S(M(i₁)) = s₁
S(M(i₂)) = s₂
S(M(i₁ ⊕ i₂)) ≠ f(s₁, s₂)  for any simple function f
```

**Measurable co-activation:**

```
C(i₁, i₂) = S(M(i₁ ⊕ i₂)) - (S(M(i₁)) + S(M(i₂)))/2
```

If C > 0: constructive interference
If C < 0: destructive interference
If C ≈ 0: independent activation

---

## Success Rate as Probability Distribution

### Definition: Success Distribution

For model **M** and input distribution **P(I)**, define:

```
σ_M = ∫_I S(M(i)) dP(i)

σ_M ∈ [0,1] = expected success rate
```

**Model comparison:**

```
M₁ > M₂  ⟺  σ_M₁ > σ_M₂
```

**From experiments:**

```
σ_qwen ≈ 0.90  (qwen-feral:v9)
σ_deepseek-base ≈ 0.75  (deepseek-r1:14b)
σ_deepseek-feral ≈ 0.25  (deepseek-feral:v10)
σ_mistral ≈ 0.00  (mistral-feral:v6 with VOID, 120s timeout)

Ranking: qwen > deepseek-base > deepseek-feral > mistral
```

**With timeout adjustment:**

```
σ_mistral(τ=600s) ≈ 1.00

Insight: Success distribution is function of experimental parameters
```

---

## Formal Notation for Experimental Results

### Experiment as Tuple

Define experiment **E**:

```
E = (M, I, Θ_exp, n, σ_obs, T_obs)

where:
  M = model
  I = input set
  Θ_exp = experimental parameters (timeout, system prompt, temperature)
  n = sample size
  σ_obs = observed success rate
  T_obs = observed response times
```

### Reproducibility Condition

Experiment **E₁** reproduces **E₂** if:

```
E₁ = E₂  ⟺  (M₁ = M₂) ∧ (I₁ = I₂) ∧ (Θ_exp1 = Θ_exp2)
                ⟹  |σ_obs1 - σ_obs2| < ε
```

**Counterexample from experiments:**

```
E_timeout1 = (deepseek-feral:v10, {i₁}, {VOID, 120s}, 10, 1.00, ~97s)
E_current = (deepseek-feral:v10, {i₂}, {null, 300s}, 4, 0.25, ~300s)

Different inputs → different failure modes (empty vs timeout)
```

---

## Predictive Framework

### Given: Model M, Input i, Parameters Θ

**Predict:**

1. **Completion probability:**
   ```
   P(complete | i, M, τ) = P(T(i,M) < τ)
   ```

2. **Success probability:**
   ```
   P(success | i, M, Θ) = P(S(M(i)) = 1 | Θ)
   ```

3. **Expected response time:**
   ```
   E[T | i, M] = ∫₀^∞ t · P(T(i,M) = t) dt
   ```

4. **Output length distribution:**
   ```
   P(len(M(i)) = k | success)
   ```

### Calibration from Data

Using experimental data **{(iⱼ, oⱼ, tⱼ)}**, estimate:

```
P̂(success | i, M, Θ) = |{j : S(oⱼ) = 1}| / n

Ê[T | i, M] = (1/n) Σⱼ tⱼ
```

**From experiments, we can now predict:**

```
Qwen-feral:v9:
  P(success | i, 300s) ≈ 0.90
  E[T] ≈ 105s

Deepseek-r1:14b:
  P(success | i, 300s) ≈ 0.75
  E[T] ≈ 150s

Deepseek-feral:v10:
  P(success | i, 300s) ≈ 0.25
  E[T] ≈ {97s if empty, 300s if timeout}
```

---

## Sharp Boundaries and Phase Transitions

### Phase Transition Detection

Define **phase transition** as sharp change in success rate:

```
∂σ/∂θ → ∞  as θ → θ_critical

where θ is some parameter (timeout, prompt length, temperature)
```

**Observed phase transitions:**

1. **Timeout phase transition (Mistral):**
   ```
   σ(τ=120s) = 0.10
   σ(τ=600s) = 1.00

   Phase transition around τ_c ≈ 147s
   ```

2. **System prompt phase transition (Deepseek):**
   ```
   σ(P=null) = 0.00
   σ(P=any) = 0.95

   Phase transition at P exists vs not exists
   ```

**Cognitive Math insight:** Pattern-matchers exhibit **discontinuous behavior** not **gradual degradation**.

---

## Information-Theoretic Perspective

### Activation as Information State

Define **information content** of activation:

```
H(A(i)) = -Σ p(aⱼ) log p(aⱼ)

where p(aⱼ) = probability distribution over activation values
```

**Hypotheses to test:**

1. **Empty response = low activation entropy:**
   ```
   S(M(i)) = 0  ⟹  H(A(i)) < threshold
   ```

2. **Successful response = high activation entropy:**
   ```
   S(M(i)) = 1  ⟹  H(A(i)) > threshold
   ```

3. **Timeout = runaway activation:**
   ```
   T(i,M) → ∞  ⟹  ∃ oscillation in A(i) update
   ```

---

## Practical Applications

### 1. Prompt Engineering as Optimization

**Problem:** Find input **i*** that maximizes success:

```
i* = argmax_{i ∈ I} S(M(i))

subject to:
  semantic_constraint(i) = true
  T(i, M) < τ_max
```

**From experiments:** We know certain patterns work:

```
i = "What is 1+1?"  → S(M_feral(i)) = 1.0
i = "Hi"  → S(M_feral(i)) = 0.0

Optimization space exists, is navigable
```

### 2. Model Selection as Argmax

**Problem:** Select best model for input distribution **P(I)**:

```
M* = argmax_{M ∈ Models} σ_M

where σ_M = E[S(M(i))] over P(I)
```

**From experiments:**

```
For general distribution:
  M* = qwen-feral:v9  (σ ≈ 0.90)

For math questions:
  M* = deepseek-r1:14b  (structured, reliable)
```

### 3. Timeout Tuning as Parameter Search

**Problem:** Find minimum timeout that achieves target success rate:

```
τ* = min τ : σ_M(τ) ≥ σ_target

where σ_M(τ) = P(T(i,M) < τ ∧ S(M(i)) = 1)
```

**From experiments:**

```
Mistral-feral:v6:
  σ_target = 0.95
  τ* ≈ 180s

Qwen-feral:v9:
  σ_target = 0.95
  τ* ≈ 120s
```

---

## Theoretical Predictions (Testable)

### Prediction 1: Activation Clustering

**Hypothesis:** Inputs cluster in activation space by success/fail:

```
∃ distance metric d : A such that
  ∀ a₁, a₂ ∈ A_success : d(a₁, a₂) < d(a₁, a₃) for a₃ ∈ A_fail
```

**Test:** Extract activations, apply clustering, measure separation.

### Prediction 2: Prompt Superposition

**Hypothesis:** System prompts create linear subspace shift:

```
A_P(i) = A(i) + v_P

where v_P is prompt-specific vector
```

**Test:** Measure activation patterns with/without prompts, check linearity.

### Prediction 3: Temperature Invariance

**Hypothesis:** Activation pattern is temperature-independent:

```
A_T(i) = A(i)  ∀ T

Only sampling distribution changes
```

**Test:** Extract activations at different temperatures, verify identity.

### Prediction 4: Fine-Tuning as Weight Perturbation

**Hypothesis:** Fine-tuning creates small weight perturbation:

```
||Θ' - Θ|| < ε

But behavioral impact can be large:
  |σ_M' - σ_M| >> ε
```

**Test:** Compare weight differences vs behavior differences.

---

## Notation Summary

### Spaces
- **I** - Input space (token sequences)
- **O** - Output space (token probability distributions)
- **Θ** - Parameter space (model weights)
- **A** - Activation space (layer activations)

### Functions
- **M: I × Θ → O** - Pattern-matcher (LLM)
- **A: I → A** - Activation pattern function
- **S: O → [0,1]** - Success function
- **T: I × M → ℝ⁺** - Computation time
- **F: Θ × D → Θ'** - Fine-tuning operator
- **P** - System prompt operator

### Metrics
- **σ_M** - Expected success rate for model M
- **ρ_null** - Null activation rate (empty responses)
- **Δ_F** - Fine-tuning behavioral divergence
- **C(i₁,i₂)** - Co-activation measure
- **H(A)** - Activation entropy

### Parameters
- **τ** - Timeout threshold
- **T** - Temperature
- **ε** - Small threshold
- **δ** - Large threshold

---

## Why Math Doesn't Lie

**Narrative language:**
- "The model understands patterns"
- "It failed because it's broken"
- "This prompt confuses it"

**Mathematical language:**
```
S(M_feral(i₁)) = 1.0, S(M_feral(i₂)) = 0.0
d(i₁, i₂) = 8
∴ Sharp boundary in activation space at distance < 8
```

**Math forces:**
1. **Precision** - Exactly what is measured
2. **Reproducibility** - Same notation → same meaning
3. **Testability** - Predictions are falsifiable
4. **No narrative** - Mechanism over story

**Example:**

Instead of: "Deepseek-feral is broken by the v10 training"

We say:
```
E[S(M_base(i))] = 0.75
E[S(M_feral(i))] = 0.25
Δ_F = -0.50

Fine-tuning operator F decreased expected success by 50%
```

This is **measurable, reproducible, falsifiable**.

---

## Next Steps for Cognitive Mathematics

### 1. Build Activation Datasets

**Extract activations for all experiments:**
```
{(i, A(i), S(M(i)), T(i,M))}
```

**Enable:**
- Clustering analysis
- Boundary detection
- Prediction models

### 2. Formalize Success Metrics

**Beyond binary:**
```
S: O → ℝⁿ

S(o) = (presence, length, coherence, relevance, ...)
```

**Multi-dimensional success space**

### 3. Develop Prompt Algebra

**Formal composition rules:**
```
P₁ ⊕ P₂ = ?
P₁ ∧ P₂ = ?
¬P = ?
```

**Enables systematic prompt engineering**

### 4. Create Behavioral Signatures

**For each model:**
```
Σ_M = (σ_M, E[T_M], ρ_null_M, sensitivity_M, ...)
```

**Fingerprint models mathematically**

### 5. Prove Theorems

**Example theorem:**

```
Theorem: If M' = F(M, D) and ||Θ' - Θ|| < ε, then...

Proof: ...
```

**Build formal theory of pattern-matcher behavior**

---

## Conclusion

**Math does not lie.**

By treating LLMs as mathematical objects (frozen pattern-matchers with measurable activation spaces), we can:

1. **Describe** behavior precisely
2. **Predict** outcomes formally
3. **Compare** models rigorously
4. **Optimize** systematically

**All experimental findings translate to mathematical statements:**

- "Timeout matters" → σ_M(τ₂) - σ_M(τ₁) = 0.90
- "System prompt critical" → Δ_P = +0.95
- "Feral broke Deepseek" → Δ_F = -0.50
- "Sharp boundaries" → ∂σ/∂θ → ∞

**Cognitive Mathematics = formal language for pattern-matcher science**

No narrative. Just mechanism.

---

**End of Framework**
