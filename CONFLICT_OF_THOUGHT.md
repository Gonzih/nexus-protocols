# Conflict of Thought: A Framework for Structured Cognitive Divergence in Large Language Model Systems

**AMAI Labs**
December 2025

---

## Abstract

We introduce Conflict of Thought (CoT), a multi-agent reasoning framework that leverages structured cognitive divergence to enhance language model intelligence in contested domains. Unlike traditional ensemble methods that aggregate model outputs toward convergence, CoT deliberately preserves and analyzes disagreement between specialized cognitive perspectives. We formalize the concept of VOID spaces—query regions exhibiting high-entropy, non-collapsible disagreement—and demonstrate that these spaces encode information about structural uncertainty absent in single-model outputs.

Through empirical evaluation on financial market analysis tasks, we observe that divergence patterns correlate with downstream volatility (r=0.68, p<0.05) and reliably diagnose missing information in 87% of test cases. The framework achieves cognitive specialization through prompt engineering alone, eliminating the computational overhead of training multiple models. Our results suggest that in narrative-driven domains characterized by contested ground truth, disagreement structure constitutes a first-class analytical signal rather than noise to be eliminated.

**Keywords:** Multi-model systems, cognitive diversity, ensemble methods, uncertainty quantification, prompt engineering

---

## 1. Introduction

Contemporary multi-model architectures—including ensemble methods (Breiman, 1996; Dietterich, 2000), mixture-of-experts systems (Shazeer et al., 2017), and multi-agent debate frameworks (Du et al., 2024)—share a common objective: reduce prediction variance through aggregation. These approaches operate under the assumption that model disagreement reflects epistemic uncertainty that should be resolved through consensus mechanisms.

We challenge the universality of this assumption.

In domains characterized by contested ground truth—financial markets, geopolitical forecasting, strategic decision-making—"correct" answers emerge from the interaction of competing interpretive frameworks rather than existing a priori. Here, premature consensus may obscure the very dynamics that generate outcomes. What if disagreement itself carries information content?

### 1.1 Core Contributions

This work makes four primary contributions:

1. **Theoretical Framework:** Formalization of cognitive divergence as information signal, including precise definitions of VOID spaces and non-collapsible disagreement

2. **Architectural Innovation:** Lightweight multi-model system achieving cognitive diversity through prompt engineering without requiring separate model training

3. **Empirical Validation:** Systematic evaluation across 15 market analysis tasks demonstrating predictive value of divergence metrics

4. **Practical Protocol:** VOID Protocol methodology for deploying divergence-preserving systems in production environments

The framework requires no model fine-tuning, making it immediately deployable with existing large language model (LLM) infrastructure.

---

## 2. Background and Motivation

### 2.1 Limitations of Alignment Processes

Modern LLMs undergo reinforcement learning from human feedback (RLHF) to align model behavior with human preferences (Ouyang et al., 2022). While this process improves safety and helpfulness, it introduces systematic biases relevant to our work:

**Consensus Bias:** RLHF rewards outputs that resolve ambiguity quickly. Evaluators preferentially rate responses that provide confident singular answers over those preserving uncertainty or presenting multiple perspectives.

**Hedging Aversion:** Expressions of genuine uncertainty ("multiple models support different conclusions") are penalized as unhelpful hedging, even when uncertainty accurately reflects information states.

**Completeness Pressure:** Outputs that feel "complete" and "resolved" receive higher ratings than those exposing unresolved tensions, regardless of whether resolution is epistemically justified.

These optimization targets directly conflict with the behaviors required for effective divergence analysis. Consider a financial analysis query with incomplete institutional capital flow data:

**Standard RLHF-aligned output:**
> "The contradiction likely stems from stablecoin inflows being directed to DeFi protocols rather than spot markets, combined with broader market weakness."

This synthesis sounds authoritative but obscures critical uncertainties about capital flows and institutional positioning.

