# git创建分支.md

1. git clone xxx (xxx为刚刚复制的仓库链接）

（1）新建分支

        git branch xxx (xxx填写你的分支名称)

（2）查看所有分支

        git branch -a

（3）切换到某一分支

        git checkout xxx (xxx填写要切换的分支名称）

（4）添加修改代码到缓存（注意最后的"."前面有个空格

        git add .

（5）添加提交代码的备注

        git commit -m "xxx" （xxx为本次提交代码的备注）

（6）提交代码到指定分支

        git push origin xxx （xxx为要提交代码的分支名称）