---
layout: post
title: "shell note"
date: 2016-12-05
---

shell note

出现error 是因为文件转换的时候有多余的\r
/usr/bin/env: ‘sh\r’: No such file or directory

https://stackoverflow.com/questions/29045140/env-bash-r-no-such-file-or-directory

As a quick fix, you can remove the \r chars. as follows:
sed $'s/\r$//' ./install.sh > ./install.Unix.sh


open your terminal and enter the command ln -s ~/.ssh ~/ssh to create a symbolic link called 'ssh' which will appear in file lists.*

df -hT #check disk amount


