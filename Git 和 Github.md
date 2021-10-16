# Git 和 Github

**大纲：**

* 将自己的本地项目推向 master 分支
* git pull 删除的本地文件
* 使用 Idea 集成的 git 功能
* 问题
* 参考



#### 1. 将本地项目推向 master 分支：

* 1.在 Github 上创建一个存储库：

  ![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/1633829487219.png)

  可以选择加入 README 文件和 gitignore 文件：

  ![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/1633829678368.png)

* 2.从 GitHub 获取 URL 用于克隆

  ![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/1633830134418.png)

  

* 在本地打开终端，并且移动到希望作为存储库的本地目录

  在终端上运行：

  git clone git@github.com:DDtime123/Java-JDBC-MySQL.git

* 推送到主工作流

  * 当创建一个新的 repo 时，默认是从 master 分支开始的。

    当然，可以通过以下语句创建新分支：

    ~~~git
    git checkout -b my_feature_branch
    ~~~

  * 在 master 分支中的文件中创建一些代码

  * 使用 git status 命令来检查本地文件的更改状态。

    ![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/1633831466055.png)

  * 暂存所有更改，此时输入 git add -A

    暂存后使用 git status ，要提交的文件成了绿色

    ![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/1633834384709.png)

    或者不想将所有文件添加到 GitHub 存储库中，可以选择性的添加文件 git add my_filename。

    当然，可以通过在 repo 中创建 .gitignore 文件，往 .gitignore 中添加不想保存在 Github 仓库中的文件，从而让 git 忽略掉这些文件。

    以下是 java 项目的 .gitignore 文件示例：

    ~~~makefile
    # Compiled class file
    *.class
    
    # Log file
    *.log
    
    # BlueJ files
    *.ctxt
    
    # Mobile Tools for Java (J2ME)
    .mtj.tmp/
    
    # Package Files #
    *.jar
    *.war
    *.nar
    *.ear
    *.zip
    *.tar.gz
    *.rar
    
    # virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml
    hs_err_pid*
    ~~~

  * 提交更改 git commit -m "my commit message"

*  切换分支 git checkout my_branch

* 查看所有分支 git branch -v

* 使用本地功能分支合并到本地主分支中 git merge my_feature_branch

* 将更新的主分支推送到 GitHub git push origin master

* 删除旧的，本地的，非主分支 git branch -d my_feature_branch



##### 1.1 为本地仓库添加远程仓库的方法

~~~go
git init
git add -A
git commit -m "my first commit"
git remote add origin <url_of_remote_repository>
git push origin master
~~~



#### 2. git pull 删除的本地文件

在我使用 Git 和 Github 的过程中，出现过由于远程仓库在远程（Github 网页上）作出了修改，导致远程仓库有本地仓库没有的更改，而本地仓库也作出了更改，导致本地仓库和远程仓库不能同步的情况：

git push 失败：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211013193819.png)

要想正常地进行 push ，有两种办法：

* 第一种，现将远程仓库 PULL 下来，这样会覆盖本地仓库中已经进行了 git add 操作的文件。

* 第二种，强行将本地仓库 PUSH 上去，使用 --force 标签将远程仓库覆盖。

两种方法都会覆盖一方的更改，所以会有撤销更改的需求：

首先是撤销更改：

* 先是使用 git pull 覆盖掉本地更改

* 然后使用 git reflog 查看在此 repo 中的操作历史记录：

  ![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211013200433.png)

* 请根据这些记录前的索引，使用 git reset --hard index 命令 让本地仓库复原之前的 commit 镜像：

  ~~~git
  git reset --hard 87c57fd
  ~~~

  如上命令可让本地仓库恢复至执行 git pull 前的一次 commit 镜像。



#### 3. 使用 Idea 集成的 git 功能

首先 idea 提供了集成 git status 的功能，通过快捷键 Alt + 0 呼出 commit 面板，可以看到当前进行了 git add，已做了更改但还没有进行 git commit 操作的文件：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014090024.png)

接下来查看 git 菜单，这里提供了 git 命令选项：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014090812.png)

编写 commit 信息后，就可以提交 commit：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014091059.png)

同理，点击 push 即上传至远程目录。



当然，我这里的 idea 项目在之前就已经通过 git 命令行命令初始化了 git 项目，并且添加了远程仓库：

~~~git
git init
	... git add , git commit
git remote add origin <url_of_remote_repository>
git push origin master
~~~

如果不想要通过命令行添加远程仓库，可以通过 Git -> Manage Remotes... 添加或更改远程仓库：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014092726.png)

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014092942.png)

这里添加另一个远程仓库作为测试：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014093703.png)

添加以后可以在命令行里执行 git remote -v 命令查看当前项目的远程仓库，确认无误后再执行下一步：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014093753.png)

此时可以通过命令行命令 “git push 主机名 branch名” 推送到相应的仓库：

~~~git
git push origin master 
git push origin2 master
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014101018.png)

接下来在 idea 中推送到新的远仓库：

请点击 Git -> Push，

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014101238.png)

点击 origin 选择想要 push 上去的主机（远程仓库），

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014101129.png)

再选择或创建新的 branch，就可以 push 上去了：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014101413.png)



#### 4. 问题

Q：将修改后的项目推送到远程仓库，会覆盖掉旧版本吗？

A：将修改后的本地仓库推送到远程仓库的已有 branch ，将在远程仓库创建一个新的 commit 镜像，这点和在本地仓库 commit 一次的结果相似。

在这样的 commit 的过程中，与其说修改后的项目覆盖掉了旧版本的项目，不如说所有版本的版本根据时间轴创建了一条永久的存储链。这里的链是根据 Git 的存储方式来修饰的：在每次 commit 后，除了将当前镜像修改以外，还记录其相对于旧版本的更改，这样无论 commit 了多少次，都能根据这条存储链上记录的更改复原任意一次 commit 的镜像。

Github 比起本地 Git 能够更加方便的查看之前的 commit 镜像，打开 Github 仓库页，点击右上角的 commits，即可看到所有的镜像：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014234944.png)

点击想要查看的镜像，比如我想查看最初的 commit ，点击最初的 commit：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014235223.png)

这里会出现这个版本和前一个版本的比对，我们点击右上角的 Browser files，即可查看该版本的项目文件：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014235357.png)

返回旧版本项目：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014235654.png)

如果想要在本地仓库恢复旧版本项目，请将项目 clone 或者  pull 下来，使用之前所说的 “git reflog” 查看 commit 的索引，然后使用 “git reset --hard 索引” 的方式恢复：

~~~git
git reflog 
git reset --hard 索引
~~~

#### 5. 参考

[1] [如何给 Git repo 添加两个远程仓库](https://articles.assembla.com/en/articles/1136998-how-to-add-a-new-remote-to-your-git-repo)

[2] [设置 Git 存储库](https://www.jetbrains.com/help/idea/set-up-a-git-repository.html#check_project_status)