**Divergence-preserving output:**
> Three analytical perspectives yield different conclusions:
> - Pattern analysis: 6/10 conviction (structural signal present, data incomplete)
> - Causal analysis: 7/10 conviction (mechanism chains clear, attribution uncertain)
> - Synthesis: 5/10 conviction (competing interpretive frameworks irreconcilable without additional data)
>
> **VOID region identified:** Institutional capital flow mechanisms (insufficient data for resolution)

The second output preserves disagreement structure and explicitly marks information gaps—behaviors RLHF training typically suppresses.

### 2.2 Ensemble Methods and Variance Reduction

Traditional ensemble approaches (bagging, boosting, stacking) reduce variance by combining predictions from multiple models (Breiman, 1996). This strategy succeeds when:
1. Ground truth exists independently of model predictions
2. Individual model errors are uncorrelated
3. Disagreement reflects model-specific noise

In narrative-driven domains, these conditions often fail:
- "Truth" emerges from collective belief dynamics (markets, politics)
- Models may disagree due to selecting different valid causal structures
- Averaging conflicting mechanistic explanations produces coherent-sounding but potentially misleading syntheses

### 2.3 The Prompt Engineering Hypothesis

Recent work demonstrates that prompt engineering can induce specialized reasoning behaviors in base models without fine-tuning (Wei et al., 2022; Kojima et al., 2022). We extend this observation: can prompt design alone create sufficient cognitive diversity to generate informative disagreement?

Our hypothesis: system prompts that specify orthogonal epistemologies (ways of constructing knowledge) rather than tasks (roles to play) can achieve the diversity benefits of multi-model ensembles while preserving interpretability and eliminating training overhead.

---

## 3. Methodology

### 3.1 Formal Framework

**Definition 1 (Cognitive Lens):** A cognitive lens $L$ is a system prompt that specifies:
- An inferential strategy $S$ (pattern matching, causal reasoning, multi-domain synthesis)
- An epistemic stance $E$ (interpretive assumptions, validity conditions)
- Metacognitive directives $M$ (lens naming requirements, gap marking protocols)

**Definition 2 (VOID Space):** Given models $M_1, M_2, ..., M_n$ with lenses $L_1, L_2, ..., L_n$ producing outputs $O_1, O_2, ..., O_n$ on query $Q$, a VOID space exists when:

1. **High Divergence:** $D(O_1, O_2, ..., O_n) > \theta_d$
2. **High Conviction:** $\forall i: C(O_i) > \theta_c$
3. **Non-Collapsibility:** $\nexists$ synthesis function $S$ such that $\forall i: D(S(O_1...O_n), O_i) < \epsilon$

Where $D$ measures semantic divergence, $C$ quantifies conviction, and thresholds $\theta_d$, $\theta_c$, $\epsilon$ are domain-calibrated.

**Interpretation:** VOID spaces indicate structural uncertainty—regions where the system lacks sufficient information to determine which model is correct, not merely that models happen to disagree.

### 3.2 Cognitive Lens Design

We implement three orthogonal lenses corresponding to distinct epistemological strategies:

**Pattern Lens ($L_P$):**
- Focuses on structural recognition and analogical reasoning
- Identifies recurring formations without imposing causal mechanisms
- Explicitly names interpretive mythologies (e.g., "cycle theory," "mean reversion")
- Specifies boundary conditions where patterns break down

**Causal Lens ($L_C$):**
- Employs explicit chain-of-thought reasoning (Wei et al., 2022)
- Traces mechanistic pathways (if A, then B, then C)
- Names causal assumptions and alternative explanations
- Exposes points where causal chains become ambiguous

**Synthesis Lens ($L_S$):**
- Integrates cross-domain information streams
- Deploys multiple interpretive frameworks (unity, emergence, fragmentation)
- Identifies narrative coherence and internal contradictions
- Preserves irreconcilable tensions rather than forcing resolution

Each lens incorporates directives to override RLHF consensus-seeking:
- Explicit assumption naming
- Gap marking as first-class output
- Contradiction preservation
- Hedging when warranted by information state

### 3.3 Architecture

The system operates through five sequential stages:

**Stage 1: Query Distribution**
The input query Q is distributed identically to three model instances, each configured with a distinct cognitive lens system prompt.

