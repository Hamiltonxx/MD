# Install 
https://www.youtube.com/watch?v=E2mKJ73M9pg

# Tips
Space + th: change theme  
Ctrl + n: file tree  
Space + ff: finding files  
Space + h: built-in terminal emulator; ctrl+x,:q to exit.

# Mason
:Mason
:MasonInstall rust-analyzer

# line number
```lua
vim.cmd [[
  augroup numbertoggle
    autocmd!
    autocmd BufEnter,FocusGained,CursorMoved * if mode() == 'n' | set relativenumber number | endif
    autocmd FocusLost,ModeChanged * if mode() =~# '[v]' | set norelativenumber nonumber | endif
  augroup END
]]
```
