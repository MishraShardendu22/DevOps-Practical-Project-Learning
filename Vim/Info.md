# Vim Quick Reference - This File is practically useless this is no way to learn vim other than using, it is ai generated dont waste time on it. I am commiting it as a sign on progress.

## Exiting Vim

- `q` - Quit (only works if no changes)
- `q!` - Quit without saving changes
- `wq` - Write (save) and quit
- `x` - Save and quit (shorthand for wq)

## Entering Insert Mode

- `i` - Insert before cursor
- `a` - Insert after cursor (append)
- `o` - Insert on new line below
- `I` - Insert at beginning of line
- `A` - Insert at end of line
- `O` - Insert on new line above

## Navigation

- `h/j/k/l` - Left/Down/Up/Right
- `w` - Jump to next word
- `b` - Jump to previous word
- `e` - Jump to end of word
- `0` - Jump to beginning of line
- `$` - Jump to end of line
- `gg` - Go to beginning of file
- `G` - Go to end of file
- `123G` or `:123` - Go to line number 123

## Editing

- `u` - Undo
- `Ctrl + r` - Redo
- `x` - Delete character under cursor
- `.` - Repeat last command

## Visual Mode

- `v` - Enter visual mode (character selection)
- `V` - Enter visual line mode
- `Ctrl + v` - Enter visual block mode

## Copy, Cut, and Paste (Visual Mode)

- `d` - Delete/cut selected text
- `dd` - Delete/cut entire line
- `y` - Yank (copy) selected text
- `yy` - Yank (copy) entire line
- `p` - Paste after cursor
- `P` - Paste before cursor

## Search

- `/pattern` - Search forward for pattern
- `?pattern` - Search backward for pattern
- `n` - Jump to next match
- `N` - Jump to previous match

## Configuration (.vimrc)

Add these to `~/.vimrc` for permanent settings:

```vim
set mouse=a          " Enable mouse support
set number           " Show line numbers
set relativenumber   " Show relative line numbers
set tabstop=4        " Tab width = 4 spaces
set shiftwidth=4     " Indentation width = 4 spaces
set autoindent       " Auto-indent new lines
set expandtab        " Convert tabs to spaces
syntax on            " Enable syntax highlighting
```
