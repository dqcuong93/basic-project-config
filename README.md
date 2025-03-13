# Code Formatter Guide-line

## Table of Contents

- [Overview](#overview)
- [Configuration Files](#configuration-files)
  - [EditorConfig](#editorconfig)
  - [PyProject](#pyproject)
  - [Flake8 Configuration](#flake8-configuration)
- [Tools Overview](#tools-overview)
  - [Isort](#isort)
  - [Black](#black)
  - [Flake8](#flake8)
- [Installation](#installation)
- [Usage Guide](#usage-guide)
  - [Code Formatting](#code-formatting)
  - [Type Checking](#type-checking)
  - [Security](#security)
- [Best Practices](#best-practices)
  - [Code Quality](#code-quality)
  - [Type Safety](#type-safety)
  - [Security Best Practices](#security-best-practices)
- [Troubleshooting](#troubleshooting)
  - [Common Issues and Solutions](#common-issues-and-solutions)
- [CI/CD Integration](#cicd-integration)
  - [GitHub Actions Workflow Example](#github-actions-workflow-example)
- [Author](#author)
- [License](#license)

## Overview

This guide provides a comprehensive approach to maintaining consistent code quality across Python projects through automated formatting tools and best practices.

### Key Benefits

- Ensures consistent code style across the project
- Reduces time spent on manual formatting
- Minimizes code review friction
- Improves code readability and maintainability

## Configuration Files

Three essential configuration files form the foundation of our code formatting setup:

### EditorConfig

The `.editorconfig` file ensures consistent coding styles across different editors and IDEs by defining project-wide formatting rules.

### PyProject

The `pyproject.toml` file serves as a central configuration hub for Python tools like Isort and Black, defining project-specific settings and behaviors.

### Flake8 Configuration

The `.flake8` file configures Flake8's behavior, including custom rules, ignored errors, and plugin settings.

## Tools Overview

### Isort

Isort is an import sorting utility that automatically organizes your Python imports.

**Features:**

- Alphabetical sorting
- Import grouping by type
- Automatic section separation

Reference: <https://pypi.org/project/isort/>

### Black

Black is an opinionated code formatter that ensures consistent Python code style.

**Key Features:**

- Deterministic formatting
- PEP 8 compliance
- Editor integration support

Reference: <https://black.readthedocs.io/en/latest/>

### Flake8

Flake8 combines multiple Python code quality tools into a single package.

**Components:**

- PyFlakes for error detection
- pycodestyle for PEP 8 checking
- McCabe for complexity checking

Reference: <https://pypi.org/project/flake8/>

## Installation

Install all development dependencies with a single command:

```zsh
pip install -r requirements-dev.txt
```

## Usage Guide

### Code Formatting

#### Format code with Black

```zsh
# Format a single file
black path/to/file.py

# Format all Python files in current directory
black .

# Check what would be formatted without making changes
black --check .
```

#### Sort imports with isort

```zsh
# Sort imports in a single file
isort path/to/file.py

# Sort imports in all Python files
isort .

# Check what would be sorted without making changes
isort --check-only .
```

#### Check code with Flake8

```zsh
# Check a single file
flake8 path/to/file.py

# Check all Python files
flake8 .

# Check with more detailed output
flake8 --statistics .
```

### Type Checking

#### Using MyPy

```zsh
# Check a single file
mypy path/to/file.py

# Check all Python files
mypy .

# Check with strict mode
mypy --strict path/to/file.py

# Check with detailed output
mypy --pretty path/to/file.py
```

#### Common Type Checking Issues

1. **Missing Type Annotations**

   ```python
   # Bad
   def process_data(data):
       return data

   # Good
   from typing import List, Dict
   
   def process_data(data: List[Dict[str, str]]) -> List[Dict[str, str]]:
       return data
   ```

2. **Any Type Usage**

   ```python
   # Bad
   from typing import Any
   
   def process(data: Any) -> Any:
       return data

   # Good
   from typing import TypeVar, Generic
   
   T = TypeVar('T')
   
   def process(data: T) -> T:
       return data
   ```

3. **Optional Types**

   ```python
   # Bad
   def get_user(id: int) -> str:
       if id == 0:
           return None
       return "User"

   # Good
   from typing import Optional
   
   def get_user(id: int) -> Optional[str]:
       if id == 0:
           return None
       return "User"
   ```

### Security

#### Using Bandit for Security Analysis

```zsh
# Check a single file
bandit -r path/to/file.py

# Check all Python files
bandit -r .

# Check with specific confidence level
bandit -r . -c 2

# Generate HTML report
bandit -r . -o report.html
```

#### Using Safety for Dependency Security

```zsh
# Check installed packages
safety check

# Check specific requirements file
safety check -r requirements.txt

# Update safety database
safety update
```

#### Common Security Issues

1. **SQL Injection**

   ```python
   # Bad
   query = f"SELECT * FROM users WHERE id = {user_id}"

   # Good
   from sqlalchemy import text
   query = text("SELECT * FROM users WHERE id = :user_id")
   ```

2. **Hardcoded Secrets**

   ```python
   # Bad
   API_KEY = "secret123"

   # Good
   import os
   API_KEY = os.getenv("API_KEY")
   ```

3. **Unsafe YAML Loading**

   ```python
   # Bad
   import yaml
   data = yaml.load(file)

   # Good
   import yaml
   data = yaml.safe_load(file)
   ```

## Best Practices

### Code Quality

1. **Pre-commit Integration**
   - Run formatters before committing code
   - Use pre-commit hooks for automation
   - Keep configuration in version control

2. **Dependency Management**
   - Update dependencies regularly
   - Run security checks on dependencies
   - Monitor for vulnerabilities

3. **Workflow Integration**
   - Run all checks before pushing
   - Integrate with CI/CD pipelines
   - Maintain consistent standards

### Type Safety

1. **Type Annotation Guidelines**
   - Always use type annotations
   - Avoid `Any` type when possible
   - Leverage `TypeVar` for generics

2. **MyPy Configuration**
   - Enable strict mode
   - Use Protocol for structural subtyping
   - Configure per-module settings

### Security Best Practices

1. **Code Security**
   - Follow OWASP guidelines
   - Implement input validation
   - Use environment variables for secrets

2. **Dependency Security**
   - Regular security audits
   - Keep dependencies updated
   - Monitor security advisories

## Troubleshooting

### Common Issues and Solutions

1. **Black and isort conflicts**
   - Use `isort --profile black` to ensure compatibility
   - Run Black first, then isort

2. **Flake8 errors**
   - Check the error code in the output
   - Common fixes:
     - E203: Whitespace before ':'
     - W503: Line break before binary operator
     - F401: Imported but unused

3. **Docstring issues**
   - D100-D104: Missing docstrings (module, class, function, etc.)
   - D200-D213: Docstring formatting issues
   - D400-D419: Docstring content issues
   - Common fixes:
     - Use Google style docstrings
     - Include required sections (Args, Returns, Raises)
     - Use double quotes for docstrings
     - Ensure minimum length of 10 characters

4. **Editor Integration**
   - VSCode: Install Python extension and enable format on save
   - PyCharm: Enable Black and isort in settings
   - Sublime Text: Install Python packages and enable format on save

## CI/CD Integration

### GitHub Actions Workflow Example

```yaml
name: Type Check and Security Scan

on: [push, pull_request]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Set up Python
        uses: actions/setup-python@v2
      - name: Install dependencies
        run: pip install -r requirements-dev.txt
      - name: Run type checking
        run: mypy .
      - name: Run security checks
        run: |
          bandit -r .
          safety check
```

## Author

- **Chris Dao** - <https://github.com/dqcuong93>

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
