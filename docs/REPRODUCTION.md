# Reproduction Guide

Complete instructions for reproducing this work from scratch.

---

## Quick Start (Google Colab)

**Fastest path to results:**

1. Open Google Colab
2. Upload notebooks from `notebooks/` folder
3. Run `05_full_pipeline.ipynb`
4. Results will be in your Google Drive

**Estimated time:** 3-4 hours on Colab Pro (T4 GPU)

---

## Detailed Step-by-Step

### Prerequisites

**Hardware:**
- GPU with ≥15GB VRAM (recommended: T4, V100, A100)
- For CPU-only: Possible but ~10x slower

**Software:**
- Python 3.8+
- CUDA 11.7+ (for GPU)
- Google Drive (if using Colab)

---

## Setup

### Option 1: Google Colab (Recommended)

```python
# Cell 1: Mount Google Drive
from google.colab import drive
drive.mount('/content/drive')

# Create project directory
import os
project_dir = '/content/drive/MyDrive/joke_mechanistic_interp'
os.makedirs(project_dir, exist_ok=True)
os.chdir(project_dir)
```

```python
# Cell 2: Install dependencies
!pip install -q transformer-lens==1.17.0 einops==0.7.0 datasets==2.14.0
```

### Option 2: Local Environment

```bash
# Clone repository
git clone https://github.com/[your-username]/joke-circuits.git
cd joke-circuits

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

---

## Running the Pipeline

### Step 1: Data Preparation (~5 minutes)

Run `notebooks/01_data_preparation.ipynb`

**What it does:**
- Downloads ~200k jokes from Reddit
- Filters to 17k high-quality jokes
- Creates train/val/test splits
- Saves to `.jsonl` files

**Outputs:**
```
train_jokes.jsonl  (18,156 examples)
val_jokes.jsonl    (2,269 examples)
test_jokes.jsonl   (2,271 examples)
```

**Verification:**
```python
import pandas as pd
df = pd.read_json('train_jokes.jsonl', lines=True)
print(f"Train size: {len(df)}")
print(f"Example: {df.iloc[0]['text']}")
```

---

### Step 2: Model Training (~2 hours)

Run `notebooks/02_training.ipynb`

**What it does:**
- Creates 64M parameter transformer
- Trains for 30 epochs with probe tracking
- Saves checkpoints every 1000 steps
- Generates final visualizations

**Monitoring progress:**
- Watch loss curve (should drop from ~5 to ~0.3)
- Probe evaluations every 500 steps
- Checkpoints saved to prevent data loss

**Outputs:**
```
final_model.pt              (trained weights)
latest_checkpoint.pt        (resumable checkpoint)
probe_history.json          (probe tracking data)
probe_evolution.png         (training visualization)
```

**If training is interrupted:**
```python
# The notebook will automatically resume from latest checkpoint
# Just re-run the training cell
```

**Verification:**
```python
# Test joke generation
model.eval()
test_setup = "<|setup|>Why did the chicken cross the road?<|punchline|>"
# ... (see notebook for full generation code)
```

---

### Step 3: Circuit Analysis (~30 minutes)

Run `notebooks/03_circuit_analysis.ipynb`

**What it does:**
- Extracts activations at each layer
- Trains linear probes for joke detection
- Creates PCA visualizations
- Identifies important neurons

**Outputs:**
```
circuit_analysis.png        (probe accuracy + PCA)
```

**Expected results:**
- All layers should show 1.0 probe accuracy
- PCA should show clear joke/non-joke clustering
- Orange (jokes) cluster tightly on right

**Verification:**
```python
# Check probe accuracies
for layer in range(4):
    print(f"Layer {layer}: {neuron_importance[layer]['probe_acc']:.3f}")
# Should all be 1.000
```

---

### Step 4: Attention Head Analysis (~30 minutes)

Run `notebooks/04_attention_heads.ipynb`

**What it does:**
- Extracts attention patterns from all heads
- Computes setup→punchline attention
- Compares jokes vs non-jokes
- Creates heatmap visualization

**Outputs:**
```
attention_heads.png         (attention difference heatmap)
```

**Expected results:**
- Most values should be negative (blue)
- Layer 3, Head 0 should have largest negative value (~-0.058)
- This indicates non-jokes use MORE attention

**Verification:**
```python
# Find top head with biggest difference
all_heads = []
for layer in range(4):
    for head in range(8):
        diff = head_stats[layer][head]['difference']
        all_heads.append((layer, head, abs(diff)))

all_heads.sort(key=lambda x: x[2], reverse=True)
print(f"Top head: Layer {all_heads[0][0]}, Head {all_heads[0][1]}")
# Should be Layer 3, Head 0
```

---

## Expected Results Summary

### Training Metrics

```
Final training loss: ~0.30-0.35
Final validation loss: ~5.5-5.7
Training time: 2-2.5 hours (T4 GPU)
```

**Note:** Validation loss higher than training indicates overfitting, which is expected and fine for this analysis.

### Probe Accuracies

```
Layer 0: 1.000
Layer 1: 1.000
Layer 2: 1.000
Layer 3: 1.000
```

All layers should achieve perfect separation.

### Attention Patterns

```
Most negative head: Layer 3, Head 0 (~-0.05 to -0.06)
Overall pattern: Majority of heads show negative differences
```

### Generation Quality

```
"Why did the scarecrow win an award?"
→ "Because he was outstanding in his field"

"What do you call a bear with no teeth?"
→ "A gummy bear"