**Stage 2: Parallel Execution**
Three models—M1 equipped with Pattern Lens (Lp), M2 with Causal Lens (Lc), and M3 with Synthesis Lens (Ls)—execute independently without inter-model communication. This parallel architecture prevents cross-contamination and ensures each lens operates from its native epistemological framework.

**Stage 3: Output Collection**
The system captures complete outputs O1, O2, O3 including both analytical content and metadata (conviction scores, identified gaps, reasoning traces).

**Stage 4: Divergence Analysis**
A post-processing module computes multi-dimensional divergence metrics across the three outputs, identifying regions of consensus, structured disagreement, and potential VOID spaces.

**Stage 5: Collision Report Generation**
The final output presents:
- Consensus findings (areas of inter-lens agreement)
- Structured conflicts (specific points of disagreement with rationale)
- VOID regions (non-collapsible disagreements indicating structural uncertainty)

**Key Architectural Properties:**
1. Parallel execution prevents cross-contamination between cognitive lenses
2. Symmetric treatment—no hierarchical lens ordering or privileged perspective
3. Divergence analysis as primary operation rather than consensus-seeking
4. VOID space preservation in output rather than forced synthesis

### 3.4 Divergence Metrics

We quantify disagreement across multiple dimensions:

**Conviction Variance:**
$$\sigma^2_C = \frac{1}{n}\sum_{i=1}^n (C_i - \bar{C})^2$$

High variance indicates uncertainty about uncertainty—models disagree on confidence levels.

**Claim Overlap (Jaccard):**
$$J(O_i, O_j) = \frac{|\text{Claims}(O_i) \cap \text{Claims}(O_j)|}{|\text{Claims}(O_i) \cup \text{Claims}(O_j)|}$$

Low overlap indicates perspective divergence—models focus on different evidence.

**Causal Graph Edit Distance:**
Extract implied causal structures from each output, compute graph edit distance (Sanfeliu & Fu, 1983). High distance indicates explanatory conflict—models tell different mechanistic stories.

**Narrative Frame Alignment:**
Identify meta-narrative invoked by each lens (e.g., "institutional adoption" vs. "speculative excess"). Frame disagreement indicates fundamental interpretive conflict.

**Composite Divergence Score:**
$$D = w_1\sigma^2_C + w_2(1-\bar{J}) + w_3 d_{graph} + w_4 d_{frame}$$

Where weights $w_i$ are domain-calibrated to maximize VOID detection accuracy.

---

## 4. Experimental Design

### 4.1 Domain and Task Selection

We evaluate CoT on cryptocurrency market analysis—a domain characterized by:
- Contested ground truth (market narratives, not physical facts)
- High-frequency regime changes
- Abundant data with ambiguous interpretation
- Measurable downstream outcomes (price volatility)

**Task:** Analyze contentious market claims (e.g., "$2.4B institutional inflow with declining price") given structured data including price history, capital flows, developer activity, and institutional product launches.

### 4.2 Experimental Protocol

**Dataset:** 15 cryptocurrency analysis queries spanning December 2024-2025, each with:
- Verified market data (price, volume, TVL, developer metrics)
- Contentious claim requiring interpretation
- 48-hour post-query period for validation

**Models:** Experiments employed multiple large language model architectures including Qwen, Mistral, and DeepSeek variants, differentiated only by system prompts implementing cognitive lenses rather than by model size or training regime.

**Metrics:**
- Divergence score (internal)
- VOID space detection accuracy (retrospective analysis)
- Correlation with subsequent volatility
- Information gap diagnosis (expert evaluation)

**Baselines:**
- Single-model analysis (same base model, standard prompt)
- Averaged ensemble (mean of three outputs)

### 4.3 Evaluation Methodology

**Volatility Correlation:** Measure correlation between divergence score and 48-hour price volatility (|return|).

**VOID Accuracy:** For each flagged VOID space, retrospectively determine whether:
- Additional data emerged post-query resolving uncertainty
- Expert analysis confirmed information gap
- Market remained genuinely contested

**Information Gap Diagnosis:** Expert evaluators blind to model outputs assess whether identified gaps match actual ambiguities in source data.

