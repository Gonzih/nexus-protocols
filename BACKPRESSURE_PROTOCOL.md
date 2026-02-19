# Backpressure Protocol: Installable Self-Correction for Claude Code

**Date:** 2026-01-18
**Source:** https://banay.me/dont-waste-your-backpressure/
**Status:** PROTOCOL - Install in any Claude Code instance
**Lens:** Backpressure as automated feedback mechanism for AI agents
**Application:** Task execution with self-correction loops, no human intervention at non-critical steps

---

## Installation Instructions

**To install this protocol in a Claude Code instance:**

1. Add to project's `.claude/CLAUDE.md`:
   ```markdown
   ## Backpressure Protocol

   See /path/to/BACKPRESSURE_PROTOCOL.md for full protocol.

   **Core principle:** Convert errors/failures from human intervention points to automated self-correction loops.

   **When executing tasks:**
   1. Define explicit success criteria before starting
   2. Add automated validation after each step
   3. Self-correct based on validation feedback
   4. Loop until criteria met OR true blocker (requires human)
   5. Only surface to human: final results or genuine blockers

   **Never:** Stop at first error and wait for human guidance
   **Always:** Attempt automated correction first
   ```

2. Reference this document when designing experiments, running tests, or executing multi-step tasks

3. Apply patterns from "Backpressure Patterns for Claude Code Tasks" section

---

## Core Concept: Backpressure as Distinction

**Backpressure** = Automated feedback system that enables self-correction without human intervention

**The Pattern:**
```
Agent Action → System Response → Agent Correction → Repeat until consistent
```

**NOT:**
```
Agent Action → Human Review → Human Fix → Next action
```

**The Distinction:**

- **Without backpressure:** Agent makes mistake → human catches it → human fixes it → wasted leverage
- **With backpressure:** Agent makes mistake → system catches it → agent fixes it → human only intervenes at true blockers

**Why it matters:** Backpressure converts agent mistakes from **friction** (human intervention needed) to **fuel** (automated correction loop).

---

## Backpressure in Cognitive Mathematics Notation

Using framework from `COGNITIVE_MATHEMATICS_FRAMEWORK.md`:

### Definition: Backpressure Function

Let **B** be backpressure function:

```
B: O → {valid, invalid} × ℝ

B(o) = (status, error_signal)

where:
  status ∈ {valid, invalid}
  error_signal = measure of deviation from desired state
```

### Self-Correction Loop

```
o₀ = M(i₀)                    Initial output
b₁ = B(o₀)                     Check backpressure

if b₁ = (invalid, e₁):
  i₁ = correction_prompt(i₀, e₁)
  o₁ = M(i₁)                   Corrected output
  b₂ = B(o₁)                   Check again

  Repeat until B(oₙ) = (valid, 0)
```

**Cognitive math insight:** Backpressure creates **convergence constraint** on agent behavior:

```
lim_{n→∞} error_signal(oₙ) = 0

Agent iterates until error minimized
```

---

## Types of Backpressure (From Blog Post)

### 1. Build System Backpressure

**Mechanism:** Let agent run builds and read error output

**Implementation in Claude Code:**

```python
# Agent writes code
agent.write_code("src/foo.py")

# Build system provides backpressure
result = subprocess.run(["python", "-m", "pytest"], capture_output=True)

if result.returncode != 0:
    # Backpressure signal
    error_output = result.stderr.decode()

    # Agent self-corrects
    agent.fix_code("src/foo.py", error_context=error_output)

    # Loop until tests pass
```

**Backpressure function:**
```
B_build(code) = {
  (valid, 0)          if all tests pass
  (invalid, stderr)   if tests fail
}
```

**Example from our experiments:**

When testing deepseek models, we could have added:
```python
# Instead of just running query
result = query_llm(prompt)

# Add backpressure
if len(result) == 0:
    # Empty response detected
    # Try with system prompt
    result = query_llm(prompt, system_prompt="You are helpful assistant")

# Loop until non-empty or max retries
```

### 2. Type System Backpressure

**Mechanism:** Use expressive type systems to prevent invalid states

**Implementation in Claude Code:**

