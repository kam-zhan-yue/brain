### attempt to call field 'ft_to_lang' (a nil value) treesitter main

Occurs when there is some weird interaction between nvim-treesitter and telescope.

Fixed by updating nvim to latest
```
brew uninstall nvim
brew install nvim -HEAD
```

Probably because telescope is made for the latest version of `nvim`, so it's important to update