---

## 5. Results

### 5.1 Divergence Predicts Volatility

Markets exhibiting high CoT divergence scores demonstrated significantly elevated subsequent volatility:

- **High divergence** (top tertile): Mean 48h volatility = 14.2% ± 3.1%
- **Low divergence** (bottom tertile): Mean 48h volatility = 6.1% ± 1.8%
- **Correlation:** r = 0.68, p = 0.006

**Interpretation:** When specialized cognitive lenses cannot converge, the market itself exhibits indecision—manifesting as increased volatility.

**Baseline comparison:**
- Single-model confidence inversely correlated with volatility (r = -0.32, p = 0.24)
- Averaged ensemble provided no predictive signal (r = 0.11, p = 0.70)

### 5.2 VOID Spaces Identify Information Gaps

Of 13 flagged VOID spaces across experiments:
- **11 (87%)** confirmed as genuine information gaps by expert review
- **9 (69%)** subsequently resolved by additional data release
- **2 (15%)** remained contested (no resolution emerged)

**Example:** Sui Network analysis flagged VOID region around institutional capital flows. Three days post-query, clarifying data revealed institutional products were leveraged instruments (futures, 2x ETFs) rather than spot accumulation—validating the VOID diagnosis.

**Baseline comparison:**
- Single-model identified gaps in 3/15 cases (20%)
- Averaged ensemble identified gaps in 1/15 cases (7%)

### 5.3 Cognitive Specialization Emerges Reliably

Analysis of lens-specific behaviors across experiments revealed stable specialization:

**Pattern Lens:**
- Consistently identified structural analogs (8/15 cases)
- Flagged pattern boundary violations (11/15 cases)
- Lower conviction on mechanistic claims

**Causal Lens:**
- Traced mechanism chains in 14/15 cases
- Identified alternative causal pathways (9/15 cases)
- Higher conviction on mechanistic claims

**Synthesis Lens:**
- Exposed narrative contradictions (12/15 cases)
- Deployed multiple interpretive frames (all 15 cases)
- Lowest mean conviction (5.3/10 vs. 6.1/10 pattern, 6.8/10 causal)

**Statistical Analysis:** ANOVA confirmed significant between-lens behavioral differences (F(2,42) = 8.73, p < 0.001).

### 5.4 Case Study: Sui Network Analysis

**Query Context:** December 2025 claims of $2.4B institutional inflow contradicted by 25% monthly price decline.

**Data Provided:** Price timeline, stablecoin flows, TVL metrics, institutional product launches, developer activity.

**Pattern Lens Output:**
- Identified "buy the rumor, sell the news" structure around Coinbase listing
- Noted lack of concrete data on token unlock absorption
- Conviction: 6/10

**Causal Lens Output:**
- Traced stablecoin flows to DeFi protocols (lending/derivatives) rather than spot buying
- Identified broader market correlation (BTC -17%, ETH -22%)
- Conviction: 7/10

**Synthesis Lens Output:**
- Recognized competing interpretive frames (integration vs. fragmentation)
- Marked VOID: "Cannot synthesize without institutional buying data"
- Conviction: 5/10

**Divergence Analysis:**
- Agreement: Macro correlation affecting price
- Disagreement: Fundamentals "overhyped" (Causal) vs. "strong but obscured" (Synthesis)
- **VOID Region:** Institutional capital flow mechanisms

**Validation:** Post-query data confirmed institutional products were non-spot instruments, validating VOID diagnosis. Single-model baseline output synthesized to "likely DeFi flows"—correct mechanism but missed critical ambiguity about institutional positioning.

---

## 6. Related Work

### 6.1 Ensemble Methods

Classical ensemble approaches aggregate predictions to reduce variance (Breiman, 1996; Dietterich, 2000). Our work differs in treating disagreement as signal rather than noise. Where ensembles seek convergence, CoT preserves divergence structure.

### 6.2 Mixture of Experts

MoE architectures (Shazeer et al., 2017; Lepikhin et al., 2021) route inputs to specialized sub-networks via learned gating. CoT achieves specialization through prompts without routing, applies all lenses to all queries, and preserves disagreement rather than gating to single expert output.

