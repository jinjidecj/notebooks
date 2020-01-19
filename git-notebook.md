git config --global user.name "your name"

git config --global user.email "e@mail.com"

git config -l 

git add .   #.表示当前目录

git commit -m "content"

git add xx.txt

git add xx.txt xx2.txt

git add foldname

git commit aa.txt -m ""

git diff aa.txt

git reflog --online #完整的日志

工作区撤销

git checkout -- hello.txt 可以撤销到最后一次git add 或者git commit 的状态，从暂存区中(若没有，则从分支中)恢复到工作区

暂存区撤销 

git reset HEAD hello.txt  从暂存区中删除

git checkout -- hello.txt  回退到上一个版本