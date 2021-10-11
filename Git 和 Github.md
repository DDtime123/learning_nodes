# Git 和 Github

#### **大纲：**

* 将自己的本地项目推向 master 分支
* 单独使用自己的原始项目和拉取请求
* 单独使用克隆项目
* 共同为项目做出贡献



#### 将本地项目推向 master 分支：

* 1.在 Github 上创建一个存储库：

  ![1633829487219](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633829487219.png)

  可以选择加入 README 文件和 gitignore 文件：

  ![1633829678368](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633829678368.png)

* 2.从 GitHub 获取 URL 用于克隆

  ![1633830134418](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633830134418.png)

  

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

    ![1633831466055](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633831466055.png)

  * 暂存所有更改，此时输入 git add -A

    暂存后使用 git status ，要提交的文件成了绿色

    ![1633834384709](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633834384709.png)

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



#### 单独拉取请求工作流

* 将功能分支推送到远程 存储库 git push origin my_feature_branch
* 在浏览器上导航到 Github 存储库，

