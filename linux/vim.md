---
title: Vim
---

# Vim Commands

| **Commands**                | **Description**                          |
| --------------------------- | ---------------------------------------- |
| `:retab`                    | Transform all tabs into spaces.          |
| `q a <commands> @a`         | Record commands into register `a`. You can replace `a` by any lowercase letter. |
| `daw`                       | Delete the current "text object" under cursor. |
| `<C-a> <C-x>`               | Increase/decrease the number under cursor. If not under cursor, jump to the next number in the line then increase/decrease. |
| `gU{motion}` `gu{motion}`   | Change to upper/lower case               |
| `<C-w>` `<C-u>`             | Delete last word/to start in Insert mode. |
| `zz`                        | Scroll the screen and put the cursor at middle of screen. |
| `<C-r><C-p>0` `<C-r><C-p>1` | Paste the copy/cut register in Insert mode. |
| `<C-r>={expression}`        | Insert the evaluation of an expression in Insert mode. |
| `ga`                        | Check the hex code of the character under cursor. |
| `<C-v>u####`                | Insert unicode.                          |
| `<C-k>{char}{char}`         | Insert a combination of two characters.  |
| `gv`                        | Reselect the previous visual part.       |
| `vit`                       | Select content inside a pair of tag.     |

