# Setup Instructions for GitHub

Follow these steps to get your repo up and running.

---

## Step 1: Create GitHub Repository

1. Go to https://github.com/new
2. Repository name: `joke-circuits` (or your preferred name)
3. Description: "Mechanistic interpretability study of humor understanding in transformers"
4. **Public** (recommended for portfolio) or Private
5. **DO NOT** initialize with README (we already have one)
6. Click "Create repository"

---

## Step 2: Download Files from Google Drive

Go to your Google Drive folder: `/MyDrive/joke_mechanistic_interp/`

### Download these files:

**Visualizations (put in `results/` folder):**
- [ ] probe_evolution.png
- [ ] circuit_analysis.png  
- [ ] attention_heads.png
- [ ] final_summary_report.png (if you created it)

**Data (put in `results/` folder):**
- [ ] analysis_summary.json

**Notebooks (put in `notebooks/` folder):**
- [ ] Your Colab notebooks (download as .ipynb files)
  - Data preparation notebook → `01_data_preparation.ipynb`
  - Training notebook → `02_training.ipynb`
  - Circuit analysis notebook → `03_circuit_analysis.ipynb`
  - Attention heads notebook → `04_attention_heads.ipynb`

**Note:** Don't upload model files (*.pt) - they're too large for GitHub

---

## Step 3: Organize Local Folder

Create this structure on your computer:

```
joke-circuits/
├── README.md ✓
├── LICENSE ✓
├── requirements.txt ✓
├── .gitignore ✓
├── CONTRIBUTING.md ✓
│
├── docs/
│   ├── FINDINGS.md ✓
│   ├── METHODOLOGY.md ✓
│   └── REPRODUCTION.md ✓
│
├── examples/
│   └── generated_jokes.md ✓
│
├── notebooks/
│   ├── README.md ✓
│   ├── 01_data_preparation.ipynb ← ADD THIS
│   ├── 02_training.ipynb ← ADD THIS
│   ├── 03_circuit_analysis.ipynb ← ADD THIS
│   └── 04_attention_heads.ipynb ← ADD THIS
│
└── results/
    ├── README.md ✓
    ├── probe_evolution.png ← ADD THIS
    ├── circuit_analysis.png ← ADD THIS
    ├── attention_heads.png ← ADD THIS
    └── analysis_summary.json ← ADD THIS (optional)
```

**✓ = Already created**
**← ADD THIS = You need to add from Google Drive**

---

## Step 4: Initialize Git Repository

Open terminal in your `joke-circuits/` folder:

```bash
# Initialize git
git init

# Add all files
git add .

# First commit
git commit -m "Initial commit: Complete mechanistic interpretability study"

# Connect to GitHub (replace with your username)
git remote add origin https://github.com/YOUR-USERNAME/joke-circuits.git

# Push to GitHub
git branch -M main
git push -u origin main
```

---

## Step 5: Verify on GitHub

Go to your repository URL and check:

- [ ] README displays properly with images
- [ ] Folder structure is correct
- [ ] All documentation files are present
- [ ] Results folder has images
- [ ] Notebooks folder has .ipynb files

---

## Step 6: Make it Look Professional

### Add Topics/Tags

On your GitHub repo page:
1. Click ⚙️ (settings icon) next to "About"
2. Add topics:
   - `mechanistic-interpretability`
   - `transformers`
   - `nlp`
   - `machine-learning`
   - `interpretability`
   - `transformer-circuits`
3. Save

### Update README with Your GitHub Username

In README.md, replace:
```
url = {https://github.com/[your-username]/joke-circuits}
```
with:
```
url = {https://github.com/YOUR-ACTUAL-USERNAME/joke-circuits}
```

### Add Description

In GitHub repo settings:
- Website: (leave blank or add personal site)
- Description: "Mechanistic interpretability study of humor understanding in transformers. Trained 64M param model, found distributed semantic circuits."

---

## Step 7: Optional Enhancements

### Add GitHub Actions (optional)

Create `.github/workflows/test.yml` for automated testing (advanced)

### Create Wiki (optional)

Add detailed technical notes in GitHub Wiki

### Enable Discussions (optional)

For community questions and discussions

### Add Badges (optional)

Add to top of README.md:
```markdown
![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Python](https://img.shields.io/badge/python-3.8+-blue.svg)
```

---

## Troubleshooting

### "Images not showing in README"

Make sure image paths are correct:
```markdown
![Circuit Analysis](results/circuit_analysis.png)
```
Not:
```markdown
![Circuit Analysis](/results/circuit_analysis.png)  ❌ Extra /
```

### "Notebooks not rendering"

- Ensure files end with `.ipynb`
- GitHub automatically renders Jupyter notebooks
- May take a few seconds to load

### "Push rejected"

```bash
# If you need to force push (only on first push)
git push -u origin main --force
```

---

## What NOT to Upload

**Do not upload:**
- [ ] Model files (*.pt, *.pth) - Too large
- [ ] Dataset files (*.jsonl) - Too large
- [ ] Checkpoints - Too large
- [ ] __pycache__/ folders - Auto-generated
- [ ] .ipynb_checkpoints/ - Temporary files

**Why:** Git is for code, not large binary files. Users can regenerate these.

---

## Sharing Your Work

Once uploaded, you can:

1. **Add to portfolio:**
   - Link in resume/CV
   - Show to potential collaborators
   - Reference in applications

2. **Share with Shubham:**
   - Send him the GitHub link
   - Shows complete, professional documentation
   - Easy for him to explore

3. **Social media:**
   - Tweet the link with key findings
   - Post on LinkedIn
   - Share in ML communities

---

## Next Steps

After repo is live:

1. ✅ Share with Shubham and collaborators
2. ✅ Add to your portfolio/resume
3. ✅ Consider writing a blog post explaining findings
4. ✅ Maybe submit to ML Collective or similar venues

---

## Quick Commands Reference

```bash
# Clone your repo (on new machine)
git clone https://github.com/YOUR-USERNAME/joke-circuits.git

# Update repo with changes
git add .
git commit -m "Description of changes"
git push

# Check status
git status

# View commit history
git log --oneline
```

---

You're done! 🎉

Your mechanistic interpretability study is now professionally documented and publicly available.
