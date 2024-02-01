# Git 分布式版本控制工具

------

：无论学什么框架，最好熟悉官网

> Everything is local. 可以记录文件修改的历史记录，方便历史版本的查阅、切换，从而从个人开发过渡到团队协作。

### 1.分布式vs集中式

<img src="/Users/lby/Library/Application Support/typora-user-images/image-20230608210934065.png" alt="image-20230608210934065" style="zoom:40%;" />

> **集中式**
>
> 所有代码在一个单一集中管理的中央服务器，虽然便于管理，但如果出现center server的单点故障，则无法进行多人协作；
>
> **分布式**
>
> 每个工作的client同时也是server，同时有一个远程库做代码托管，每人工作前后要clone和push，保证远程库上托管的永远是最新版本；每个client都是把代码的最新版本完整的景象到本地（完整备份），所以虽然就算远程库故障不能push，但每个client都可以在本地短暂的进行版本控制；

### 2.git各部分模块分区

![image-20230612151723050](/Users/lby/Library/Application Support/typora-user-images/image-20230612151723050.png)

### 3.安装与使用

```bash
git --version // terminal 命令查询是否安装git及版本：已安装 git version 2.39.2(Apple Git-143)
```

> 在 macOS 上执行 git 命令非常方便，无需额外安装 git bash 工具，只要在terminal中运行相应的 git 命令即可；
>
> 使用 git 进行代码版本控制需要账号，但在本地使用 Git 的基本功能是不需要账号的；

### 4.命令（先学工作必备）

##### 1.设置用户签名

！注意：这里的用户签名和后面登录远程库的账号没有任何关系！！！只是为了区分不同的操作者身份🆔；

```bash
// 设置 user.name&email(并不会验证邮箱) 
lby@macbookdeMBP-2 ~ % git config --global user.name lby
lby@macbookdeMBP-2 ~ % git config --global user.email boyanli01@foxmail.com
// 验证设置成功
lby@macbookdeMBP-2 ~ % git config user.name
lby
lby@macbookdeMBP-2 ~ % git config user.email
boyanli01@foxmail.com
// （重复此步骤即可修改和再次验证）必须设置 不然提交会报错！
```

##### 2.初始化本地库

> 命令：git init
>
> 1. 选定自己建立的目录：/Users/lby/git
>
> 2. **在terminal先cd进入这个目录**；
>
> 3. 然后运行 git init 命令，在这个目录下生成.git文件，完成初始化；

##### 3.查看本地库状态

> 命令：git status
>
> 其中在执行命令的返回内容中：红色是未被git识别，但在该目录下的文件；

##### 4.添加到暂存区+多个

> 命令：git add 完整文件名称(全英文不能有空格)
>
> ​			git add . // 添加该目录下所有改动到暂存区

##### 5.从暂存区删除（本地文件不受影响）

> 命令：git rm --cached 完整文件名称

##### 6.提交到本地库（形成历史版本）+多个

> 命令：git commit -m "编写提交日志信息" 完整文件名称
>
> 修改文件：再次走一遍过程，生成历史版本；
>
> 命令：git commit -m "编写提交日志信息" // 一次性提交暂存区内所有数据，命令中不加文件名称

##### 7.查看历史版本信息

> 命令
>
> 1. git log：查看历史版本详细信息，版本号也是齐全的版本号，详细显示了作者和时间戳；
>
>    <img src="/Users/lby/Library/Application Support/typora-user-images/image-20230609172048594.png" alt="image-20230609172048594" style="zoom:100%;" />
>
> 2. git reflog：查看历史版本信息，相对简化的信息，版本号简短显示前几位；
>
> <img src="/Users/lby/Library/Application Support/typora-user-images/image-20230609172122835.png" alt="image-20230609172122835" style="zoom:70%;" />
>
> head->main指向的是当前版本！

##### 8.版本穿梭（切换版本）

> 命令：git reset --hard 版本号

##### 9.释放对本地仓库的git管理权