```python
# Instead of:
def process_data(data):  # No type checking
    return data.process()

# Use:
from typing import Optional, List

def process_data(data: Optional[List[str]]) -> Result[Output, Error]:
    if data is None:
        return Error("Data cannot be None")

    # Type system provides backpressure
    # Agent must handle all cases
```

**Backpressure function:**
```
B_type(code) = {
  (valid, 0)           if type checker passes
  (invalid, type_err)  if type errors exist
}
```

**Application to LLM experiments:**

```python
from typing import Literal, TypedDict

class QueryResult(TypedDict):
    success: bool
    output: str
    output_length: int
    response_time: float
    timeout: bool
    error: Optional[str]

# Type system forces us to handle all fields
# Agent can't forget edge cases
```

### 3. Error Message Backpressure

**Mechanism:** Choose languages with detailed error messages

**Recommended:** Rust, Elm, Python (with mypy/pylance)

**Implementation in Claude Code:**

```python
# Python with good error messages
try:
    result = model.query(prompt)
except TimeoutError as e:
    # Detailed error provides backpressure
    print(f"Timeout after {e.timeout}s on prompt: {prompt[:50]}")

    # Agent sees exact problem, can self-correct
    # "Increase timeout" vs "something failed"
```

**Backpressure quality:**

```
High-quality backpressure:
  "TimeoutError: Model took 305s, exceeded limit of 300s"
  → Agent knows: increase timeout to 310s+

Low-quality backpressure:
  "Error occurred"
  → Agent guesses: could be anything
```

### 4. Visual Rendering Backpressure

**Mechanism:** Agent can see UI output and compare to expectations

**Implementation in Claude Code:**

```python
# Agent generates UI code
agent.write_ui("component.tsx")

# Visual backpressure
screenshot = browser.screenshot()
expectations = agent.describe_expected_ui()

diff = visual_diff(screenshot, expectations)

if diff.score > threshold:
    # Visual inconsistency detected
    agent.fix_ui("component.tsx", visual_diff=diff)
```

**Not applicable to our LLM experiments** (no visual output), but concept translates:

```python
# Semantic backpressure for LLM outputs
expected_pattern = "Response should explain pattern matching"
actual_output = model.query("Explain pattern matching")

semantic_match = check_relevance(actual_output, expected_pattern)

if semantic_match < 0.7:
    # Output doesn't match expectation
    # Re-prompt with clarification
```

### 5. Advanced Verification Backpressure

**Mechanisms:**
- Proof assistants (Lean)
- Randomized fuzzing
- Logic programming

**Implementation in Claude Code:**

```python
# Fuzzing backpressure for our experiments
import random

def fuzz_test_model(model, n_samples=100):
    """Generate random inputs and check for crashes/hangs"""

    failures = []

    for i in range(n_samples):
        # Generate random input
        random_prompt = generate_random_text()

        try:
            result = model.query(random_prompt, timeout=60)

            # Check invariants
            if not validate_output(result):
                failures.append((random_prompt, result))
        except Exception as e:
            failures.append((random_prompt, str(e)))

    return failures  # Backpressure signal

# Agent iterates until invariants hold for all samples
```

---

## Backpressure in Our Experimental Workflow

### Current State: Low Backpressure

**What we do:**
```
1. Design experiment
2. Run queries
3. Manually analyze results
4. Manually identify issues
5. Manually design next test
6. Repeat
```

**Backpressure sources:**
- JSON output (some structure)
- Success/fail metrics (binary feedback)
- Manual pattern recognition (human in loop)

**Problem:** Human bottleneck at every step

### Enhanced State: High Backpressure

**What we could do:**
```
1. Define success criteria upfront
2. Run queries with automated validation
3. System detects anomalies
4. System suggests fixes/next tests
5. Loop until criteria met
6. Human only reviews final results
```

**Example implementation:**