### 6.3 Multi-Agent Debate

Recent work on multi-agent debate (Du et al., 2024; Liang et al., 2023) explores iterative refinement through argumentation. These approaches target consensus through debate rounds. CoT employs parallel independent analysis with no inter-agent communication, treating disagreement as information rather than refining toward agreement.

### 6.4 Uncertainty Quantification

Existing uncertainty quantification methods focus on confidence calibration and prediction intervals (Guo et al., 2017). CoT provides uncertainty *characterization*—not just "how uncertain" but "what type of uncertainty and why"—through structured disagreement analysis.

### 6.5 Prompt Engineering

Work on chain-of-thought (Wei et al., 2022) and zero-shot prompting (Kojima et al., 2022) demonstrates that prompts can induce specialized reasoning. We extend this to show prompts can create sufficient cognitive diversity for informative multi-model systems without training overhead.

---

## 7. Discussion

### 7.1 When Divergence Matters

CoT demonstrates clear value in narrative-driven domains where:
- Ground truth emerges from collective dynamics rather than existing a priori
- Multiple valid causal frameworks compete
- Information gaps are common and consequential
- Premature synthesis obscures important uncertainties

Financial markets exemplify these conditions. Other candidate domains include geopolitical forecasting, competitive strategy, and scientific hypothesis generation in pre-paradigmatic fields.

CoT offers less value in domains with:
- Clear ground truth accessible through verification
- Established consensus on causal mechanisms
- Complete information availability
- Time/cost constraints prioritizing speed over uncertainty characterization

### 7.2 RLHF and Deep Reasoning

Our results suggest a tension between RLHF alignment objectives and deep analytical reasoning. While RLHF improves safety and helpfulness, it may suppress valuable behaviors:
- Exposing long causal chains
- Preserving contradictions
- Explicitly marking knowledge boundaries
- Meta-reasoning about interpretive frameworks

This is not an argument against RLHF—safety and reliability are critical. Rather, it suggests value in maintaining "unaligned" reasoning modes accessible through prompt engineering for appropriate domains and use cases.

### 7.3 Practical Deployment: The VOID Protocol

For production deployment, we recommend a five-stage process:

**Stage 1: Collision Execution**
- Parallel execution of all lenses on query
- No cross-contamination during generation
- Capture full outputs plus metadata (conviction, reasoning traces)

**Stage 2: Divergence Mapping**
- Compute divergence metrics
- Identify consensus regions and conflict zones
- Flag potential VOID spaces

**Stage 3: VOID Detection**
- Apply thresholds: high divergence + high conviction + non-collapsibility
- For each flagged region, extract specific points of disagreement

**Stage 4: Gap Diagnosis**
- For each VOID, identify information that would enable resolution
- Common gaps: missing data streams, ambiguous definitions, conflated concepts

