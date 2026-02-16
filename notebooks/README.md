# Notebooks

Jupyter notebooks for each phase of the analysis.

## Files

**Note:** Upload your Colab notebooks here after downloading them from Google Drive.

### Core Pipeline

1. **01_data_preparation.ipynb** - Dataset creation and preprocessing
2. **02_training.ipynb** - Model training with probe tracking
3. **03_circuit_analysis.ipynb** - Neuron-level circuit discovery
4. **04_attention_heads.ipynb** - Attention pattern analysis
5. **05_full_pipeline.ipynb** - Complete end-to-end workflow

## Usage

### In Google Colab

1. Upload notebook to Colab
2. Run cells sequentially
3. Results save to Google Drive automatically

### Locally

```bash
jupyter notebook
# Open desired notebook
# Run cells sequentially
```

## Dependencies

See `requirements.txt` in root directory.

## Execution Order

**First time:**
1. Run `01_data_preparation.ipynb`
2. Run `02_training.ipynb` (takes ~2 hours)
3. Run `03_circuit_analysis.ipynb`
4. Run `04_attention_heads.ipynb`

**Subsequent runs:**
- Can skip training if model already exists
- Analysis notebooks can run independently

## Outputs

Each notebook saves its outputs to:
- Working directory (local)
- `/content/drive/MyDrive/joke_mechanistic_interp/` (Colab)

## Troubleshooting

- **Import errors:** Install dependencies with `pip install -r requirements.txt`
- **CUDA errors:** Ensure GPU runtime is enabled (Colab: Runtime > Change runtime type)
- **Out of memory:** Reduce batch size or max_samples parameters