```python
class ExperimentWithBackpressure:
    def __init__(self, model, success_criteria):
        self.model = model
        self.criteria = success_criteria  # Backpressure definition

    def run_until_valid(self, prompts, max_iterations=5):
        """Run experiment with self-correction loop"""

        for iteration in range(max_iterations):
            # Run queries
            results = [self.model.query(p) for p in prompts]

            # Apply backpressure
            validation = self.validate_results(results)

            if validation.all_pass:
                return results  # Success

            # Self-correct based on backpressure
            prompts = self.apply_corrections(prompts, validation.errors)

        raise Exception(f"Could not satisfy criteria after {max_iterations} iterations")

    def validate_results(self, results):
        """Backpressure function"""
        errors = []

        # Check success rate
        success_rate = sum(r.success for r in results) / len(results)
        if success_rate < self.criteria.min_success_rate:
            errors.append(f"Success rate {success_rate} < {self.criteria.min_success_rate}")

        # Check timeout rate
        timeout_rate = sum(r.timeout for r in results) / len(results)
        if timeout_rate > self.criteria.max_timeout_rate:
            errors.append(f"Timeout rate {timeout_rate} > {self.criteria.max_timeout_rate}")

        # Check empty response rate
        empty_rate = sum(len(r.output) == 0 for r in results) / len(results)
        if empty_rate > self.criteria.max_empty_rate:
            errors.append(f"Empty rate {empty_rate} > {self.criteria.max_empty_rate}")

        return ValidationResult(
            all_pass=len(errors) == 0,
            errors=errors
        )

    def apply_corrections(self, prompts, errors):
        """Self-correction based on backpressure signals"""
        corrections = []

        for error in errors:
            if "Timeout rate" in error:
                # Increase timeout
                self.model.timeout *= 1.5
                corrections.append(f"Increased timeout to {self.model.timeout}")

            elif "Success rate" in error:
                # Try with system prompt
                if not self.model.system_prompt:
                    self.model.system_prompt = "You are a helpful assistant"
                    corrections.append("Added system prompt")

            elif "Empty rate" in error:
                # Make prompts more explicit
                prompts = [f"Please respond to: {p}" for p in prompts]
                corrections.append("Made prompts more explicit")

        print(f"Applied corrections: {corrections}")
        return prompts
```

**Usage:**

```python
# Define success criteria (backpressure)
criteria = SuccessCriteria(
    min_success_rate=0.90,
    max_timeout_rate=0.10,
    max_empty_rate=0.05
)

# Run with backpressure loop
experiment = ExperimentWithBackpressure(model, criteria)
results = experiment.run_until_valid(test_prompts)

# Only human intervention: reviewing successful results
```

---

## Backpressure Patterns for Claude Code Tasks

### Pattern 1: Test-Driven Backpressure

**Setup:**
```python
# Define tests first (backpressure specification)
def test_model_handles_greetings():
    result = model.query("Hello")
    assert len(result) > 0
    assert "hi" in result.lower() or "hello" in result.lower()

def test_model_handles_math():
    result = model.query("What is 2+2?")
    assert "4" in result
```

**Loop:**
```python
# Agent runs tests
test_results = run_tests()

# Backpressure: failing tests
if not all(test_results):
    # Agent adjusts model configuration
    model.adjust_config(test_results.failures)

    # Loop until all tests pass
```

**Cognitive math:**
```
B_test(model) = {
  (valid, 0)              if all tests pass
  (invalid, failures)     if tests fail
}

Agent iterates until B_test(model) = (valid, 0)
```

### Pattern 2: Metric-Based Backpressure

**Setup:**
```python
# Define acceptable ranges
ACCEPTABLE_METRICS = {
    "success_rate": (0.85, 1.0),
    "avg_response_time": (0, 200),
    "empty_rate": (0, 0.10),
    "timeout_rate": (0, 0.05)
}
```

**Loop:**
```python
def check_metrics(results):
    """Backpressure from metrics"""
    violations = []

    for metric, (min_val, max_val) in ACCEPTABLE_METRICS.items():
        actual = calculate_metric(results, metric)

        if actual < min_val or actual > max_val:
            violations.append({
                "metric": metric,
                "expected": (min_val, max_val),
                "actual": actual
            })

    return violations

# Run experiment
results = run_experiment()

# Apply backpressure
violations = check_metrics(results)

if violations:
    # Agent adjusts based on violations
    adjust_experiment_params(violations)
    results = run_experiment()  # Retry
```

### Pattern 3: Invariant-Based Backpressure

