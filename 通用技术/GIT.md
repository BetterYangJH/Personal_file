# 流程

*   本地master开发

    *   提交前：git diff --check(去除多余字符)

    *   提交：

        *   工作区->本地仓库
            *   提交git commit -am 'xxx'
            *   查看提交内容git log --no-merges
        *   本地仓库->远程仓库
            *   git push origin master(提交的时master指针指向的内容)
            *   git push origin xxx(提交指定指针指向的内容)
        *   分解成多次小块提交，方便追溯

    *   提交前如果有人改动了其他部分，需要本地合并后再更新

        *   git fetch origin

            两个指针：本地master，origin/master

        *   git merge origin/master

            剩一个指针：本地master

        *   git push origin master

*   本地其他分支开发

    *   自己分支合并到本地master

        git merge xxx

    *   再进入其他人分支合并到本地master

        git merge origin/master

    *   切回本地merge提交

        git checkout/push

# 操作

*   push 远程仓库名(一般为origin) 分支名(一般其他分支合到master，提交时为master)
*   remote add 远程仓库名

# Github

*   git clone克隆项目
*   checkout -b name origin/master切换分支并编辑
*   commit工作区到本地仓库
*   github - Fork按钮 - 创建自己可写仓库
*   git remote add myfork远程链接申请的仓库
*   push myfork name提交代码到远程仓库
*   github - pull request按钮

