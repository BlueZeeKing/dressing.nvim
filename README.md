# Dressing.nvim

With the release of Neovim 0.6 we were given the start of extensible core UI
hooks ([vim.ui.select](https://github.com/neovim/neovim/pull/15771) and
[vim.ui.input](https://github.com/neovim/neovim/pull/15959)). They exist to
allow plugin authors to override them with improvements upon the default
behavior, so that's exactly what we're going to do.

It is a goal to match and not extend the core Neovim API. All options that core
respects will be respected, and we will not accept any custom parameters or
options in the functions. Customization will be done entirely using a separate
[configuration](#configuration) method.

* [Requirements](#requirements)
* [Screenshots](#screenshots)
* [Installation](#installation)
* [Configuration](#configuration)
* [Advanced configuration](#advanced-configuration)
* [Similar projects](#similar-projects)

## Requirements

Neovim 0.5+

On versions prior to 0.6, this plugin will act as a polyfill for `vim.ui`

## Screenshots

`vim.input` replacement (handling a LSP rename)

![Screenshot from 2021-12-02 19-43-00](https://user-images.githubusercontent.com/506791/144541672-17db1045-6ab6-4422-a09e-b2257965817e.png)

`vim.select` (telescope)

![Screenshot from 2021-12-02 19-46-01](https://user-images.githubusercontent.com/506791/144541916-4fa60c50-cadc-4f0f-b3c1-6307310e6e99.png)

`vim.select` (fzf)

![Screenshot from 2021-12-02 19-46-54](https://user-images.githubusercontent.com/506791/144541986-6081b4f8-b3b2-418d-9265-b9dabec2c4c4.png)

`vim.select` (nui)

![Screenshot from 2021-12-02 19-47-56](https://user-images.githubusercontent.com/506791/144542071-1aa66f81-b07c-492e-9884-fdafed1006df.png)


`vim.select` (built-in)

![Screenshot from 2021-12-02 19-50-50](https://user-images.githubusercontent.com/506791/144542327-c7420501-c75d-40de-9fc8-70204ed16589.png)

## Installation
dressing.nvim supports all the usual plugin managers

<details>
  <summary>Packer</summary>

  ```lua
  require('packer').startup(function()
      use {'stevearc/dressing.nvim'}
  end)
  ```
</details>

<details>
  <summary>Paq</summary>

  ```lua
  require "paq" {
      {'stevearc/dressing.nvim'};
  }
  ```
</details>

<details>
  <summary>vim-plug</summary>

  ```vim
  Plug 'stevearc/dressing.nvim'
  ```
</details>

<details>
  <summary>dein</summary>

  ```vim
  call dein#add('stevearc/dressing.nvim')
  ```
</details>

<details>
  <summary>Pathogen</summary>

  ```sh
  git clone --depth=1 https://github.com/stevearc/dressing.nvim.git ~/.vim/bundle/
  ```
</details>

<details>
  <summary>Neovim native package</summary>

  ```sh
  git clone --depth=1 https://github.com/stevearc/dressing.nvim.git \
    "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/pack/dressing.nvim/start/dressing.nvim
  ```
</details>

## Configuration
If you're fine with the defaults, you're good to go after installation. If you
want to tweak, call this function:

```lua
require('dressing').setup({
  input = {
    -- Default prompt string
    default_prompt = "➤ ",

    -- These are passed to nvim_open_win
    anchor = "SW",
    relative = "cursor",
    row = 0,
    col = 0,
    border = "rounded",

    -- These can be integers or a float between 0 and 1 (e.g. 0.4 for 40%)
    prefer_width = 40,
    max_width = nil,
    min_width = 20,

    -- see :help dressing_get_config
    get_config = nil,
  },
  select = {
    -- Priority list of preferred vim.select implementations
    backend = { "telescope", "fzf", "builtin", "nui" },

    -- Options for telescope selector
    telescope = {
      -- can be 'dropdown', 'cursor', or 'ivy'
      theme = "dropdown",
    },

    -- Options for fzf selector
    fzf = {
      window = {
        width = 0.5,
        height = 0.4,
      },
    },

    -- Options for nui Menu
    nui = {
      position = "50%",
      size = nil,
      relative = "editor",
      border = {
        style = "rounded",
      },
      max_width = 80,
      max_height = 40,
    },

    -- Options for built-in selector
    builtin = {
      -- These are passed to nvim_open_win
      anchor = "NW",
      relative = "cursor",
      row = 0,
      col = 0,
      border = "rounded",

      -- Window options
      winblend = 10,

      -- These can be integers or a float between 0 and 1 (e.g. 0.4 for 40%)
      width = nil,
      max_width = 0.8,
      min_width = 40,
      height = nil,
      max_height = 0.9,
      min_height = 10,
    },

    -- see :help dressing_get_config
    get_config = nil,
  },
})
```

## Advanced configuration
For each of the `input` and `select` configs, there is an option
`get_config`. This can be a function that accepts the `opts` parameter that
is passed in to `vim.select` or `vim.input`. It must return either `nil` (to
no-op) or config values to use in place of the global config values for that
module.

For example, if you want to use a specific configuration for code actions:
```lua
require('dressing').setup({
  select = {
    get_config = function(opts)
      if opts.kind == 'codeaction' then
        return {
          backend = 'nui',
          nui = {
            relative = 'cursor',
            max_width = 40,
          }
        }
      end
    end
  }
})

```
## Similar projects
* [nvim-notify](https://github.com/rcarriga/nvim-notify) - doing pretty much the
    same thing but for `vim.notify`
* [nui.nvim](https://github.com/MunifTanjim/nui.nvim) - provides common UI
    components for plugin authors
