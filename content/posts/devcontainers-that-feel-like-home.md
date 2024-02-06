+++
title = "Devcontainers That Feel Like Home"
date = "2024-02-05"
[taxonomies]
  tags = ["docker", "vscode", "devcontainers", "shell", "zsh", "dotfiles"]
+++

[VSCode devcontainers](https://code.visualstudio.com/docs/remote/containers) are a great way to keep development environments consistent across machines.

Here are a few customizations I made to my default devcontainer to make it feel a bit more like home:

1. Zsh and [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh/wiki)
2. [Dotfiles](@/posts/easy-dotfiles-with-chezmoi.md)
3. Vim
4. Python and [Poetry](https://python-poetry.org/)

I walk through configuring these features below. This devcontainer configuration is available on my [public repo](https://github.com/gavinest/devcontainer-python). Feel free clone the repo and try it out in VSCode.

When VSCode opens the project, it will prompt to reopen the project in a devcontainer. If this prompt doesn't appear then open the command palette and search for "Dev Containers: Reopen in Container".

The first time it builds will take a little while. In the meantime Let's look at the configurations to see the customizations.

The `.devcontainer` folder contains the configuration files for the devcontainer.

```bash
.devcontainer
├── Dockerfile
├── devcontainer.json
└── post_create.sh
```

Python and Vim are installed in the `.devcontainer/devcontainer.json` file using available [features](https://containers.dev/features).

```json
...
 "features": {
  "ghcr.io/devcontainers-contrib/features/curl-apt-get:1": {},
  "ghcr.io/devcontainers/features/git:1": {},
  "ghcr.io/guiyomh/features/vim:0": {},
  "ghcr.io/devcontainers/features/python:1": {
   "version": "3.11"
  },
  "ghcr.io/devcontainers/features/docker-outside-of-docker:1": {}
 },
...
```

To install [dotfiles during set-up](https://code.visualstudio.com/docs/devcontainers/containers#_personalizing-with-dotfile-repositories) the VSCode `settings.json` must be configured.

My settings.json file looks like this:
  
```json
// settings.json
...
  "dotfiles.repository": "gavinest/dotfiles",
  "dotfiles.targetPath": "~/dotfiles",
  "dotfiles.installCommand": "install.sh",
...
```

The [install.sh](https://github.com/gavinest/dotfiles/blob/main/install.sh) script in my dotfiles repo is used by VSCode to set up the dotfiles. I take this opporunity to configure Zsh and Oh My Zsh and Vim plugins as well.

Finally, the `.devcontainer/post_create.sh` script runs after the devcontainer is created. This is a good place to install any additional tools or configurations. I use it to install the project Python dependencies with Poetry so my virtual environment is good to go once I enter the container. I also have some commands here to persist the .zsh_history. That was the subject of another [post](@/posts/devcontainers-persist-zsh-history.md).

```bash
#!/usr/bin/env bash
set -euxo pipefail

mkdir -p /commandhistory
touch /commandhistory/.zsh_history
chown -R ${USER} /commandhistory

echo "autoload -Uz add-zsh-hook; append_history() { fc -W }; add-zsh-hook precmd append_history; export HISTFILE=/commandhistory/.zsh_history" >> /home/${USER}/.zshrc
echo "PATH=\".venv/bin:${PATH}\"" >> /home/${USER}/.zshrc

pip install --upgrade pip \
    && pip install poetry \
    && poetry config virtualenvs.in-project true \
    && poetry config cache-dir ./ \
    && poetry install --with=dev --no-root
```
