# Vim Utilities

Installation:

1. Copy all `.vim` files to `~/.vim/plugin`
2. Copy all `.txt` files to `~/.vim/doc`
3. Sometimes need to 

## C++ Source Navagator

* `Trinity`: https://github.com/wesleyche/Trinity
  * Depends on **Source Explorer**, **Taglist**.
  * Also depends on **NERD Tree**, but the source code contains NERD Tree.
  * Depends on `exuberant ctag`, which can be `apt install`ed.
  * To navagate a project, run `ctag *` on the project, then `vim` open any source files.
  * Provides the following functions:
    * `TrinityToggleSourceExplorer`
    * `TrinityToggleTagList`
    * `TrinityToggleNERDTree`
    * `TrinityToggleAll` which invokes the above three.