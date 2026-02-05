# Contributing to Financial Transactions Summary Tool

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Commit Message Conventions](#commit-message-conventions)
- [Code Style Guidelines](#code-style-guidelines)
- [Testing Requirements](#testing-requirements)
- [Pull Request Process](#pull-request-process)
- [Project Structure](#project-structure)
- [Agile Collaboration Guidelines](#agile-collaboration-guidelines)

## Code of Conduct

This project follows a code of conduct that emphasizes:
- **Respect**: Treat all contributors with respect and kindness
- **Collaboration**: Work together to build better solutions
- **Agile Practices**: Follow Agile principles and maintain traceability
- **Quality**: Write clean, modular, and documented code

## Getting Started

### Prerequisites

- Python 3.8 or higher
- Git
- GitHub account

### Setup Development Environment

1. **Clone the repository**:
   ```bash
   https://github.com/kojounfc/Grp4_Project_Toronto_Bike_Sharing_Analytics_Tool.git
   cd Grp4_Project_Toronto_Bike_Sharing_Analytics_Tool
   ```

2. **Create a virtual environment**:
   ```bash
   python -m venv venv
   # On Windows:
   venv\Scripts\activate
   # On Linux/Mac:
   source venv/bin/activate
   ```

3. **Install dependencies**:
   ```bash
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

4. **Install development tools**:
   ```bash
   pip install black isort pytest pytest-cov
   ```

5. **Verify setup**:
   ```bash
   pytest tests/ -v
   ```

## Development Workflow

### 1. Create a Feature Branch

**IMPORTANT**: Always create a new branch for your work.

```bash
git checkout -b feat/your-feature-name
# or
git checkout -b fix/bug-description
# or
git checkout -b docs/update-readme
```

**Branch naming conventions**:
- `feat/` - New features (e.g., `feat/data-loader`, `feat/spending-summary`)
- `fix/` - Bug fixes (e.g., `fix/date-parsing`, `fix/missing-values`)
- `docs/` - Documentation updates
- `test/` - Adding or updating tests
- `refactor/` - Code refactoring
- `chore/` - Maintenance tasks

### 2. Make Your Changes

- Write clean, modular code following Python best practices
- Add docstrings to all functions and classes (Google or NumPy style)
- Follow the project structure (see [Project Structure](#project-structure))
- Functions should return ready-to-use data objects or plot objects
- Never hardcode file paths - use function parameters or configuration

### 3. Write Tests

**Every new feature must include tests**:

- Create test files in `tests/` matching the source structure
- Test file naming: `test_<module_name>.py`
- Use pytest conventions
- Aim for good test coverage

Example:
```python
# src/features.py
# tests/test_features.py
```

### 4. Format Your Code

Before committing, format your code:

```bash
# Format with Black
black src/ tests/

# Sort imports with isort
isort src/ tests/

# Verify formatting
black --check src/ tests/
isort --check src/ tests/
```

### 5. Run Tests

**Always run tests before committing**:

```bash
# Run all tests
pytest tests/ -v

# Run specific test file
pytest tests/test_features.py -v

# Run with coverage
pytest tests/ --cov=src --cov-report=html
```

### 6. Commit Your Changes

**IMPORTANT**: Include user story IDs in commit messages for traceability.

Use conventional commit messages (see [Commit Message Conventions](#commit-message-conventions)):

```bash
git add .
git commit -m "feat(data-loader): implement CSV data loading [US-001]"
```

### 7. Keep Your Branch Updated

Regularly sync with dev branch:

```bash
git fetch origin
git rebase origin/dev
# Resolve any conflicts if needed
```

### 8. Push and Create Pull Request

```bash
git push origin feat/your-feature-name
```

Then create a Pull Request on GitHub.

## Commit Message Conventions

We follow [Conventional Commits](https://www.conventionalcommits.org/) specification with **user story traceability**.

### Format

```
<type>(<scope>): <description> [US-XXX]

[optional body]

[optional footer]
```

### Types

- **`feat`**: New feature
- **`fix`**: Bug fix
- **`docs`**: Documentation changes
- **`test`**: Adding or updating tests
- **`refactor`**: Code refactoring (no feature change or bug fix)
- **`chore`**: Maintenance tasks, dependency updates
- **`style`**: Code style changes (formatting, missing semicolons, etc.)
- **`perf`**: Performance improvements

### Scope (Optional)

The scope should be the name of the module affected:
- `data-loader` - Data loading functionality
- `data-cleaner` - Data cleaning and preprocessing
- `summary-generator` - Summary generation
- `insights` - Transaction insights
- `visualization` - Visualization functions
- `main` - Main entry point
- `docs` - Documentation

### Examples

```bash
# Feature with scope and user story ID
feat(data_io.py):Load Raw Data[US-1]

# Bug fix with user story ID
fix(data_io.py): resolve date parsing issue [US-002]

# Documentation
docs(readme): update installation instructions

# Test with user story ID
test(data_io.py): add unit tests for loading data [US-1]

# Refactoring
refactor(visualization): optimize figure creation

# Multiple changes (use body)
feat(features.py): add feature engineering functions [US-004]

- Implement income calculation function
- Add expense categorization
- Generate summary DataFrame
- Add unit tests
```

### Commit Body (Optional)

For complex changes, use a multi-line commit message:

```bash
git commit -m "feat(features.py): add feature engineering functions [US-004]
# - Implement CSV loader
# - Add JSON loader support
# - Handle file validation
# - Add unit tests for all formats

Closes #123"
```

## Code Style Guidelines

### Python Style

- Follow PEP 8 style guide
- Use Black for code formatting (line length: 88)
- Use isort for import sorting
- Maximum line length: 88 characters (Black default)

### Import Organization

```python
# Standard library imports
import os
from pathlib import Path
from typing import Dict, List, Optional

# Third-party imports
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Local imports
from src.data_io import load_raw_data
from src.data_io import clean_data
```

### Docstrings

Use Google or NumPy style docstrings:

```python
def generate_spending_summary(df: pd.DataFrame) -> Dict[str, float]:
    """
    Generate spending summary from transaction data.

    Args:
        df: Cleaned transaction DataFrame with required columns

    Returns:
        Dictionary containing total spending, average spending, and category breakdowns

    Raises:
        ValueError: If required columns are missing
    """
    pass
```

### Type Hints

Always use type hints for function signatures:

```python
from typing import Dict, List, Optional
import pandas as pd

def load_transactions(
    file_path: str,
    date_format: Optional[str] = None,
) -> pd.DataFrame:
    pass
```

## Testing Requirements

### Test Structure

- Tests mirror source code structure
- Each module has a corresponding test file in `tests/`
- Test classes group related tests
- Use descriptive test names

### Test Naming

```python
class TestDataLoader:
    """Test cases for data loading functionality."""

    def test_load_csv_success(self):
        """Test successful CSV loading."""
        pass

    def test_load_csv_invalid_path(self):
        """Test CSV loading with invalid file path."""
        pass
```

### Test Coverage

- Aim for good test coverage
- Test both success and error cases
- Use fixtures for common setup
- Mock external dependencies when appropriate

### Running Tests

```bash
# All tests
pytest

# Specific test file
pytest tests/test_data_io.py

# Specific test
pytest tests/test_data_loader.py::TestDataLoader::test_load_csv_success

# With coverage
pytest --cov=src --cov-report=term-missing

# Verbose output
pytest -v

# Stop on first failure
pytest -x
```

## Pull Request Process

### Before Submitting

1. ✅ All tests pass locally
2. ✅ Code is formatted (Black + isort)
3. ✅ Documentation updated (if needed)
4. ✅ Commit messages follow conventions and include user story IDs
5. ✅ Branch is up to date with `dev`

### Review Process

1. **Create PR**: Push your branch and create a pull request
2. **Peer Review**: At least one team member must review your PR
   - Reviewers can approve or request changes
   - Address any feedback before merging
3. **Approval**: Once approved, the PR can be merged
4. **Merge**: Merge the PR into `dev` branch

## Additional Guidelines

### Reporting Bugs

When reporting bugs, include:
- Description of the issue
- Steps to reproduce
- Expected behavior
- Actual behavior
- Environment (OS, Python version, etc.)
- Error messages/logs
- Related user story ID (if applicable)

### Asking Questions

- Check existing documentation first
- Search closed PRs for similar questions
- Create a discussion or ask in team chat
- Be specific and provide context

## Getting Help

- **Documentation**: Check `docs/` directory and README.md
- **Code**: Review existing code for patterns
- **Team**: Reach out to team members

## License

By contributing, you agree that your contributions will be licensed under the same license as the project (MIT License).

---