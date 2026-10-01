###############################
#CLAUDE.md (Project10)
###############################
  
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Purpose

To perform survey estimation using Python's `svy` library in an equivalent way to using `svy` commands in Stata. 
It involves tasks such as (1) importing a Stata dataset in a format needed for `svy`, (2) setting negative values on the outcome variable to missing; (3) defining the survey design in a similar way to Stata's `svyset`, and (4) performing survey estimation to estimate the mean of the outcome variable after accounting for the complex survey design.

## Tasks

Execute a Jupyter Notebook file (extension .ipynb) using Visual Studio Code. 
The task involves opening a new Jupyter Notebook file, running a script to perform survey estimation using the specified outcome variable, and saving the new notebook in the `code\` folder.

## File Paths

- Working directory: `C:\CLAUDE\Projects\Project10`
- Notebook files: `code\`
- Log files, tables, figures: `output\`
- Datasets: `data\`
- Examples of notebook files: `examples\`

## Running a notebook script in VSC

- Within VSC create a new Jupyter Notebook file with the file name of the specified outcome variable (e.g. wealth.ipynb) 
- Write the python script with the following workflow

```python
#import libraries
#load the survey dataset that is in the `data\` folder
#set rows with negative values on the outcome variable to missing
#define the survey design using stratum=strata; psu=psu; wgt=wt_int
#create the sample object
#remove records with missing values on the outcome
#Estimate population mean of outcome variable with design-based standard error

```

- Run the new notebook file from within VSC.
- Save the notebook file in the code folder within the working directory using the specified outome variable as the filename (e.g. wealth.ipynb)

Verify the expected `.ipynb` file exists in `code\` before reporting the task as complete.

### Implementation notes (learned when running wealth.ipynb)

- Required packages (confirmed installed, Python 3.14): `svy` (0.18.2), `polars`, `pyreadstat`, `nbconvert`.
- Set `os.environ["POLARS_SKIP_CPU_CHECK"] = "1"` (and pop `POLARS_FEATURE_FLAGS`) **before** importing `svy`/`polars`, as in the example.
- Read the `.dta` with `pyreadstat.read_dta(...)` then `pl.from_pandas(df)`; svy needs a Polars DataFrame. Cast `psu` to `pl.String` then `pl.Categorical`.
- nbconvert runs the kernel with `code\` as the working directory, so the data path in the notebook must be `"../data/survey_data.dta"` (the example's `"data/..."` only works when run from the project root).
- Remove missing outcome records with `svy.col("<var>").is_not_null()`, not `> 0` as in the example: `> 0` would also drop legitimate zero values, which Stata's `svy: mean` keeps.
- Equivalent Stata: `svyset psu [pw=wt_int], strata(strata)` then `svy: mean <var>`. The CI uses t with design df = #PSUs - #strata.
- Console output of notebook text can contain Unicode box characters; set `PYTHONIOENCODING=utf-8` when printing outputs in the terminal.

## Enable CLAUDE to create / edit notebook files in VSC

- `.claude\settings.json` (project-level) has been created with permissions allowing Claude to Read/Write/Edit any file under `C:\CLAUDE\Projects\Project10\`, create directories under the project, and run `python`, `jupyter`, and `pip install` commands without prompting each time.

### How notebook execution was actually performed

Execute the notebook with the CLI tool that VS Code's Jupyter extension itself calls under the hood (same kernel, same outputs):

```
python -m nbconvert --to notebook --execute --inplace "code\<name>.ipynb"
```

This runs every cell against a real Python kernel and writes the outputs back into the `.ipynb` file in place, so opening the file afterward in VS Code shows already-executed cells. Required packages confirmed present on this machine: `pandas`, `numpy`, `ipykernel`, `nbconvert`, `nbclient`, `nbformat`.

Steps to reproduce the task:
1. Write a new `.ipynb` file into `code\` containing the desired code cell(s) (nbformat 4 JSON).
2. Run the `nbconvert --execute --inplace` command above to execute it and populate outputs.
3. Verify the file exists in `code\` and that outputs were populated (no error traceback in the cell output).

## Directory Layout

- `code\` — Python scripts and notebooks (e.g. `wealth.ipynb`)
- `output\` — generated files: logs, tables, figures
- `data\` — input dataset (survey dataset in Stata format .dta) to be imported into python


## Script Template

```python
# Libraries
import pandas as pd

# Commands
# ... analysis code ...

# Timestamp
import datetime
print(f"Program completed on: {datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")
```

## Git and GitHub

Remote: `https://github.com/shauns11/Claude---Project10.git` (branch `main`). The GitHub CLI (`gh`) is not installed, so use plain `git` and the GitHub website.

### First-time setup (new project)

1. Create `.gitignore` in the project root **before** the first commit, so ignored files are never committed:

```text
# Secondary logs created by batch mode (/e) in the project root
/*.log

# Stata datasets (anywhere in the project)
*.dta

# R datasets (anywhere in the project)
*.rds

# Claude Code local settings
.claude/settings.json
```

2. Initialise the repository, check what will and won't be committed, then commit:

```powershell
git init -b main
git add .
git status --short             # files to be committed
git status --short --ignored   # lines starting "!!" are ignored (e.g. 01.log)
git commit -m "Initial commit"
```

3. Create an **empty** repository on github.com (no README, .gitignore or licence) and choose Public or Private.
4. Before pushing, confirm the remote exists and is empty. `git ls-remote` returns nothing for an empty repo and "Repository not found" if the URL is wrong, deleted or private without access:

```powershell
git ls-remote https://github.com/shauns11/Claude---Project10.git
```

5. Add the remote and push `main`:

```powershell
git remote add origin https://github.com/shauns11/Claude---Project10.git
git push -u origin main
git status -sb                 # should show: ## main...origin/main
```

6. Update the `Remote:` line at the top of this section.

Notes:
- Never use `git push --force` against a repository that already has history unless you intend to permanently replace it.
- Warnings like "LF will be replaced by CRLF" are Windows line-ending notices and can be ignored.

### Day-to-day

```powershell
git status                 # see what changed
git add .                  # stage changes
git commit -m "Message"    # commit
git push                   # upload to GitHub
```

### What is tracked

- Tracked: `code\` (Jupyter notebooks), `output\` (logs, tables, figures), `examples\` (example notebooks), `CLAUDE.md`, `.gitignore`
- Ignored (see `.gitignore`):
  - `/*.log` — root-level logs created by batch mode
  - `*.dta` — Stata datasets, anywhere in the project
  - `*.rds` — R datasets, anywhere in the project
  - `.claude/settings.json` — Claude Code local permission settings (kept on disk, not uploaded)

  To save future changes, run git add ., then git commit -m "message", then git push.


## Additions to CLAUDE.md

Please add any settings necessary to complete this task to the CLAUDE.md file so that claude can create/edit files and perform common filesystem operations such as creating directories without repeatedly asking you. Please add steps necessary to perform task in the CLAUDE.file

## Examples

Example code is provided in "C:\CLAUDE\Projects\Project10\examples\income.ipynb"

