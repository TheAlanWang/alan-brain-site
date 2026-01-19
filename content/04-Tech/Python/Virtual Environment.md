> A **virtual environment (`venv`)** is an **isolated Python workspace** for a project.

Q: Why need virtual environment
- Avoid dependency conflicts between projects
- Keep system Python clean
- Make installs reproducible via `requirements.txt`
## Environment Setup

### Prerequisites
- Python 3.10+ (recommended)
- `pip` available (comes with Python)    
## macOS / Linux
### 1) Create virtual environment
```zsh
cd /project
python3 -m venv .venv
```

![[Pasted image 20260113234513.png|300]]
### 2) Activate the environment (macOS / Linux)
```bash
source .venv/bin/activate
```
After successfully activating the virtual environment, your prompt will look like:
`(.venv) alanwang@...`
### 3) Upgrade pip (recommended)
```bash
python -m pip install --upgrade pip
```

### 4) Install dependencies
```zsh
# Install dependencies from requirements.txt
pip install -r requirements.txt
```

Install a single package
```bash
# Install dependencies
pip install <package-name>
```
#### Export dependencies
```zsh
pip freeze > requirements.txt
```

### 5) Deactivate Virtual Environment
After Activate successfully `source .venv/bin/activate`
```zsh
deactivate
```

---
Linked to: [[env Guide]]