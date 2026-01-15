Q: Why need virtual environment
- Avoid dependency conflicts between projects
- Keep system Python clean
- Make installs reproducible via `requirements.txt`
### Create virtual environment
```zsh
cd /project
python3 -m venv .venv
```

![[Pasted image 20260113234513.png|300]]

Install dependencies
```zsh
python -m pip install --upgrade pip
pip install <package-name>
```

Export dependencies
```zsh
pip freeze > requirements.txt
```

Reinstall dependencies
```zsh
pip install -r requirements.txt
```

### Activate Virtual Environment 
```zsh
source .venv/bin/activate
```

```
# update pip
python -m pip install --upgrade pip
```

After successfully activating the virtual environment, your prompt will look like:
`(.venv) alanwang@...`
### Deactivate Virtual Environment
```zsh
deactivate
```