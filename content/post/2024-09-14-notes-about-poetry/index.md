---
title: Notes about poetry
description: ""
date: 2024-09-14T03:52:17.166Z
preview: ""
draft: false
tags: [Python, Poetry]
categories: [Python]
image: "https://images.unsplash.com/photo-1598738865218-7809c17181c3?q=80&w=2574&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
slug: notes-about-poetry
---
## Why Poetry?

Aside from the traditional `requirements.txt` / `setup.py` and `conda` solutions, `Poetry` is a modern Python packaging and dependency management tool that leverages the `pyproject.toml` file to manage dependencies and build packages. 

## Why `pyproject.toml`?
To start with poetry we must first introduce the `pyproject.toml` file. This file is a configuration file that is used to manage the dependencies and build process of a Python package. It is a modern alternative to the `setup.py` file. (Check [PEP 518](https://peps.python.org/pep-0518/) for more information.)

As a simple example, here is a `pyproject.toml` file that is generated from poetry:

```toml
[tool.poetry]
name = "awesome-package"
version = "0.1.0"
description = "An awesome package"
authors = ["Your Name <youremail@example.com>"]
license = "MIT"
readme = "README.md"
homepage = "https://github.com/username/awesome-package"

[tool.poetry.dependencies]
python = "^3.8"
numpy = "^1.20.0"
requests = "^2.25.0"

[tool.poetry.dev-dependencies]
pytest = "^6.0"
black = "^22.0"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

In order to do the same with `setup.py`, you need to write:

```python
from setuptools import setup, find_packages

setup(
    name="awesome-package",
    version="0.1.0",
    description="An awesome package",
    author="Your Name",
    author_email="youremail@example.com",
    license="MIT",
    packages=find_packages(),
    install_requires=[
        "numpy>=1.20.0",
        "requests>=2.25.0"
    ],
    extras_require={
        "dev": [
            "pytest>=6.0",
            "black>=22.0"
        ]
    }
)
```

The pipeline is actually mixed with python code, which is not very clean. `pyproject.toml` on the other hand provides a cleaner and more structured way to manage dependencies and build process.

## Functions
Poetry not only handles the dependencies, but also creates a virtual environment automatically, builds the package, and (potentially) publishes it to PyPI through a unified interface.

Some functionalities include:
- `poetry new`: Create a new Python package.
- `poetry install`: Install dependencies based on the `pyproject.toml` file. This also creates a virtual environment.
- `poetry add <package>`: Add a new package to the dependencies.
- `poetry remove <package>`: Remove a package from the dependencies.

It also enables switching between dev and production environments.
- `poetry install --only main`: Install only the production dependencies.
- `poetry add --group dev <package>`: Add a new package to the development dependencies.

> The new poetry uses `--group` instead of `--dev`.

## Poetry virtual environment
One key feature that is not so intuitive is that poetry __automatically__ creates a virtual environment for each project.
### In-project virtual environment
The virtualenv is usually stored in some system folder, but you can also let it init a `.venv` folder in your project root by setting `virtualenvs.in-project` option to `true`:

```bash
poetry config virtualenvs.in-project true
```

### Running the scripts
Some may find that if they directly run the script, the dependencies are not found. This is because the virtual environment is **not automatically activated**, so you need to run the scripts in the virtual environment by prefixing the command with `poetry run`:

```bash
poetry run python my_script.py
```

or, you can create a shell in the virtual environment, and run the scripts in the shell:

```bash
poetry shell
python my_script.py
```

### Configuring in vscode
If you are using the in-project virtual environment, vscode will automatically configure it for you. 

If not, you can manually set the python interpreter to the virtual environment by clicking on the python version at the bottom.

First, use poetry to find the path of the virtual environment:

```bash
poetry env info --path
```

Then, in vscode:
- Click on the python version at the bottom, and select "Enter interpreter path". 
- Paste the path of the virtual environment and select the python executable.

### About environment info
If you run `poetry env info`, you will see two parts: "Virtualenv" and "Base". The "Virtualenv" part is the virtual environment of the project, and the "Base" part is the system global python environment.