It is time to increase the productivity with [Vi](https://en.wikipedia.org/wiki/Vi) text editor, and do not rely too much in the use of installed [vundles](https://github.com/jootse84/dotfiles/blob/master/.vimrc). Some commands I should **asap** start using on a daily basis:

![alt Vi Vim]({{ site.url }}/assets/images/vim_post.png)

### [NORMAL_MODE] :!UNIX_command

You can run UNIX commands and see their output without leaving vi, useful when you need to run tests or create a folder in the current directory:

```
:!mkdir jose
```

### [NORMAL_MODE] :map + key_map + :!UNIX_command

Even better, we can store on a key map those specific commands that we are going to use often during the session.

After executing the following in vi, we can run pytest anytime from vi by pressing the *space* tab and the letter *t* from keyboard. Note - always finish the unix command with the *cr*.

```
:map <space>t :!PYTHONPATH=. py.test -s -v<cr>
```

### [NORMAL_MODE] crtl + p

Please don't use more [*:NERDTree* vundle](https://github.com/scrooloose/nerdtree) and find your files by name with *crtl + p*.

### [NORMAL_MODE] d + s + BRACKET/SURROUND_TYPE

You can remove sorroundings really fast. From NORMAL mode, just type d + s + the sorround type. For instance, if we want to remove a surrounding brackets:

```
d s (
```

### [NORMAL_MODE] shift + d

Delete from your current position until the end of the line.

### [NORMAL_MODE] shift + i [INSERT_MODE] type/code + esc [NORMAL_MODE] move_to_another_line + . + move_to_another_line + . + ...

Use this sequence when you need to repeatly **write the same text at the beginning of multiple lines** in your code. Note that a magical thing happens with the dot command - it repeats a command that used a numbered register, it will use the next numbered register (see :help redo-register). 

### [NORMAL_MODE] SELECT MULTIPLE LINES

For example, if I'm on line 5 and I want to select to line 35, I might press *ma* to place mark *a* on line 5, then *:35* to move to line 35. Shift + v (or Crtl + v to select just columns) to enter linewise visual mode, and finally *`a* to select back to mark a.

### [NORMAL_MODE] COPY UNTIL THE START/END OF THE LINE

The normal-mode command to move to the end of the line is with the $ symbol. You can copy to the start/end of the line with 0$ or y$ and paste with p. To copy/paste between different instances, you can use the system clipboard by selecting the * register, so the commands become "\*y$ for copying and "\*p for pasting.

Check :h registers for more information.

### [NORMAL_MODE] JUMP BETWEEN WINDOWS

Ctrl + W. Check out :help window-moving for more information.

### MAP :W to :w

A very annoying thing about vim is that frequently you type :W instead of :w. You can remap those commands in your .vimrc:

```
cmap W w
cmap Q q
```

### [NORMAL_MODE] COMMENT MULTIPLE LINES

We can comment at the same time a number of lines:

Ctrl + v → jj...jj → I → // or # or % ... → ESC

### [INSERT_MODE] GET SUGGESTIONS

Use Ctrl + p if you want to get a list of suggestions while typing.