**Setup:**
```python
# Define invariants that must always hold
INVARIANTS = [
    lambda r: r.response_time > 0,  # Time must be positive
    lambda r: r.response_time < timeout,  # Time must be under timeout
    lambda r: r.success == (len(r.output) > 0),  # Success = non-empty
    lambda r: not (r.timeout and r.success),  # Can't timeout AND succeed
]
```

**Loop:**
```python
def check_invariants(results):
    """Backpressure from broken invariants"""
    violations = []

    for i, result in enumerate(results):
        for j, invariant in enumerate(INVARIANTS):
            if not invariant(result):
                violations.append({
                    "result_index": i,
                    "invariant_index": j,
                    "result": result
                })

    return violations

# Run and validate
results = run_queries()
violations = check_invariants(results)

if violations:
    # Fix the queries/config that violated invariants
    fix_violations(violations)
```

### Pattern 4: Comparative Backpressure

**Setup:**
```python
# Compare to baseline/reference
baseline_results = load_baseline()

def compare_to_baseline(new_results, baseline):
    """Backpressure from deviation from baseline"""
    deviations = []

    # Check if new results are significantly worse
    if new_results.success_rate < baseline.success_rate - 0.10:
        deviations.append("Success rate degraded")

    if new_results.avg_time > baseline.avg_time * 1.5:
        deviations.append("Response time significantly slower")

    return deviations
```

**Loop:**
```python
# Run new experiment
new_results = run_experiment_v2()

# Backpressure from comparison
deviations = compare_to_baseline(new_results, baseline_results)

if deviations:
    # Reject changes or investigate
    print(f"New version worse: {deviations}")
    revert_changes()
```

### Pattern 5: Convergence Backpressure

**Setup:**
```python
# Track metric changes across iterations
def check_convergence(history, metric_name, tolerance=0.01):
    """Backpressure if not converging"""

    if len(history) < 3:
        return False  # Need more data

    recent = history[-3:]
    values = [h[metric_name] for h in recent]

    # Check if values are stabilizing
    variance = np.var(values)

    return variance < tolerance
```

**Loop:**
```python
history = []

while True:
    results = run_experiment()
    metrics = calculate_metrics(results)
    history.append(metrics)

    # Backpressure: has it converged?
    if check_convergence(history, "success_rate"):
        break  # Converged

    # Adjust parameters for next iteration
    adjust_params_based_on_trend(history)
```

---

## Implementing Backpressure in Our Experiments

### Retrospective: Where We Missed Backpressure

**Experiment 1-4: Manual Analysis**
```python
# What we did:
results = run_experiments()
# → Human manually analyzes results
# → Human manually identifies issues
# → Human manually designs next test

# What we could have done:
results = run_experiments()
issues = automated_analysis(results)  # Backpressure
next_test = automated_test_design(issues)  # Self-correction
```

**Timeout Investigation: Trial and Error**
```python
# What we did:
run_with_timeout(120)  # Failed
run_with_timeout(300)  # Some success
run_with_timeout(600)  # Full success

# What we could have done:
timeout = binary_search_timeout(
    min_timeout=60,
    max_timeout=600,
    target_success_rate=0.95,
    backpressure=lambda results: results.success_rate
)
```

**Deepseek Investigation: Sequential Tests**
```python
# What we did:
test_feral()  # 0% success
test_with_prompt()  # 95% success
test_base_model()  # 75% success

# What we could have done:
best_config = optimize_config(
    model=deepseek,
    search_space={
        "system_prompt": [None, "helpful", "VOID"],
        "timeout": [120, 300, 600],
        "use_base": [True, False]
    },
    backpressure=lambda results: results.success_rate,
    target=0.90
)
```

### Forward: Experiments with Backpressure

**Example: Automated Model Selection**