"Why don't scientists trust atoms?"
→ "They make up everything"
```

Generated punchlines should be coherent and contextually appropriate.

---

## Troubleshooting

### Out of Memory Errors

**Problem:** GPU runs out of memory during training

**Solutions:**
```python
# Reduce batch size in training notebook
batch_size = 8  # Instead of 16

# Or reduce max_samples in analysis
max_samples = 200  # Instead of 500
```

### Dataset Download Fails

**Problem:** Can't download jokes from GitHub

**Solution:**
```python
# Manual download
# Go to: https://raw.githubusercontent.com/taivop/joke-dataset/master/reddit_jokes.json
# Save as 'reddit_jokes.json' in working directory
# Then modify data preparation notebook to load local file
```

### Model Not Learning

**Problem:** Loss stays high, doesn't decrease

**Check:**
1. Verify data loaded correctly (`len(train_dataset)` should be ~18k)
2. Check learning rate (should be 3e-4)
3. Verify special tokens in tokenizer
4. Try training for more epochs

**Expected behavior:**
- Loss should drop rapidly in first epoch (5.0 → 3.0)
- Continue decreasing to ~0.3 by epoch 30

### Probes Stay at 0.5 Accuracy

**Problem:** Probes can't distinguish jokes from non-jokes

**This shouldn't happen**, but if it does:
1. Verify data has both jokes and non-jokes (`df['is_joke'].value_counts()`)
2. Check that probe is using correct activations
3. Ensure punchline token is in tokenizer vocabulary

### Different Results Than Reported

**Expected variations:**
- Loss values may vary slightly (±0.1)
- Exact attention values will differ slightly
- Generated jokes will be different (randomness in generation)

**Should be consistent:**
- Probe accuracies (all should be 1.0)
- Overall attention pattern (negative differences)
- Joke quality (coherent punchlines)

---

## Hardware Requirements

### Minimum (CPU Only)

```
CPU: Any modern processor
RAM: 16GB
Storage: 5GB
Time: 8-12 hours training
```

### Recommended (Single GPU)

```
GPU: T4 (15GB VRAM) or better
RAM: 16GB
Storage: 10GB
Time: 2-3 hours total
```

### Optimal (Multi-GPU or Better GPU)

```
GPU: V100 (32GB VRAM) or A100
RAM: 32GB
Storage: 20GB
Time: 1-2 hours total
```

---

## Cost Estimate (Google Colab)

```
Colab Free: 
  - Limited GPU access
  - May disconnect
  - Not recommended for full pipeline

Colab Pro ($10/month):
  - T4 GPU access
  - ~2-3 hour session needed
  - Total cost: ~$0.50 per run
  - ✓ Recommended

Colab Pro+ ($50/month):
  - Better GPUs (V100, A100)
  - Faster training
  - ✓ Overkill for this project
```

---

## Validation Checklist

After running full pipeline, verify:

- [ ] Three `.jsonl` files created (train/val/test)
- [ ] `final_model.pt` file exists and is ~250MB
- [ ] `probe_evolution.png` shows flat lines at 1.0
- [ ] `circuit_analysis.png` shows separated clusters in PCA
- [ ] `attention_heads.png` is mostly blue (negative values)
- [ ] Model generates coherent jokes when tested
- [ ] All probe accuracies are 1.000

---

## Getting Help

**Common issues and solutions:**
- Check the notebooks for inline comments
- See [METHODOLOGY.md](METHODOLOGY.md) for technical details
- See [FINDINGS.md](FINDINGS.md) for expected results

**If stuck:**
1. Verify all dependencies installed correctly
2. Check GPU is available (`torch.cuda.is_available()`)
3. Ensure enough disk space
4. Try restarting runtime and re-running from start

**For unusual errors:**
- Check versions match `requirements.txt`
- Verify Python 3.8+ is being used
- Try in fresh Colab session

---

## Reproducing Specific Findings

### Finding: "Attention Pattern Inversion"

```python
# After running 04_attention_heads.ipynb
print(head_stats[3][0]['difference'])
# Should be ~-0.058 (negative = non-jokes use more attention)
```

### Finding: "Perfect Layer Separation"

```python
# After running 03_circuit_analysis.ipynb
for layer in range(4):
    print(f"Layer {layer}: {neuron_importance[layer]['probe_acc']}")
# All should be 1.000
```

### Finding: "Coherent Generation"

```python
# After training
test_setups = [
    "<|setup|>Why did the scarecrow win an award?<|punchline|>",
    "<|setup|>What do you call a bear with no teeth?<|punchline|>",
]
# Generate and verify punchlines make sense
```

---

## Timeline

**For complete reproduction:**

```
Setup:                    15 minutes
Data preparation:         5 minutes
Training:                 2 hours
Circuit analysis:         30 minutes
Attention analysis:       30 minutes
Documentation review:     30 minutes
-------------------------
Total:                    ~4 hours
```

**For quick validation:**

```
Use pre-trained model:    Skip training (save 2 hours)
Run analysis only:        1 hour
-------------------------
Total:                    ~1 hour
```

---

## Next Steps

After successful reproduction:

1. **Explore the results:**
   - Look at generated jokes
   - Examine attention patterns in detail
   - Try different probe architectures

2. **Extend the work:**
   - Try larger models
   - Test on different joke types
   - Implement MLP analysis

3. **Compare to baselines:**
   - Fine-tune GPT-2 small on same data
   - Compare circuit structures

4. **Share your findings:**
   - Document any variations you observe
   - Contribute improvements via PR
