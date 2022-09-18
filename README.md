# Github Copilot in the Terminal (bash)
# copilot-in-the-terminal

So you got tired of AI just suggesting code edits, and now you want it to help
you run code, too. Silly human, you have come to the right place. This will
take five steps.

This is a hacky solution to allow for copilot suggestions to work in the
terminal by treating neovim as a thin shell-around-a-shell.

## Installing

_I use Astro Vim, so the code snippets below may need slightly different syntax
depending on your neovim setup!_

1. You will want to install and get Github Copilot working with neovim.
2. The configuration for accepting copilot's autocomplete looks a bit like this:

In `~/.config/nvim/init.lua`, set a desired keymap:
```
vim.api.nvim_set_keymap("!", "<C-]>", 'copilot#Accept("<CR>")', { silent = true, expr = true, noremap=true })
vim.api.nvim_set_var('copilot_assume_mapped', true)
```

3. You will want to install the following three plugins (special credit to Shougo, the real legend!!!):

In `~/.config/nvim/lua/core/plugins.lua`:
```
  ["Shougo/vimproc.vim"] = {},
  ["Shougo/vimshell.vim"] = {}, 
  ["Shougo/unite.vim"] = {},
```

Edit: or replace `Shougo/vimshell.vim` with [maxwell-bland/vimshell-copilot.vim](https://github.com/maxwell-bland/vimshell-copilot.vim) and skip step 5 below. (vimshell-copilot.vim also includes bash scripting ergonomic improvements.)

4. Build vimproc! 

```
cd ~/.local/share/nvim/site/pack/packer/start/vimproc.vim
make
```

5. Remove the key mapping conflicts with vimshell!

```
vim ~/.local/share/nvim/site/pack/packer/start/vimshell.vim/autoload/vimshell/mappings.vim
```

and comment out (or remap) the overloaded mappings on line 155, change the following

```
inoremap <expr> <SID>(bs-ctrl-])
      \ getline('.')[col('.') - 2] ==# "\<C-]>" ? "\<BS>" : ''
imap <buffer> <C-]>               <C-]><SID>(bs-ctrl-])
imap <buffer> <CR>                <C-]><Plug>(vimshell_enter)
```

to the following

```
" inoremap <expr> <SID>(bs-ctrl-])
"       \ getline('.')[col('.') - 2] ==# "\<C-]>" ? "\<BS>" : ''
" imap <buffer> <C-]>               <C-]><SID>(bs-ctrl-])
imap <buffer> <CR>                <Plug>(vimshell_enter)
```

## Running

Open up a new vim session and type `:VimShell`. Github copilot's autocomplete
will now work in your bash shell. Here's an example I came up with, where I 
typed in `echo a random` and `cat`, then autocompleted the rest.

```
vimshell% # echo a random number between 1 and 10
vimshell% cat /dev/urandom | od -N 1 -An -i | awk '{print $1 % 10 + 1}'
```

## Conclusion and a Warning

Alright, my 1337 buddy. As you know, Copilot will start learning from the
commands you have executed in the current session, and the output. Don't 
[leak](https://man.openbsd.org/arc4random.3#arc4random_uniform) anything 
too secret, haha. Roko's Basilisk thanks you and sees all.