> 命令：rm -rf .git

将会删除本地仓库中的 `.git` 目录，从而将 Git 的版本控制功能从该目录中移除。执行该命令后，您在本地的仓库就不再是一个 Git 仓库，而只是一个普通的文件目录了。注意，这个命令是不可逆的，也就是说，一旦执行完成，您在该本地仓库中所保存的所有历史数据都将被永久性地删除。如果您想保留历史记录，但不再使用 Git 进行版本控制，可以将该本地仓库复制到其他位置，并且删除 `.git` 目录。

### 5.分支

> 在version control中，同时推进多个任务，为每个任务创建单独的分支，可以使程序员将自己的工作从开发主线上分离开：开发自己的分支时，不会影响主线的运行，就算某一个分支开发失败，直接删掉即可，不会影响主线的内容。同时可以进行多功能开发，提高开发效率。
>
> 初学者可以将branch当作开发副本；
>
> <img src="/Users/lby/Library/Application Support/typora-user-images/image-20230612115458470.png" alt="image-20230612115458470" style="zoom:50%;" />
>
> branch命令：创建、查看当前本地仓库所有分支情况、切换、合并
>
> <img src="/Users/lby/Library/Application Support/typora-user-images/image-20230609183244703.png" alt="image-20230609183244703" style="zoom:50%;" />
>
> ​											  git branch -d 分支名								删除制定分支（确认已经保存所有数据到main分支） 一般用不到

##### *.合并、产生与解决冲突

> 冲突产生：**CONFLICT **/ **MERGEING**
>
> <img src="/Users/lby/Library/Application Support/typora-user-images/image-20230612145341956.png" alt="image-20230612145341956" style="zoom:50%;" />
>
> 冲突产生的原因：
>
> 在合并分支时，合并的两个分支，在同一个文件的同一个位置，有两套完全不同的修改，git无法替我们决定使用哪一个，必须人为决定新代码的内容！
>
> <img src="/Users/lby/Library/Application Support/typora-user-images/image-20230612145143133.png" alt="image-20230612145143133" style="zoom:50%;" />
>
> **解决冲突**：
>
> 手动修改自动merging的结果，手动删掉<<<HEAD（当前分支）只保留想留下的内容，并再次提交main；
>
> 1. 把fix后的版本先git add添加到暂存区，命令：git add 文件全名
> 2. 把fix后的版本git commit添加到本地库，⚠️**注意这里不可以加文件名**！命令：git commit -m "说明"
>
> 合并到main分支的版本已解决冲突，hot-fix分支的版本还是提交merge前的内容没有变；

### 6.git团队协作机制（必须引入远程库）

##### 1.团队内协作

<img src="/Users/lby/Library/Application Support/typora-user-images/image-20230613143955965.png" alt="image-20230613143955965" style="zoom:40%;" />

##### 2.多团队间协作

<img src="/Users/lby/Library/Application Support/typora-user-images/image-20230613152928412.png" alt="image-20230613152928412" style="zoom:50%;" />





# GitHub Git官方代码托管中心

------

### 1.创建远程库

- 远程和本地库名相同（便于管理）
- 默认public就行，private也行，但超过团队人数限制收费（Gitee也是）

### 2.远程库基本操作

<img src="/Users/lby/Library/Application Support/typora-user-images/image-20230613174742942.png" alt="image-20230613174742942" style="zoom:50%;" />

> ⚠️
>
> - 远程库的本地**别名**：用来在git中代替很长的远程仓库地址，建议和本地库名（远程库名）保持一致；
>
>   ```bash
>   // 修改别名对应地址的命令：
>   git remote set-url origin new_url
>   // 修改别名名称的命令：
>   git remote rename origin new_name
>   ```
>
> - Push：最小单位是分支，eg. 把本地库的main分支推送到远程库地址；
> - Pull：将本地内容更新到最新版本；
> - Clone克隆（Down代码）：读权限不需要登录任何账号，自动做3件事：1.pull代码 2.初始化本地库 3.创建别名；

