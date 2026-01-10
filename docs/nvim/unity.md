## LSP

There are two requirements to use Unity with an LSP.

1. [Mono](https://www.mono-project.com/docs/about-mono/)
Mono is an open source development platform based on the .NET framework that allows developers to build cross-platform applications with improved developer productivity. There are several components that make up Mono:
- C# Compiler - A regular feature complete compiler for C#
- Mono Runtime - A runtime that implements the ECMA Common Language Infrastructure and provides a Just-in-Time (JIT) compiler, an Ahead-of-Time compiler (AOT), a library loader, the garbage collector, a threading system, and interoperability functionality
- .NET Framework Class Library - A comprehensive set of classes to build applications on
- Mono Class Library - Classes for Gtk+, OpenGL, POSIX, etc

Install mono on macOS with

```shell
brew install mono
```

2. Roslyn LSP
Manually installing the language application server for Roslyn is a HUGE pain, so I chose to go with [roslyn.nvim](https://github.com/seblyng/roslyn.nvim). All I needed to do was add this code to my lsp.lua

```lua
vim.lsp.config("roslyn", {})
```

## Opening Nvim from Unity

Install this Unity Package
```
https://github.com/apyra/nvim-unity.git
```

Unity will auto detect the editor as "Neovim Code Editor" in `External Tools`
- Go to `Edit > Preference > External Tools`
- Select Neovim Code Editor
> You will need to install this UPM in every project

