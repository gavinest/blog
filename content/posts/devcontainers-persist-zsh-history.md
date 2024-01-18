+++
title = "Devcontainers: Persist Zsh History"
date = "2024-01-21"
[taxonomies]
  tags = ["vscode", "devcontainers", "zsh", "dotfiles"]
+++

VsCode has documentation for [persisting .bash_history in devcontainers](https://code.visualstudio.com/remote/advancedcontainers/persist-bash-history).

But what about zsh users? How do we persist _.zsh_history_ in devcontainers?

This post walks through how I did it.

## Explanation

Familiarize yourself with the docs for [persisting .bash_history](https://code.visualstudio.com/remote/advancedcontainers/persist-bash-history). We are going to use the same methodology but swap out the bash specific commands with zsh equivalents.

First change _.bash_history_ --> _.zsh_history_ and _.bashrc_ --> _.zshrc_, but also we need to change the _SNIPPET_.

```bash
# The VsCode docs for bash say to use the following SNIPPET.
SNIPPET="export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history"

# SNIPPET for zsh
SNIPPET="autoload -Uz add-zsh-hook; append_history() { fc -W }; add-zsh-hook precmd append_history; export HISTFILE=/commandhistory/.zsh_history"
```

Here's what's happening.

`PROMPT_COMMAND` is a bash specific environment variable that is executed before the prompt is displayed. Zsh achieves the same functionality with hooks. In this case we use the _precmd_ hook. Zsh has other hooks too (see _precmd_ and _chpwd_).

The hook takes a function. Hence the `append_history() { fc -W }` function. The _fc -W_ is equivalent to the _history -a_ from the bash commmand. It writes the history to the file specified by _HISTFILE_.

`HISTFILE` is common between bash and zsh. It tells the shell where to write the history. The shells default to _~/.bash_history_ and _~/.zsh_history_ respectively.

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
  "source=devcontainer-zshistory,target=/commandhistory,type=volume"
 ],

...
```

## Another Problem Emerges. Dotfile Managers

Are you using [dotfiles in your devcontainer](https://code.visualstudio.com/docs/devcontainers/containers#_personalizing-with-dotfile-repositories)? If you are using [Chezmoi](./easy-dotfiles-with-chezmoi.md) or another dotfile manager, you may have noticed that the _SNIPPET_ above does not take effect because the dotfile manager is overriding the _.zshrc_ file that the _SNIPPET_ was appeneded to.

## The Solution

I solved this by moving the _SNIPPET_ to a [_postCreateCommand_](https://containers.dev/implementors/json_reference/) in the devcontainer.json file. In the end my Dockerfile and devcontainer.json files look like the below.

```Dockerfile
# Dockerfile
ARG USERNAME=vscode

RUN mkdir /commandhistory \
    && touch /commandhistory/.zsh_history \
    && chown -R $USERNAME /commandhistory

```

```json
// devcontainer.json

...

 "mounts": [
  "source=devcontainer-zshistory,target=/commandhistory,type=volume"
 ],

...

 "postCreateCommand": "echo \"autoload -Uz add-zsh-hook; append_history() { fc -W }; add-zsh-hook precmd append_history; export HISTFILE=/commandhistory/.zsh_history\" >> /home/vscode/.zshrc",

...
```

If this all seems like a lot of effort just to persist _.zsh_history_, you're correct.
