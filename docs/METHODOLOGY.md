# Methodology

## Overview

This project uses mechanistic interpretability techniques to understand how transformers learn humor. We follow a three-phase approach: training with monitoring, circuit discovery, and interpretation.

---

## Phase 1: Data Preparation

### Dataset Construction

**Source:** Reddit jokes from [taivop/joke-dataset](https://github.com/taivop/joke-dataset)

**Filtering criteria:**
- Minimum quality: Reddit score ≥ 50
- Length constraints: 10 ≤ total length ≤ 300 characters
- Complete structure: Both title (setup) and body (punchline) present

**Final dataset:**
- 17,022 high-quality jokes
- 5,674 non-joke examples (control group)
- 22,696 total examples

**Special tokens:**
```
<|setup|>[setup text]<|punchline|>[punchline text]<|end|>
```

**Data split:**
- Train: 80% (18,156 examples)
- Validation: 10% (2,269 examples)
- Test: 10% (2,271 examples)

### Non-Joke Construction

To create contrast, we added simple factual sentences with the same structure:

```python
Templates = [
    ("The weather today is", "sunny and warm"),
    ("I went to the store to buy", "milk and eggs"),
    ("The cat sat on", "the mat and fell asleep"),
    # ... etc
]
```

This gives the model clear negative examples for what jokes are NOT.

---

## Phase 2: Model Training

### Architecture

**Model type:** HookedTransformer (TransformerLens)

**Specifications:**
```python
n_layers = 4
d_model = 512
n_heads = 8
d_mlp = 2048
d_head = 64
n_ctx = 128 (max sequence length)
act_fn = "gelu"
normalization_type = "LN"
```

**Total parameters:** 64.2M

**Vocabulary:**
- Base: GPT-2 tokenizer (50,257 tokens)
- Added special tokens: 4
- Total vocab size: 50,261

### Training Configuration

**Optimizer:** AdamW
- Learning rate: 3e-4
- Weight decay: 0.01
- Gradient clipping: 1.0

**Learning rate schedule:** Cosine annealing over all steps

**Training:**
- Batch size: 16 (for T4 GPU memory constraints)
- Epochs: 30
- Total steps: ~34,050
- Training time: ~2 hours on single T4 GPU

**Loss function:** Cross-entropy on next-token prediction
- Ignore padding tokens
- Standard autoregressive language modeling objective

### Probe Tracking

**Purpose:** Monitor circuit emergence during training

**Procedure:**
Every 500 steps:
1. Extract activations at punchline position for 200 samples
2. For each layer (0-3):
   - Extract residual stream activations
   - Train logistic regression probe
   - Evaluate on validation set
3. Record accuracy for each layer

**What we're probing:** Can a linear classifier predict "is this a joke?" from layer activations?

---

## Phase 3: Circuit Discovery

### 3A: Neuron-Level Analysis

**Goal:** Find which neurons encode joke vs non-joke information

**Procedure:**

1. **Extract activations** (500 test samples)
   - Get residual stream at each layer
   - At punchline position (first token after `<|punchline|>`)

2. **Train probes** per layer
   - Logistic regression classifier
   - Features: 512-dimensional activation vector
   - Target: Binary joke/non-joke label

3. **Identify important neurons**
   - Use probe coefficients as importance scores
   - Top 20 neurons per layer (highest |coefficient|)

4. **Visualization**
   - Probe accuracy by layer (bar chart)
   - PCA of activation space (scatter plot)
   - Color by joke/non-joke label

### 3B: Attention Head Analysis

**Goal:** Find which attention heads connect setup to punchline

**Procedure:**

1. **Extract attention patterns** (200 test samples)
   - For each layer and head:
   - Get full attention matrix [query_pos × key_pos]

2. **Compute setup→punchline attention**
   ```python
   punchline_tokens = tokens[punchline_pos+1 : end]
   setup_tokens = tokens[0 : punchline_pos]
   
   attention_score = mean(attention[punchline_tokens, setup_tokens])
   ```

3. **Compare jokes vs non-jokes**
   ```python
   difference = mean_attention_jokes - mean_attention_non_jokes
   ```

4. **Visualization**
   - Heatmap: [layer × head] colored by difference
   - Positive (red) = more attention in jokes
   - Negative (blue) = more attention in non-jokes

---

## Phase 4: Interpretation

### PCA Visualization

**Purpose:** Visualize high-dimensional activation spaces

**Method:**
- Extract 512-dim activations from specific layer
- Apply PCA to reduce to 2D
- Plot with color = joke/non-joke label

**Interpretation:**
- Clustering indicates similar representations
- Separation indicates discriminative features
- Explained variance shows how much structure is captured

### Generation Testing

**Purpose:** Verify model learned semantic structure, not just classification

**Method:**
- Provide setup: `<|setup|>Why did the chicken...<|punchline|>`
- Generate completion autoregressively
- Temperature = 0.8 (some randomness, not pure argmax)
- Stop at `<|end|>` token

**Evaluation:** Qualitative assessment of:
- Coherence with setup
- Appropriateness as punchline
- Novelty (not memorized)
- Humor mechanism (wordplay, incongruity, etc.)

---

## Technical Details

### Why HookedTransformer?

**TransformerLens** provides:
- Easy access to all intermediate activations
- Clean API for running with cache
- Standard architecture for interpretability research
- No need for custom hooks

Example usage:
```python
logits, cache = model.run_with_cache(input_ids)

# Access any activation
layer_2_output = cache['blocks.2.hook_resid_post']
attention_pattern = cache['blocks.3.attn.hook_pattern']
```

### Why Train from Scratch?

**Advantages:**
- Clean slate - know exactly what model learned
- No confounding from pretraining
- Easier to attribute learned behaviors
- Fast training for this dataset size

**Tradeoffs:**
- Smaller model than pretrained alternatives
- Less general knowledge
- But: Perfect for mechanistic study of specific task

### Computational Requirements

**Training:**
- GPU: T4 (15GB VRAM)
- Time: ~2 hours
- Cost: ~$0.50 on Google Colab Pro

**Analysis:**
- GPU: Same T4 (needed for activation extraction)
- Time: ~1 hour total for all analyses
- Cost: Negligible

**Total time:** ~3 hours end-to-end

---

## Reproducibility Notes

### Random Seeds
- Model initialization: `seed=42`
- Probe training: `random_state=42`
- Data shuffling: Set seed before split

### Hardware Variations
- Batch size may need adjustment for different GPUs
- Training time scales with GPU speed
- Results should be qualitatively similar across hardware

### Data Availability
- Reddit jokes dataset is public
- Our filtered version can be regenerated with provided code
- Small variations in filtering won't significantly affect results

---

## Limitations & Future Work

### Current Limitations

1. **Small model size**
   - 64M params is tiny by modern standards
   - Larger models might show different circuit structures

2. **Single task focus**
   - Only tested on joke completion
   - Doesn't generalize to all humor understanding

3. **Dataset limitations**
   - Reddit jokes have specific style/format
   - May not capture all humor types

4. **Probe methodology**
   - Linear probes have limited expressiveness
   - Might miss nonlinear features

### Potential Extensions

1. **Larger models**
   - Test on Gemma-2B or Qwen-1.8B
   - Compare circuit structure across scales

2. **Different joke types**
   - Puns vs anti-jokes vs one-liners
   - Do they use different circuits?

3. **MLP neuron analysis**
   - Attention doesn't tell full story
   - Need to analyze MLP activations

4. **Causal interventions**
   - Activation patching
   - Path patching
   - Prove causal role of identified components

5. **Comparison to pretrained models**
   - Does GPT-2 have similar circuits?
   - Transfer learning vs from-scratch differences

---

## Code Organization

### Notebooks Structure

Each notebook is self-contained and can run independently:

1. **01_data_preparation.ipynb**
   - Downloads jokes
   - Creates train/val/test splits
   - Saves to `.jsonl` files

2. **02_training.ipynb**
   - Defines model architecture
   - Training loop with probe tracking
   - Saves checkpoints and results

3. **03_circuit_analysis.ipynb**
   - Neuron importance analysis
   - PCA visualization
   - Probe accuracy plots

4. **04_attention_heads.ipynb**
   - Attention pattern extraction
   - Setup→punchline attention analysis
   - Heatmap visualization

5. **05_full_pipeline.ipynb** (optional)
   - Complete end-to-end workflow
   - Useful for reproduction
   - May take longer to run

### Data Flow

```
Raw jokes (JSON) 
  → Clean & filter (01)
  → Format with special tokens (01)
  → Train/val/test splits (01)
  → Model training (02)
  → Trained model + checkpoints (02)
  → Circuit analysis (03, 04)
  → Visualizations + findings (03, 04)
```

All intermediate outputs saved to Google Drive for persistence.

---

## References

### Key Papers & Resources

**Mechanistic Interpretability:**
- [A Mathematical Framework for Transformer Circuits](https://transformer-circuits.pub/2021/framework/index.html)
- [In-context Learning and Induction Heads](https://transformer-circuits.pub/2022/in-context-learning-and-induction-heads/index.html)
- [Progress Measures for Grokking](https://arxiv.org/abs/2301.05217)

**Tools:**
- [TransformerLens Documentation](https://transformerlensorg.github.io/TransformerLens/)
- [Neel Nanda's MI Tutorials](https://www.neelnanda.io/mechanistic-interpretability)

**Dataset:**
- [Reddit Jokes Dataset](https://github.com/taivop/joke-dataset)
