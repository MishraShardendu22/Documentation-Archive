# VS Code to Neovim Transition Roadmap
## Phase 1: Vim in VS Code
- Install extension: `VSCodeVim`
- Disable conflicting keybindings:
  - Open settings: `Ctrl+,` → Search `vim`
  - Tweak `vim.useCtrlKeys`, `vim.handleKeys`, etc.
- Use insert/normal mode regularly (disable arrow keys if needed)
- Get used to:
  - Motions: `w`, `b`, `e`, `gg`, `G`
  - Editing: `d`, `c`, `y`, `p`, `x`, `r`, `u`, `Ctrl+r`
  - Visual mode: `v`, `V`, `Ctrl+v`
  - Search: `/`, `?`, `n`, `N`
  - Buffers: `:bn`, `:bp`, `:bd`
- Optional: Add `.vimrc` emulation using `"vim.vimrc.enable": true`
**Goal:** Muscle memory + modal editing fluency
---
## Phase 2: Switch to Neovim
- Install Neovim (`nvim`) on your system
- Setup bare config:
  - `~/.config/nvim/init.lua` or `init.vim`
- Start minimal, just set:
  ```vim
  set number
  set relativenumber
  set clipboard=unnamedplus
  set tabstop=4 shiftwidth=4 expandtab
  syntax on
  ```
- Begin plugin setup via lazy.nvim or packer.nvim (only when ready)
---
Confirm when ready to proceed to plugin bootstrapping or full Neovim config.
