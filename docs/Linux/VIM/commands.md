# Useful VIM Commands

## Switch to tabs instead of spaces

If you're working on someone else's code.. and they're monsters who use tabs.. then this command will switch your VIM to tab mode

```ag-0-1eupk20ujag-ag-1-1eupk20uj0-1eupk20ujvimag-1-1eupk20uj
:set tabstop=4      " To match the sample file
:set noexpandtab    " Use tabs, not spaces
:%retab!
```
