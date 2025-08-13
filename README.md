# defaults

Project Defaults.

Instructions below will show how to append these defaults to your settings files,
if the files do not exist, these commends will create them.

**It is strongly advised that you check your config files
after you append these defaults and remove any duplicated of unnecessary entries.**

The below instructions assume that you have a `$PROJECT_DIR` envvar set
and that this repo is a child of that path.

something like this in your `~/.zshrc` file

```bash
export PROJECT_DIR="$HOME/Projects"
```

Replace `Projects` with whatever directory name you typically use for your git repo projects.

The instructions below also assume that you are inside your git repo root.

```bash
$ tree $PROJECT_DIR
/Users/bendog/Projects
├── defaults
│   ├── .editorconfig
│   ├── .gitignore
│   ├── .pre-commit-config.yaml
│   └── pyproject.toml
└── your_git_repo
$ cd $PROJECT_DIR/your_git_repo
```

## .editorconfig

Read the contents of `defaults/.editorconfg`
and append it to the end of your `.editorconfig` file.
This will prevent losing any settings you currently have.

```shell
cat "$PROJECT_DIR/defaults/.editorconfig" >> .editorconfig
```

## Pre-commit settings

Read the contents of `defaults/.pre-commit-config.yaml`
and append it to the end of your `.pre-commit-config.yaml` file.
This will prevent losing any settings you currently have.

```shell
cat "$PROJECT_DIR/defaults/.pre-commit-config.yaml" >> .pre-commit-config.yaml
```

pre-commit requires:
- local installation of <https://pre-commit.com/> on your system
- running `pre-commit install` in your repo **after adding the config file**.
- for python projects, you will need to add sensible settings into your `pyproject.toml` file,
see below.

## PyProject.toml for Python Projects

*I strongly advise that you use UV <https://github.com/astral-sh/uv>
 to initialise your python projects.
 Doing this uv will have already created a `pyproject.toml` file in your repo directory.*  
**You will want to use `uv init` BEFORE you add these defaults,
 it will not work the other way around.**

Read the contents of `defaults/pyproject.toml`
and append it to the end of your `pyproject.toml` file.
This will prevent losing any settings you currently have.

```shell
cat "$PROJECT_DIR/defaults/pyproject.toml" >> pyproject.toml
```

## .gitignore

This is a pretty generic default gitignore file for python projects.

```shell
cat "$PROJECT_DIR/defaults/.gitignore" >> .gitignore
```
