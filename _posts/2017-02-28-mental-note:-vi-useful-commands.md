It is time to increase the productivity with [Vi](https://en.wikipedia.org/wiki/Vi) text editor, and do not rely too much in the use of installed [vundles](https://github.com/jootse84/dotfiles/blob/master/.vimrc). Some commands I should **asap** start using on a daily basis:

![alt Vi Vim]({{ site.url }}/assets/images/vim_post.png)

### NORMAL_MODE | :!UNIX_command

You can run UNIX commands and see their output without leaving vi, useful when you need to run tests or create a folder in the current directory:

```
:!mkdir jose
```

### NORMAL_MODE | :map + key_map + :!UNIX_command

Even better, we can store on a key map those specific commands that we are going to use often during the session.

After executing the following in vi, we can run pytest anytime from vi by pressing the *space* tab and the letter *t* from keyboard. Note - always finish the unix command with the *cr*.

```
:map <space>t :!PYTHONPATH=. py.test -s -v<cr>
```

### NORMAL_MODE | crtl + p

Please don't use more [*:NERDTree* vundle](https://github.com/scrooloose/nerdtree) and find your files by name with *crtl + p*.

### NORMAL_MODE | d + s + BRACKET/SURROUND_TYPE

You can remove sorroundings really fast. From NORMAL mode, just type d + s + the sorround type. For instance, if we want to remove a surrounding brackets:

```
d s (
```

### NORMAL_MODE | shift + d

Delete from your current position until the end of the line.

### NORMAL_MODE | shift + i | INSERT_MODE | type/code + esc | NORMAL_MODE | move_to_another_line + . + move_to_another_line + . + ...

Use this sequence when you need to repeatly **write the same text at the beginning of multiple lines** in your code. Note that a magical thing happens with the dot command - it repeats a command that used a numbered register, it will use the next numbered register (see :help redo-register). 