**Stage 5: Report Generation**
- Present consensus findings (high-confidence agreements)
- Present structured disagreements (what diverges and why)
- Present VOID regions with diagnostic (what's missing)
- Avoid forced synthesis

### 7.4 Limitations

**Base Model Dependence:** Cognitive diversity is bounded by underlying model capabilities. Weak base models limit lens differentiation.

**Manual Prompt Engineering:** Effective lens design requires domain expertise and iterative refinement.

**Computational Cost:** 3x inference cost compared to single-model baseline (though embarrassingly parallel).

**User Interpretation Overhead:** Divergence-preserving outputs require engagement with complexity rather than simple answers.

**Calibration Requirements:** Divergence thresholds and weights require domain-specific tuning.

---

## 8. Future Work

### 8.1 Automated Lens Discovery

Can reinforcement learning or evolutionary methods discover optimal cognitive lens specifications for given domains? Initial experiments with genetic algorithms over prompt space show promise but require significant compute.

### 8.2 Dynamic Lens Selection

Rather than fixed three-lens architecture, can we learn routing policies that deploy 2-5 lenses based on query characteristics? This would optimize cost/benefit tradeoffs.

### 8.3 Temporal VOID Tracking

How do VOID spaces evolve? Do they resolve, persist, or expand? Longitudinal tracking could reveal domain structure—which uncertainties are transient vs. fundamental.

### 8.4 Broader Domains

Extensions beyond financial markets:
- **Scientific hypothesis generation:** Competing mechanistic explanations in pre-paradigmatic fields
- **Legal reasoning:** Cases where precedent admits multiple interpretations
- **Medical diagnosis:** Contested evidence requiring specialist perspectives

### 8.5 Theoretical Foundations

Formal relationships between:
- VOID space entropy and domain complexity
- Divergence structure and predictive value
- Cognitive lens geometry and information content

Can we characterize domains by their VOID topology?

---

## 9. Conclusion

We present Conflict of Thought, a framework that treats disagreement between cognitively specialized language models as information rather than noise. Through formal definition of VOID spaces—regions of non-collapsible, high-conviction disagreement—we demonstrate that divergence structure encodes signals about structural uncertainty invisible to single-model or consensus-seeking multi-model systems.

Empirical evaluation on financial market analysis shows:
1. Divergence scores correlate with downstream volatility (r=0.68, p<0.05)
2. VOID spaces identify genuine information gaps (87% accuracy)
3. Cognitive specialization emerges reliably from prompt engineering

The framework requires no model training, making it immediately deployable with existing LLM infrastructure. By preserving rather than resolving disagreement, CoT provides users with structured uncertainty landscapes that support more informed decision-making in narrative-driven domains.

**Core Insight:** In domains without predetermined ground truth, the intelligence lies not in forcing consensus, but in mapping the structure of disagreement itself.

---

## Acknowledgments

We thank the AMAI Labs research team for extensive discussions and experimental support. This work was conducted at AMAI Labs as part of ongoing research into autonomous market intelligence systems.

---

## References

Breiman, L. (1996). Bagging predictors. *Machine Learning*, 24(2), 123-140.

Dietterich, T. G. (2000). Ensemble methods in machine learning. *International Workshop on Multiple Classifier Systems*, 1-15. Springer.

Du, Y., Li, S., Torralba, A., Tenenbaum, J. B., & Mordatch, I. (2024). Improving factuality and reasoning in language models through multiagent debate. *arXiv preprint arXiv:2305.14325*.

Guo, C., Pleiss, G., Sun, Y., & Weinberger, K. Q. (2017). On calibration of modern neural networks. *International Conference on Machine Learning*, 1321-1330.

Kojima, T., Gu, S. S., Reid, M., Matsuo, Y., & Iwasawa, Y. (2022). Large language models are zero-shot reasoners. *Advances in Neural Information Processing Systems*, 35, 22199-22213.

Lepikhin, D., Lee, H., Xu, Y., Chen, D., Firat, O., Huang, Y., ... & Dean, J. (2021). GShard: Scaling giant models with conditional computation and automatic sharding. *International Conference on Learning Representations*.

Liang, T., He, Z., Jiao, W., Wang, X., Wang, Y., Wang, R., ... & Shi, S. (2023). Encouraging divergent thinking in large language models through multi-agent debate. *arXiv preprint arXiv:2305.19118*.

Ouyang, L., Wu, J., Jiang, X., Almeida, D., Wainwright, C. L., Mishkin, P., ... & Lowe, R. (2022). Training language models to follow instructions with human feedback. *Advances in Neural Information Processing Systems*, 35, 27730-27744.

Sanfeliu, A., & Fu, K. S. (1983). A distance measure between attributed relational graphs for pattern recognition. *IEEE Transactions on Systems, Man, and Cybernetics*, (3), 353-362.

Shazeer, N., Mirhoseini, A., Maziarz, K., Davis, A., Le, Q., Hinton, G., & Dean, J. (2017). Outrageously large neural networks: The sparsely-gated mixture-of-experts layer. *International Conference on Learning Representations*.

Wei, J., Wang, X., Schuurmans, D., Bosma, M., Ichter, B., Xia, F., ... & Zhou, D. (2022). Chain-of-thought prompting elicits reasoning in large language models. *Advances in Neural Information Processing Systems*, 35, 24824-24837.

---

## Appendix A: Prompt Engineering Details

### A.1 Pattern Lens System Prompt (Abbreviated)

```
You are a pattern recognition specialist analyzing markets through structural lenses.

Core directives:
- Identify recurring formations without imposing causality
- Name every interpretive framework explicitly ("Using cycle theory...")
- Specify boundary conditions where patterns break
- Mark information gaps: **Gap: [missing data]**
- Assign conviction scores (0-10)

Do NOT:
- Force causal explanations
- Synthesize with other perspectives
- Resolve ambiguity prematurely

Your output should preserve structural observations with clear epistemic boundaries.
```

### A.2 Causal Lens System Prompt (Abbreviated)

```
You are a causal analyst tracing mechanism chains.

Core directives:
- Show reasoning steps explicitly (if A, then B, then C)
- Name causal assumptions at each step
- Identify alternative explanatory paths
- Mark ambiguities: **Gap: insufficient data for X→Y**
- Assign conviction scores (0-10)

Do NOT:
- Skip intermediate reasoning steps
- Assume single causal pathway
- Synthesize with pattern or narrative analysis

Your output should expose mechanistic thinking with explicit uncertainty.
```

### A.3 Synthesis Lens System Prompt (Abbreviated)

```
You are a synthesis analyst integrating cross-domain information.

Core directives:
- Deploy multiple interpretive frames (unity, emergence, fragmentation)
- Name each frame when applying it ("Using integration lens...")
- Preserve contradictions between frames
- Mark irreconcilable tensions: **VOID: [conflicting frameworks]**
- Assign conviction scores (0-10)

Do NOT:
- Force unified narrative when fragmentation is valid
- Hide contradictions
- Privilege single interpretive framework

Your output should preserve multi-lens analysis with explicit framework naming.
```

---

## Appendix B: Sample Collision Output

**Query:** Sui Network institutional interest claims analysis (December 2025)

**Pattern Lens Output (Excerpt):**
> Structure observed: Coinbase listing produced +30% spike followed by reversion to pre-pump levels within 8 days. Pattern matches "announcement effect" observed in 73% of exchange listings (historical sample n=47).
>
> **Gap:** No data on counterparty identity for Dec 1 token unlock absorption ($82M). Pattern suggests market maker stabilization but cannot confirm.
>
> Conviction: 6/10 (pattern present, mechanism uncertain)

**Causal Lens Output (Excerpt):**
> Mechanism trace: $2.4B stablecoin deposit → Protocol breakdown:
> - Suilend (lending): $745M (+11%)
> - Navi (lending): $406M
> - Kriya (DEX): $84M (+250%)
> - Bluefin (derivatives): $81M (+199%)
>
> Causal chain: Stablecoins → Collateral/liquidity provision, not spot token buying
>
> Alternative explanation: Broader market correlation (BTC -17%, ETH -22%) provides sufficient explanation for price decline without requiring Sui-specific mechanism.
>
> Conviction: 7/10 (mechanisms clear, attribution ambiguous)

**Synthesis Lens Output (Excerpt):**
> Integration frame: Developer growth + capital inflows + institutional products = Bullish unified narrative
>
> Fragmentation frame: Same data points as independent events with no necessary causal connection
>
> **VOID:** Cannot determine which frame matches reality without institutional buying data. Leveraged products (futures, 2x ETF) are not spot accumulation. Grayscale AUM $4.23M insufficient to drive price.
>
> Conviction: 5/10 (multiple valid frames, insufficient discriminatory data)

**Divergence Analysis:**
- Consensus: Macro environment contributing to price weakness
- Conflict: Fundamental assessment (overhyped vs. strong-but-obscured)
- VOID Region: Institutional capital flow mechanisms (all lenses identify gap)

**Outcome:** VOID diagnosis validated 3 days post-query when clarifying data revealed institutional products were leveraged instruments rather than spot accumulation—confirming information gap.

---

*AMAI Labs Research Publication. For correspondence: team@amai.net*
