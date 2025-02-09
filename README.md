# python-tips
Tips for using Python


## 1. Manage python versions wiyth `pyenv`
> [!NOTE]
> `pyenv` only works on Mac or Linux. On Windows you can use the official `py` launcher.

See the user manual for `pyenv`:
```bash
pyenv --help
```

See a list of available versions:
```bash
pyenv install --list
```

Filter to only "3.11":
```bash
pyenv install --list | grep 3.11
```

Install a specific version:
```bash
pyenv install 3.11.7
```

You can install multiple versions and switch between them.
```bash
pyenv install 3.12.1
```

Select a specific Python version:
```bash
pyenv global 3.12.1  # globally in the shell
pyenv local 3.11.7   # locally in the current folder
```

Check the path to python interpreter
```bash
pyenv which python
```

## 2. Manage project python dependencies with `poetry`
Lookup manual pages with `--help`. See all commands with `list`:
```bash
poetry --help
poetry list
```
Create a poetry project:
```bash
poetry init
```
This will add a `pyproject.toml` file in the current folder.

### Define dependecies in the toml file
Add project main dependencies, e.g. cognite sdk, pandas, and pydantic:
```toml
[tool.poetry.dependencies]
python = "^3.11.0"
cognite-sdk = "^7.52.3"
pandas = "^2.2.1"
pydantic = "^2.6.0"
```

Dependency specification syntax: [poetry docs](https://python-poetry.org/docs/dependency-specification/).

To be able to use a jupyter notebook install the following development dependencies:

```toml
[tool.poetry.group.dev.dependencies]
ipykernel = "*"
jupyter = "*"
```

### Create a virtual environment with the selected pyenv interpreter

```bash
# set the local python version
pyenv local 3.11.7
# save the interpreter path to a variable
SELECTED_PY_PATH=$(pyenv which python)
poetry env use "$SELECTED_PY_PATH"
```
This will create a virtual environment based on the selected pyenv environment.

You can check the environment:
```bash
poetry env info
```
It will print a table with virtual env info and base. You will need the executable path from the virtual env to set up the Python path in the code editor later.

You can easily get this path with the command:
```bash
poetry env info | awk '/^Executable/{print $2}' | head -1
```
This command prints the info, then uses `awk` cli tool to find all lines that start with `"Executable"` string, outputs the second column (assuming space separated data), then takes only the first row using `head`.

### Set the python interpreter in VS Code

Open the current folder in VS Code.
```bash
code .
```

Open the command palette (CMD + P). Start typing "Python: Select Interpreter". You should see the command highlighted. Tap the Enter key. Select "Enter interpreter path". Insert the python executable path that we got in the previous step from poetry env info.
![VS Code Command Palette with "Python: Select Interpreter" highlighted](vs_code_interpreter.png "VS Code Command Palette with 'Python: Select Interpreter' highlighted").

Now your editor should correctly highlight the code and. You may need to restart VS Code before it takes effect.

### Install dependencies
```bash
poetry install
```
This will install the dpendencies from the lock file (if available) or based on the pyporject.toml and then create a lock file.

You can update dependenices if new versions matching the version requirment spec are available.
```bash
poetry update
```

### Run python code
You need to use the correct python interpreter so that all the dependencies are found correctly. `poetry run` uses the correct virtual environment.
```bash
poetry run python demo_script.py
```
Or you can active the virtual environment first and the run the python script:
```bash
poetry shell

python demo_script.py
```
