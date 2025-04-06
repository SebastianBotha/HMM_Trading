# HMM Trading Project Guidelines

## Commands
- Run Jupyter notebooks: `jupyter notebook hmm.ipynb` or `jupyter notebook models.ipynb`
- Run Python script: `python curate.py`
- Install dependencies: `pip install pandas numpy matplotlib seaborn scipy hmmlearn`

## Code Style Guidelines
- **Imports**: Group imports in order: standard library, third-party packages, local modules
- **Formatting**: Use 4 spaces for indentation, max line length 100 characters
- **Naming**: Use snake_case for variables/functions, CamelCase for classes
- **Comments**: Use # for single-line comments, docstrings for function/class documentation
- **Error Handling**: Use try/except blocks with specific exception types
- **Data Analysis**: Prefer pandas DataFrame operations over loops when possible
- **HMM Models**: Set parameters (hidden_states, em_iterations) at top of file for easy modification
- **Algorithms**: Implement as classes with proper initialization and rebalancing methods
- **Plotting**: Use seaborn for statistical visualizations, matplotlib for custom charts