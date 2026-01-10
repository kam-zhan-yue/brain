## Opening nvim from Godot

On Godot's side, we need to set the external editor to nvim and we need to set the following exec flags:

```
--server ./godothost --remote-send "<C-\><C-N>:n {file}<CR>{line}G{col}|"
```

On Nvim's side, we need to do either

```shell
# opening nvim directly
nvim --listen ./godothost
```

``` lua
-- automatically listening if there is a project.godot file
local projectfile = vim.fn.getcwd() .. '/project.godot'
if projectfile then
  vim.fn.serverstart './godothost'
end
```

## LSP
Godot runs an LSP when it is launched. So we hook into that LSP through nvim.

```lua
---@brief
---
--- https://github.com/godotengine/godot
---
--- Language server for GDScript, used by Godot Engine.

local port = os.getenv 'GDScript_Port' or '6005'
local cmd = vim.lsp.rpc.connect('127.0.0.1', tonumber(port))

return {
  cmd = cmd,
  filetypes = { 'gd', 'gdscript', 'gdscript3' },
  root_markers = { 'project.godot', '.git' },
}
```
