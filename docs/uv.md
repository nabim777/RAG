> More about this in https://github.com/astral-sh/uv


## 🧰 What is uv?

uv is a fast Python package manager and project manager designed as a replacement for tools like:
- pip
- pip-tools
- Poetry
It’s written in Rust, which makes it extremely fast compared to traditional Python tools.


## ⚡ What makes uv special?
- Very fast installs (often 10–100× faster than pip)
- Handles virtual environments automatically
- Locks dependencies (like poetry.lock)
- Single tool for installing, resolving, and running Python projects
- Compatible with existing Python ecosystems (like requirements.txt)

📦 What can you do with it?

Install packages:

```zsh
uv pip install requests
Create and manage virtual environments
```

Sync dependencies:

```zsh
uv sync
Run Python scripts with dependencies easily
```

## Installation

simply can be install follow this [link](https://github.com/astral-sh/uv?tab=readme-ov-file#installation) or simply with this command:

```zsh
curl -Ls https://astral.sh/uv/install.sh | sh 
```

```zsh
source ~/.zshrc
```

```zsh
uv --version
```

### Work in specific version of python
> for specific version of python we can do
```
uv python install 3.13
uv venv --python 3.13
uv run python --version
```
