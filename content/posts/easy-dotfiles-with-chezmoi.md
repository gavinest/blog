+++
title = "Easy Dotfiles with Chezmoi"
date = "2024-01-06"
[taxonomies]
  tags = ["dotfiles", "bash"]
+++

# Introduction

I highly recommend the free MIT course: [The Missing Semester of Your CS Education](https://missing.csail.mit.edu/2020/command-line/).

When I first took the course a few years ago the [dotfiles](https://missing.csail.mit.edu/2020/command-line/) section spawned something I hadn't considered. While I was long familiar with ubiquitous dotfiles (i.e. .bash_profile or .vimrc) for settings management, I never thought about managing or versioning them! And I especially never considered making them usable across machines.

That set me on a search for a solution!

I looked at a few tools and even started using [GNU Stow](https://www.gnu.org/software/stow/) for a while, but that was before I found [Chezmoi](https://www.chezmoi.io/).

Chezmoi is a command-line tool that wrangles your dotfiles chaos. It takes care of synchronization, versioning and sharing, allowing you to keep a consistent environment across various systems. It is easy to [get started](https://www.chezmoi.io/quick-start/) with and dotfiles can be added gradually over time.

See [my dotfiles repo](https://github.com/gavinest/dotfiles) managed by Chezmoi.

## Common Commands

Here are commands you'll use 95% of the time, but Chezmoi is capable of much more. See [Chezmoi features](#chezmoi-features) below.

```bash
# add a dotfile to Chezmoi management
chezmoi add ~/.zshrc

# edit a dotfile file
chezmoi edit ~/.zshrc

# see the change that would be applied
chezmoi diff

# apply the change
chezmoi apply

# change to the chezmoi directory to commit changes
chezmoi cd
```

## Chezmoi Features

- **<ins>Version Control</ins>**: Chezmoi is built on top of Git, making it easy to track changes, revert to previous versions, and understand updates.

- **<ins>Variation Across Machines</ins>**: Your work and personal laptop can have slightly different settings while maintaining the same
base configuration.

- **<ins>Security</ins>**: Securely manage passwords, API keys, or other sensitive data within your dotfiles through encryption, integrating with password managers like [1Password](https://1password.com/) or by ignoring files.

- **<ins>Advanced Features</ins>**:

  - **Templates**: Templates make it easy to handle differences across machines without duplicating files.

  - **Scripts**: Automate post-apply tasks with scripts. For example, you might want to reload your shell configuration or restart services after applying new dotfiles.
  
  - **Encryption**: Secure sensitive files like SSH or GPG keys using encryption, ensuring they're safe even when your dotfile repository is public.
