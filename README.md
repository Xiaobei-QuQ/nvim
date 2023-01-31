<h1 align="center">
  <img
    src="https://raw.githubusercontent.com/catppuccin/catppuccin/main/assets/misc/transparent.png"
    height="30"
    width="0px"
  />
  Cosynvim
  <img
    src="https://raw.githubusercontent.com/catppuccin/catppuccin/main/assets/misc/transparent.png"
    height="30"
    width="0px"
  />
</h1>

<p align="center">
  <a href="https://github.com/yuchanns/nvim/stargazers">
    <img
      alt="Stargazers"
      src="https://img.shields.io/github/stars/yuchanns/nvim?style=for-the-badge&logo=starship&color=c678dd&logoColor=d9e0ee&labelColor=282a36"
    />
  </a>
  <a href="https://github.com/yuchanns/nvim/issues">
    <img
      alt="Issues"
      src="https://img.shields.io/github/issues/yuchanns/nvim?style=for-the-badge&logo=gitbook&color=f0c062&logoColor=d9e0ee&labelColor=282a36"
    />
  </a>
  <a href="https://github.com/yuchanns/nvim/contributors">
    <img
      alt="Contributors"
      src="https://img.shields.io/github/contributors/yuchanns/nvim?style=for-the-badge&logo=opensourceinitiative&color=abcf84&logoColor=d9e0ee&labelColor=282a36"
    />
  </a>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/25029451/215700389-100e396a-045e-4f42-8a40-ac9e2d1f105f.png"
  height = "40%"
  widht = "40%"
  />
</p>


## What is this

A highly customized nvim configuration which prviously generated from [glepnir/cosynvim](https://github.com/glepnir/cosynvim).

## Structure

```
├── init.lua  
├── lua
│   ├── core                       heart of cosynvim provide api
│   │   ├── init.lua
│   │   ├── keymap.lua             keymap api
│   │   ├── options.lua            vim options
│   │   └── pack.lua               hack packer
│   ├── keymap                     your keymap in here
│   │   ├── config.lua
│   │   └── init.lua
│   └── modules                    plugins module usage example
│       ├── completion
│       │   ├── config.lua
│       │   └── plugins.lua
│       ├── lang
│       │   ├── config.lua
│       │   └── plugins.lua
│       ├── tools
│       │   ├── config.lua
│       │   └── plugins.lua
│       └── ui
│           ├── config.lua
│           ├── eviline.lua
│           └── plugins.lua
├── snippets                       snippets 
│   ├── lua.json
│   └── packages.json
└── static                         dashboard logo
    └── neovim.cat

```

## Usage

### How to install plugins

Api is `require('core.pack').register_plugin`. so pass plugin as param into this function. usage like in `modules/your-folder-name/plugins.lua`

```lua
local plugin = require('core.pack').register_plugin
local conf = require('modules.ui.config')

plugin {'glepnir/zephyr-nvim', config = conf.zephyr}

plugin {'plugin github repo name'}
```

what is `config` . this is keyword of [packer.nvim](https://github.com/wbthomason/packer.nvim), you need check the doc of packer  to know
use packer. if plugin has many configs you can create other file in `modules/your-folder-name/config.lua` avoid making the plugins.lua file too long.

Recommend lazyload plugins. Check the usage in `modules` , it will improve your neovim start speed. `lazyload` is not magic, it just generate 
your config into some `autocmds`,you can check the `packer_compiled.lua` to check it. I don't like the default path config in packer it use `plugins` folder
So i set compiled file path to `~/.local/share/nvim/site/lua`, you can find compiled file in this path. Use `:h autocmd` to know more about.
When you edit the config and open neovim and it does not take effect. Please try `PackerCompile` to generate a new compile file with your new change
In my personal config i have a function that can auto compiled when i edit the lua file that in this path `~/.config/nvim`. But it will make
some noise so I didn't use it in cosynvim. when i have a new implement I will update it to cosynvim core.

```lua

-- modules/completion/plugins.lua
plugin {'neovim/nvim-lspconfig',
 -- used filetype to lazyload lsp
 -- config your language filetype in here
  ft = { 'lua','rust','c','cpp'},
  config = conf.nvim_lsp,
}

-- modules/tools/plugins.lua
plugin {'nvim-telescope/telescope.nvim',
  -- use command to lazyload.
  cmd = 'Telescope',
  config = conf.telescope,
  requires = {
    {'nvim-lua/popup.nvim', opt = true},
    {'nvim-lua/plenary.nvim',opt = true},
    {'nvim-telescope/telescope-fzy-native.nvim',opt = true},
  }
}
```

### How to config keymap

In cosynvim there has some apis can make it easy. all apis define in `core/keymap.lua`.

```lua
keymap.map -- function to generate keymap by vim.keymap.set
keymap.new_opts -- generate opts into vim.keymap.set
-- function type that work with keymap.new_opts
keymap.silent keymap.noremap keymap.expr keymap.nowait keymap.remap
keymap.cmd -- just return string with <Cmd> and <CR>
keymap.cu -- work like cmd but for visual map
```
use these apis to config your keymap in `keymap` folder. in this folder `keymap/init.lua` is necessary
but if your have many vim mode remap you can config them in `keymap/other-file.lua` in cosynvim is `config.lua`
just a example file. then config plugins keymap in `keymap/init.lua`.

the example of api usage

```lua
map {
  -- packer
  {'n','<Leader>pu',cmd('PackerUpdate'),opts(noremap,silent)},
  {'n','<Leader>pi',cmd('PackerInstall'),opts(noremap,silent)},
  {'n','<Leader>pc',cmd('PackerCompile'),opts(noremap,silent)},
}
```
`map` foreach every table and generate a new table that can pass to `vim.keymap.set`. `cmd('PackerUpdate')` just return a
string `<cmd>PackerUpdate<CR>` as rhs. lhs is `<leader>pu>`, `opts(noremap,silent)` generate options table`{noremap = true,silent = true }`.

for some vim mode remap. not need use `cmd` function. oh maybe you will be confused what is `<cmd>` check `:h <cmd>` you will get answer
```lua
  -- window jump
  {'n',"<C-h>",'<C-w>h',opts(noremap)},
```
also you can pass a table not include sub table to `map` like 
```lua
map {'n','key','rhs',opts(noremap,silent)}
```       
use `:h vim.keymap.set` to know more about.

## Tips

- Improve key repeat

```
mac os need restart
defaults write NSGlobalDomain KeyRepeat -int 1
defaults write NSGlobalDomain InitialKeyRepeat -int 10

linux
xset r rate 210 40
```

## Licenese MIT