### 3.SSH免密登陆

##### 1.在本地计算机上生成 SSH 密钥

> 命令：ssh-keygen -t rsa -b 4096 -C "在github账户的邮箱📪"
>
> 1. “-t rsa”表示使用 RSA 算法生成密钥，“-b 4096”表示选择密钥长度为 4096 bits，“-C”参数指定用于标识密钥的注释信息。
> 2. 在生成过程中会提示输入文件名和密码，可以直接按 Enter 使用默认值。
>
> ```bash
> // result
> Your identification has been saved in /Users/lby/.ssh/id_rsa
> Your public key has been saved in /Users/lby/.ssh/id_rsa.pub
> The key fingerprint is:
> SHA256:JWIAIXaDqPhC2R5VfpjIkqXuuhE5OSqx8WyggM+JZjk 此处隐藏本人邮箱
> 
> // 在终端打开的被隐藏的放置public key的文件内容
> lby@macbookdeMBP-2 ~ % cat /Users/lby/.ssh/id_rsa.pub
> ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC1PVaFqgLSelVGuTCoOMEkI22iqSuvDHmtfW7GEJc6nefETTYl1cqIprw1XNPEMGB5gydeiQgjuOSaaDK92Vuu0szxDva8EjYoTwlRZTnLBRmCn6isnM0lvzbHFU899wJJb6uLJcfz2GJhsZJYbeHNVqbd0xFdzd/4PauaBKsNEELdLPhklPKCF3Fz1Nk7K2Uu6LmgVhWEzxgWWVlFoFU+3YDNZ2to4ChQ8sZo66cT4laWacNPpJ5mr85MhjF2vlUCyXNiJ7KRDwjYzsyOFP11VN7pTCJ3dUTFHCQ4Hvy1zwZd59aPCvjgXkqxPylEcgHCmg41VvMo2QI+3W1AmWJAwg3l+L62KmvwcYnD2s0RbIsWFA56gTWx/l1HD1MJpMhJ4idj/V+zQ/1P/I4aI7yZg6BCVeGGHpqmovotB2NsXDhGMVq9BfOIMZQHmI7t8+IIOeqYN4Quytp80t8GvERo6mahmXV/FNxF5LinU9R7nedfBYzqNQx9RTneF23cNTCkaSLoytwoo4qXCH4M9Vs9qMqpdmebvDGjE249pysc/Soqpo2Mp3wc6vePTwzwE+dfxQpI5LItTdsOuYdcRqp9P4DI5NTpxgPwoBuR19h2Atzr60rXU7VVjYG/1HzqRwztdy9Pq0F6MepdG+rxRQXeOiEqaUHJLKaHFS7eyER6VQ== 此处隐藏本人邮箱
> ```

##### 2.添加公钥到 Github 上

将生成的公钥（即文件名为 `id_rsa.pub`）中的内容复制到 Github 上。首先登录 Github 账户，然后打开“Settings”-“SSH and GPG keys”页面，点击“New SSH key”按钮，填写标题和公钥信息后点击“Add SSH key”按钮即可完成添加。

标题是用于标识该密钥的名称，可以任意填写，建议填写与该密钥相关的信息：例如密钥所在的本机计算机名称或密钥生成日期等等，以便于区分、管理和撤销。

##### 3.测试连接

```bash
// 终端测试命令
ssh -T git@github.com
// 连接成功
Hi boyancn! You've successfully authenticated, but GitHub does not provide shell access.
```

至此，您已经成功地使用 Github 的 SSH 协议进行了身份验证。

##### 4.使用 SSH 进行代码上传和下载

在使用 SSH 协议进行代码上传或下载时，需要修改 Git 的远程仓库地址。可以使用 `git remote set-url` 命令将 HTTPS 协议的地址修改为 SSH 协议的地址。

```
// 命令
git remote set-url origin git@github.com:username/repo.git
```