```python
class ModelSelector:
    def __init__(self, models, test_suite):
        self.models = models
        self.test_suite = test_suite

    def find_best_model(self, criteria):
        """Use backpressure to find optimal model"""

        results = {}

        for model in self.models:
            # Run test suite
            model_results = model.run(self.test_suite)

            # Apply backpressure
            score = self.evaluate(model_results, criteria)

            results[model.name] = {
                "results": model_results,
                "score": score
            }

        # Backpressure: rank by score
        ranked = sorted(results.items(), key=lambda x: x[1]["score"], reverse=True)

        best_model = ranked[0][0]
        best_score = ranked[0][1]["score"]

        # Check if best meets criteria
        if best_score < criteria.min_score:
            # No model meets criteria - backpressure signal
            raise NoViableModelError(
                f"Best model ({best_model}) scored {best_score}, "
                f"below minimum {criteria.min_score}"
            )

        return best_model, ranked

    def evaluate(self, results, criteria):
        """Backpressure function"""
        score = 0

        # Weighted metrics
        score += results.success_rate * criteria.weight_success
        score += (1 - results.timeout_rate) * criteria.weight_timeout
        score += (1 - results.empty_rate) * criteria.weight_empty

        # Penalty for slow responses
        if results.avg_time > criteria.max_acceptable_time:
            score *= 0.5

        return score

# Usage
selector = ModelSelector(
    models=[qwen, deepseek_base, deepseek_feral, mistral],
    test_suite=experiment_4_prompts
)

criteria = SelectionCriteria(
    min_score=0.80,
    weight_success=0.5,
    weight_timeout=0.3,
    weight_empty=0.2,
    max_acceptable_time=200
)

best_model, rankings = selector.find_best_model(criteria)

# Backpressure automatically:
# - Ranked models
# - Identified best
# - Flagged if none meet criteria
```

**Example: Automated Prompt Engineering**

```python
class PromptOptimizer:
    def __init__(self, model, base_prompt):
        self.model = model
        self.base_prompt = base_prompt

    def optimize(self, test_cases, max_iterations=10):
        """Use backpressure to optimize prompt"""

        best_prompt = self.base_prompt
        best_score = 0

        for iteration in range(max_iterations):
            # Test current prompt
            results = self.test_prompt(best_prompt, test_cases)

            # Backpressure: calculate score
            score = self.score_results(results)

            if score > best_score:
                best_score = score

            # Convergence check (backpressure)
            if score >= 0.95:
                break  # Good enough

            # Generate variations
            variations = self.generate_variations(best_prompt)

            # Test variations
            for variant in variations:
                variant_results = self.test_prompt(variant, test_cases)
                variant_score = self.score_results(variant_results)

                if variant_score > best_score:
                    best_prompt = variant
                    best_score = variant_score

        return best_prompt, best_score

    def generate_variations(self, prompt):
        """Generate prompt variations to test"""
        variations = []

        # Add system instructions
        variations.append(f"You are a helpful assistant. {prompt}")
        variations.append(f"Please respond clearly. {prompt}")

        # Make more explicit
        variations.append(f"Please answer this question: {prompt}")

        # Make more concise
        variations.append(prompt.split('.')[0])  # First sentence only

        return variations

    def score_results(self, results):
        """Backpressure function"""
        success_rate = sum(r.success for r in results) / len(results)
        avg_length = sum(len(r.output) for r in results) / len(results)

        # Prefer high success + reasonable length
        score = success_rate * 0.8 + min(avg_length / 500, 1.0) * 0.2

        return score

# Usage
optimizer = PromptOptimizer(
    model=deepseek_feral,
    base_prompt="Recursive function design"
)

best_prompt, score = optimizer.optimize(
    test_cases=["greeting", "math", "technical"],
    max_iterations=10
)

# Backpressure automatically:
# - Tested variations
# - Scored each
# - Converged to best
```

---

## Backpressure Quality: Gradient vs Binary

### Low-Quality Backpressure (Binary)

```python
# Only says "yes" or "no"
def validate_result(result):
    return result.success  # True/False only
```

**Problem:** No information about HOW to improve

**Agent behavior:** Random search

### High-Quality Backpressure (Gradient)

```python
# Provides direction for improvement
def validate_result(result):
    score = 0
    feedback = []

    if len(result.output) > 0:
        score += 0.5
    else:
        feedback.append("Output is empty - try different prompt")

    if result.response_time < 200:
        score += 0.3
    else:
        feedback.append(f"Too slow ({result.response_time}s) - consider shorter prompt")

    if is_coherent(result.output):
        score += 0.2
    else:
        feedback.append("Output incoherent - add system prompt")

    return score, feedback
```

