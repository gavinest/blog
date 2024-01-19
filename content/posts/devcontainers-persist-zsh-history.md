+++
title = "Devcontainers: Persist .zsh_history"
date = "2024-01-19"
[taxonomies]
  tags = ["vscode", "devcontainers", "zsh", "dotfiles"]
+++

VSCode has documentation for [persisting .bash_history in devcontainers](https://code.visualstudio.com/remote/advancedcontainers/persist-bash-history).

But what about zsh users? How do we persist _.zsh\_history_ in devcontainers?

This post walks through how I did it.

## Explanation

Familiarize yourself with the short docs for [persisting .bash_history](https://code.visualstudio.com/remote/advancedcontainers/persist-bash-history). We are going to use the same methodology but swap out the bash specific commands with zsh equivalents.

First change _.bash\_history_ --> _.zsh\_history_ and _.bashrc_ --> _.zshrc_, but also we need to change the _SNIPPET_.

```bash
# The VSCode docs for bash say to use the following SNIPPET.
SNIPPET="export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history"

# We'll use this SNIPPET for zsh
SNIPPET="autoload -Uz add-zsh-hook; append_history() { fc -W }; add-zsh-hook precmd append_history; export HISTFILE=/commandhistory/.zsh_history"
```

Here's what's happening.

`PROMPT_COMMAND` is a bash specific environment variable that is executed before the prompt (shell) is displayed. Zsh achieves the same functionality with hooks. In this case we use the _precmd_ hook. Zsh has other hooks too (see _precmd_ and _chpwd_).

The zsh hook takes a function. Hence the `append_history() { fc -W }` function. The _fc -W_ is equivalent to the _history -a_ from the bash commmand. It writes the history to the file specified by _HISTFILE_.

`HISTFILE` is common between bash and zsh. It tells the shell where to write/read the history. The shells default to _~/.bash\_history_ and _~/.zsh\_history_ respectively.

In short the SNIPPET is saying when a shell session is opened first set the new location of the `.zsh_history` file.

## Putting It Together

My solution comes out looking like the below.

```Dockerfile
# Dockerfile

...

ARG USERNAME=vscode

RUN SNIPPET="autoload -Uz add-zsh-hook; append_history() { fc -W }; add-zsh-hook precmd append_history; export HISTFILE=/commandhistory/.zsh_history" \
    && mkdir /commandhistory \
    && touch /commandhistory/.zsh_history \
    && chown -R $USERNAME /commandhistory \
    && echo "$SNIPPET" >> "/home/$USERNAME/.zshrc"

...

```

```json
// devcontainer.json

...

 "mounts": [
  "source=devcontainer-zshhistory,target=/commandhistory,type=volume"
 ],

...

```

## Another Problem Emerges. Dotfile Managers

Are you using [dotfiles in your devcontainer](https://code.visualstudio.com/docs/devcontainers/containers#_personalizing-with-dotfile-repositories)? If you are using [Chezmoi](./easy-dotfiles-with-chezmoi.md) or another dotfile manager, you may have noticed that the _SNIPPET_ above does not take effect because the dotfile manager is overriding the _.zshrc_ file that the _SNIPPET_ was appeneded to.

The Solution:

I solved this by moving the _SNIPPET_ to a [_postCreateCommand_](https://containers.dev/implementors/json_reference/) in the devcontainer.json file.  In the end my solution looks like the below. The Dockerfile commands moved to a `post_create.sh` script to be called by the _postCreateCommand_.

```json
// devcontainer.json

...

 "mounts": [
  "source=devcontainer-zshhistory,target=/commandhistory,type=volume"
 ],

...

 "postCreateCommand": "bash .devcontainer/post_create.sh",

...

```

```bash
# post_create.sh

#!/usr/bin/env bash
set -euxo pipefail

mkdir -p /commandhistory
touch /commandhistory/.zsh_history
chown -R ${USER} /commandhistory

echo "autoload -Uz add-zsh-hook; append_history() { fc -W }; add-zsh-hook precmd append_history; export HISTFILE=/commandhistory/.zsh_history" >> /home/${USER}/.zshrc
```
