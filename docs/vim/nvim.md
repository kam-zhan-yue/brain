### Buffer Controls
- `tj` and `tk` to navigate
- `tq` to close

### Telescope
- `<leader>fg` to live grep
- `<C-p>` to search files

### Neotree
- `<C-n>` to toggle file tree
- `<Shift-H>` to show hidden files

### General
- `<Shift-K>` to show type hints

## Wishlist
- [x] Split Screen
- [x] Better tab management
- [x] Commenting
- [x] Seeing the current file name / class, etc
- [ ] Seeing git files

## Language Servers
In Nvim 0.11, there is native lsp support. In order to set something up, you need to:
- Download the language server yourself
- Setup a config
```
vim.lsp.config.clangd = {
  cmd = { 'clangd', '--background-index' },
  root_markers = { 'compile_commands.json', 'compile_flags.txt' },
  filetypes = { 'c', 'cpp' },
}
```
- Call enable
```
vim.lsp.enable('clangd')
```

Then, open a file and check
```
:checkhealth vim.lsp
```