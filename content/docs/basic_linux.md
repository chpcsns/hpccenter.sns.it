---
title: Basic Linux Commands
---

This is just a super quick reference list. You need a minimum of proficiency with **Unix** shells (*bash* and *tcsh* are the ones mostly used on the cluster) and Linux systems.

| Command | Description |
|:--------|:------------|
| **passwd** | Changes your user password.<br>1. Type your old password<br>2. Insert new password<br>3. Confirm new password |
| **ls** | Lists folders and files in current directory |
| **mkdir** | Creates new directory into the current one |
| **cd** | Changes directory. <br>Examples:<br>`cd test` (go to a directory named 'test')<br>`cd ..` (go to parent directory)<br>`cd ~` (go to home directory; ~ is a shortcut for `/home/username`) |
| **rm** | Removes specified file or directory.<br>`rm filename` (remove single file)<br>`rm *.txt` (remove ALL .txt files into current directory)<br>`rm -r dirname` (remove directory and all the files it contains)<br><br>**Please be careful when you use the -f option!** |
| **rmdir** | Removes specified EMPTY directory |
| **pwd** | Prints current absolute path |
| **man** | Shows specified command's manual page (`man ls` to see help for `ls`) |
| **vi** | A *text editor*. If the file does not exist, `vi` creates a new file and opens it; otherwise it just opens the existing file. |
| **less** | A *text pager*. Opens file in read-only mode. You can use up and down arrows to move across the text. It shares many commands with VI. |
| **chmod** | Changes POSIX permissions of a file or directory. Allows to protect files from unwanted access.<br><br>`r` : read permission<br>`w` : write permission<br>`x` : exec permission<br><br>Examples:<br>`chmod +x file.sh` (allows execution)<br>`chmod -w file.sh` (denies write) |
| **chown** | Changes owner of a file or directory |
| **top** | Shows current executing processes |
| **cat** | Prints the content of a file on screen |
| **grep** | Filters the content of a file by outputting those lines which contain the specified pattern.<br><br>Examples:<br>`grep pattern file.sh`<br>You can exploit piping to filter the output of another command with `grep`:<br>`cat file.sh \| grep home`<br>`cat file.sh \| grep "home page"` |

Further information on *Linux shell* can be found here:

- [Useful Linux Commands for Newbies - www.tecmint.com](http://www.tecmint.com/useful-linux-commands-for-newbies/)
- [Shell Redirection - Wikipedia](https://en.wikipedia.org/wiki/Redirection_%28computing%29)
- [20 Advanced Commands for Middle Level Linux Users - www.tecmint.com](http://www.tecmint.com/20-advanced-commands-for-middle-level-linux-users/)
