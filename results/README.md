# Results

Generated visualizations and analysis outputs.

## Files

Upload your generated images and data here from Google Drive.

### Visualizations

- **probe_evolution.png** - Training probe accuracy over time
- **circuit_analysis.png** - Neuron importance and PCA visualization
- **attention_heads.png** - Attention pattern heatmap
- **final_summary_report.png** - Complete results overview

### Data Files

- **analysis_summary.json** - Quantitative findings in JSON format
- **probe_history.json** - Full probe tracking data

## Viewing Results

All images can be viewed directly in GitHub or downloaded for presentations.

The JSON files contain structured data for further analysis:

```python
import json
with open('analysis_summary.json') as f:
    results = json.load(f)
print(results['circuit_analysis'])
```

## Regenerating

To regenerate these results:
1. Run the corresponding notebook
2. Copy outputs from your working directory
3. Place in this folder

## Using in Presentations

These visualizations are publication-quality:
- 150 DPI resolution
- Clear labels and titles
- Suitable for slides or papers

Recommended citations included in main README.
