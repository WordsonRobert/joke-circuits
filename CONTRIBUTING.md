# Contributing

Thanks for your interest in contributing to this project!

## How to Contribute

### Reporting Issues

Found a bug or have a suggestion?

1. Check if the issue already exists
2. If not, create a new issue with:
   - Clear description
   - Steps to reproduce (for bugs)
   - Expected vs actual behavior
   - Environment details (GPU, OS, Python version)

### Suggesting Enhancements

Have an idea for improving the analysis?

1. Open an issue describing the enhancement
2. Explain the motivation and use case
3. Provide examples if possible

### Code Contributions

#### Setup

```bash
git clone https://github.com/[your-username]/joke-circuits.git
cd joke-circuits
pip install -r requirements.txt
```

#### Making Changes

1. Create a new branch: `git checkout -b feature-name`
2. Make your changes
3. Test thoroughly
4. Commit with clear messages
5. Push and create a pull request

#### Code Style

- Follow PEP 8 for Python code
- Add comments for complex logic
- Include docstrings for functions
- Keep notebooks clean and well-documented

### Adding New Analyses

If you want to add new analysis methods:

1. Create a new notebook in `notebooks/`
2. Document methodology in `docs/METHODOLOGY.md`
3. Add findings to `docs/FINDINGS.md`
4. Update main README with new results

### Extending to New Datasets

Want to try different joke types or languages?

1. Document data source and licensing
2. Adapt data preparation notebook
3. Report findings in separate document
4. Compare to baseline results

## Areas for Contribution

### High Priority

- [ ] MLP neuron analysis
- [ ] Larger model experiments (Gemma, Qwen)
- [ ] Different joke types (puns vs anti-jokes)
- [ ] Causal intervention experiments

### Medium Priority

- [ ] Improved visualizations
- [ ] Interactive demos
- [ ] Comparison to pretrained models
- [ ] Multi-language support

### Low Priority

- [ ] Code optimization
- [ ] Additional documentation
- [ ] Tutorial videos
- [ ] Blog posts explaining findings

## Questions?

Feel free to open an issue with the "question" label.

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