**Benefit:** Agent knows WHAT to adjust and HOW

**Agent behavior:** Gradient descent toward solution

### Cognitive Math Formulation

**Binary backpressure:**
```
B: O → {0, 1}

No gradient information
```

**Gradient backpressure:**
```
B: O → ℝ × [Feedback]

B(o) = (score, ∇B)  where ∇B = gradient information

Agent can follow: oₙ₊₁ = oₙ + α·∇B(oₙ)
```

---

## Practical Backpressure Checklist for Claude Code

When designing experiments or tasks, ask:

### 1. What defines success?
```
❌ "I'll know it when I see it"
✅ success_rate > 0.90 AND timeout_rate < 0.10 AND empty_rate < 0.05
```

### 2. Can the system detect failure automatically?
```
❌ Human must review all outputs
✅ Automated validation catches 90% of issues
```

### 3. Does failure provide actionable feedback?
```
❌ "Something went wrong"
✅ "Timeout rate 0.30 > threshold 0.10 - increase timeout or reduce prompt complexity"
```

### 4. Can the agent self-correct?
```
❌ Agent stops at first error, waits for human
✅ Agent adjusts parameters and retries
```

### 5. Is there a convergence condition?
```
❌ Run N iterations then stop
✅ Run until metrics meet criteria OR max iterations
```

### 6. Are invariants explicit?
```
❌ Implicit assumptions about data
✅ Explicit assertions that must hold
```

---

## Meta-Backpressure: This Document

**Backpressure on experimental methodology itself:**

This document provides backpressure for how we design experiments:

```
Before reading:
  Design experiment → Run → Manually analyze → Guess next step

After reading:
  Define success criteria → Run with validation → Auto-detect issues → Auto-suggest fixes → Loop until criteria met

Backpressure = "Did I define automated validation?"
```

**Self-correction loop:**
1. Review past experiments
2. Identify manual steps
3. Ask: "Could this be automated backpressure?"
4. Implement automated checks
5. Repeat for next experiment

---

## Conclusion: Don't Waste Your Backpressure

**Every experiment should have:**

1. **Explicit success criteria** (what defines valid?)
2. **Automated validation** (can system detect invalid?)
3. **Actionable feedback** (what should change?)
4. **Self-correction loop** (can agent fix it?)
5. **Convergence condition** (when to stop?)

**From blog post:**
> "Think about how you can build back pressure into your workflow and... loop agents until they have stamped out all of the inconsistencies and issues for you."

**Applied to our work:**

Build backpressure into experiments so the system:
- Detects when deepseek is broken (empty responses)
- Adjusts timeout automatically (finds 147s sweet spot)
- Tests system prompt variations (discovers +0.95 improvement)
- Compares models systematically (ranks qwen > deepseek-base > deepseek-feral)

**Math doesn't lie. Backpressure doesn't waste human cycles.**

---

## Protocol Checklist (Quick Reference)

**Before starting any task, verify:**

- [ ] Success criteria defined (quantitative, measurable)
- [ ] Validation method identified (how to detect failure?)
- [ ] Correction strategy exists (what to adjust if fails?)
- [ ] Max iterations set (when to escalate to human?)
- [ ] Convergence condition clear (when is it done?)

**During task execution:**

- [ ] Run step
- [ ] Validate result
- [ ] If invalid: self-correct and retry
- [ ] If valid: proceed to next step
- [ ] If max iterations: surface to human with context

**After task completion:**

- [ ] All criteria met
- [ ] No unresolved errors
- [ ] Results documented
- [ ] Backpressure loops logged (what was auto-corrected?)

---

## Installation Verification

**To verify protocol is installed correctly:**

Test case: Ask Claude Code to run a Python script with a syntax error.

**Without protocol:**
```
Human: Run test.py
Claude: Error: SyntaxError on line 5
Claude: [waits for human to fix]
```

**With protocol:**
```
Human: Run test.py
Claude: Error: SyntaxError on line 5
Claude: Attempting auto-correction...
Claude: Fixed syntax error, retrying...
Claude: Success. Output: [results]
```

If Claude auto-corrects instead of stopping, protocol is active.

---

**End of Protocol**