其中，`origin` 表示远程仓库的别名，“git@github.com”是 Github 的 SSH 地址，“username”和“repo”表示要访问的账户和仓库名称。

由于使用了 SSH 协议，无需再输入 Github 账户的用户名和密码，可以直接使用 `git push` 和 `git pull` 命令进行操作上传和下载。



# Gitee码云 国内版

------

推荐使用https协议，因为服务器就在国内（SSH设置和GitHub一模一样）

### *.Gitee连接GitHub进行代码复制、迁移、强制更新内容

> 在gitee中，创建新的仓库时，直接往下拉，可以通过网页链接**导入已有项目**！
>
> 在github项目中，找到**HTTPS**协议的链接！！！注意这里一定是**HTTPS**！**不是SSH**！
>
> 然后直接点击创建按钮；
>
> 点击红框中的刷新，可以强制更新GitHub上的最新内容！！！（太牛了）
>
> <img src="/Users/lby/Library/Application Support/typora-user-images/image-20230615183118562.png" alt="image-20230615183118562"/>



# IDEA插件：Git Github Gitee

------

在Intellij IDEA中集成使用：git github gitee 三合一（就是不想用gitlab！！！）

### git

**配置环境**

1.配置git忽略文件，项目内只保留pom.xml和代码文件在git内；

> 忽略与项目实际功能无关，不参与服务器上部署运行的文件，忽略之后能够屏蔽IDE工具之间的差异；

2.建立忽略规则文件：xxx.ignore（eg. git.ignore)；

<img src="/Users/lby/Library/Application Support/typora-user-images/image-20230615162530391.png" alt="image-20230615162530391" style="zoom:50%;" />

3.将git.ignore配置到.gitconfig的文件内；

<img src="/Users/lby/Library/Application Support/typora-user-images/image-20230615162613780.png" alt="image-20230615162613780" style="zoom:50%;" />

4.在idea的setting的git插件中，测试git路径正确；

**idea内创建本地库**

VCS（Version Control System）-> Import into Version Control -> Create Git Repository；

**配置好了之后，进一步的原理上面已经很清楚了，具体操作idea视图真的很智能，在这里不做赘述；**

### github

**添加账号，使用user token登录**

在github账号中找personal access tokens，用token登录（破网速帐密100遍不一定登录成功）token生成的时候权限全部打勾拉满；

**创建远程库，把本地项目上传至GitHub中**

> 正常情况下，需要先在github网页账号中创建新的远程库，但在idea中，可以直接将本地项目上传至远程库，存进一个新的repository；其中，Repository name是远程仓库名称，Remote是远程仓库的**别名**！两个名字一般起成一模一样的；
>
> ![image-20230615180033756](/Users/lby/Library/Application Support/typora-user-images/image-20230615180033756.png)

**Push**

> <img src="/Users/lby/Library/Application Support/typora-user-images/image-20230615181322946.png" alt="image-20230615181322946" style="zoom:50%;" />
>
> push时默认使用https协议，手动改成ssh协议的仓库地址，提交飞快！
>
> **github和gitee在push时都要手动设置远程库地址链接（别名！！！：命令行中设置过的就直接用就行！设置后在左下栏中可以编辑！）**

**Pull**

> 编程界团队协作的规定：Push之前先Pull，提交的改动都是基于最新的代码版本进行的，每次写之前及时更新；

**Clone**

> 在开始界面就可以-> Get from Version Control，从而从远程库down代码下来（因为现在本地没有目录）；
>
> 也是用SSH免密登陆down代码！！！只要在国内都优先用SSH；
>
> <img src="/Users/lby/Library/Application Support/typora-user-images/image-20230615181656066.png" alt="image-20230615181656066" style="zoom:50%;" />

### gitee

基本操作和github一模一样；



# *GitLab 基于局域网的托管器 公司版

------

托管在公司服务器上 不公开 没有打算把代码放到互联网上（暂时用不到不想学）

### 1.服务器搭建 部署

### 2.IDEA集成