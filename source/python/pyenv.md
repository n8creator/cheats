# Pyenv

## 1. Pyenv basics


### 1.1. Pyenv installation

Install system depencencies for Ubuntu first (more info: [
Suggested build environment (GitHub, pyenv)](https://github.com/pyenv/pyenv/wiki)):

```sh
sudo apt-get update; sudo apt-get install make build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
```

### 1.2. Python installation

Here is main commands to install & update `pyenv` itself:

```sh
$ curl https://pyenv.run | bash  # Install pyenv
$ pyenv update                   # Update pyenv
```

With pyenv installed, you can install any Python version that you like:

```sh
$ pyenv install --list   # See all available python installations

$ pyenv install 3.10.0   # Install some Python version

$ pyenv versions         # See all Python versions that had been installed

$ pyenv version          # See which python is currently being used
```

Pyenv may be installed _globally_ (system-wide) or _locally_ (within some folder):

```sh
$ pyenv global 3.7.10    # Set the default/global from one of the python versions

$ pyenv local 3.8.7      # Set the python version in the current directory.
                         # This creates the file .python-version.
```

An asterisk at the beginning of the line points to the current global version:

```sh
$ pyenv versions

system
* 3.7.10 (set by /home//.pyenv/version)
3.8.7
3.9.2
```

## 2. Pyenv & Poetry configuration

This is how the basic process of creating a new project with Poetry and specifying the Python version via `pyenv` looks like:

```sh
$ mkdir test && cd $_
$ pyenv local 3.10.0

$ poetry init
$ poetry shell

(.venv) n8creator@n8creator:~/test$ python -V
Python 3.10.0
```

Python version must be specified in `pyproject.toml` file:

```toml
[tool.poetry]
...

[tool.poetry.dependencies]
python = "^3.10"

[tool.poetry.dev-dependencies]
...

[build-system]
...
```

## 3. Useful links

- [pyenv installer (GitHub Repo)](https://github.com/pyenv/pyenv-installer)
- [Get started with pyenv & poetry. Saviours in the python chaos! (blog.jayway.com)](https://blog.jayway.com/2019/12/28/pyenv-poetry-saviours-in-the-python-chaos/)
- [Modern Python: How to start your project from scratch with Pyenv and Poetry (Habr.com, in Russian)](https://habr.com/ru/post/599441/)