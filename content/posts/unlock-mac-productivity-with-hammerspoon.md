+++
title = "Unlock Mac Productivity with Hammerspoon"
date = "2025-02-10"
[taxonomies]
  tags = ["hammerspoon", "lua", "macos", "dotfiles"]
+++

[Hammerspoon](https://www.hammerspoon.org/) provides a suite of core productivity tools I rely on in my daily workflow.

The Hammerspoon framework makes it easy to interact with the macOS. You can create custom tools and automations or use pre-built ones from the community, called [Spoons](https://www.hammerspoon.org/Spoons/). While Hammerspoon has an official Spoon repository, you can also find Spoons elsewhere online. If you're interested in making custom automations, Hammerspoon offers a powerful and well-documented [API](https://www.hammerspoon.org/docs/).

Below are some community-created Spoons that are essential to my day-to-day:

* [Caffeine](https://www.hammerspoon.org/Spoons/Caffeine.html) - Prevents your Mac from sleeping
* [ShiftIt](https://github.com/peterklijn/hammerspoon-shiftit) - Essential window management
* [SpoonInstall](https://www.hammerspoon.org/Spoons/SpoonInstall.html) - Highly recommended for managing your Spoons

Hammerspoon uses a config file at `~/.hammerspoon/init.lua`, which loads automatically when Hammerspooon starts.

Below is my `init.lua` file as of writing. For the latest version, check my [dotfiles](https://github.com/gavinest/dotfiles) on Github.

```lua
shortcut_keys = { "ctrl", "alt" }
hs.hotkey.bind(shortcut_keys, "r", function() hs.reload() end) -- reload config shortcut

-- Once installed SpoonInstall automatically installs the other spoons below
hs.loadSpoon("SpoonInstall")

spoon.SpoonInstall:andUse(
    "Caffeine",
    {
        start = true,
        hotkeys = {
            toggle = {shortcut_keys, "c"}
        },
    }
)

spoon.SpoonInstall.repos.ShiftIt = {
   url = "https://github.com/peterklijn/hammerspoon-shiftit",
   desc = "ShiftIt spoon repository",
   branch = "master",
}
spoon.SpoonInstall:andUse(
    "ShiftIt",
    {
        repo = "ShiftIt",
        hotkeys = {},
    }
)
```

I manage my Hammerspoon config with Chezmoi. As you may have guessed, I have a [post](@/posts/easy-dotfiles-with-chezmoi.md) on managing dotfiles with Chezmoi.

Stay tuned - I'm currently creating and open-sourcing my first Spoon for Hammerspoon!
