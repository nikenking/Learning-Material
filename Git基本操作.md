## Git基本操作

git基本结构：

1. 工作区：我们写代码的地方
2. 暂存区：我们临时储存的地方
3. 版本库：存放已经提交的数据

git进入创建的worksapce，创建我们的仓库:crawler

1. bash基本命令

   ```python
   ll //查看目录
   ll -al|less //查看包括隐藏目录，进入end后输入:q退出
   cd ~//回到最上层
   pwd//查看当前用户
   cat .gitconfig //打印查看文本
   vim xxx.txt //i插入,esc退回到命令栏,:wq退出保存    
   set nu//查看行号 set nonu取消行号    
   ```

2. 初始化仓库

   ```python
   git init
   ```

3. 设置用户名和email

   ```python
   git config --global user.name tom_glo//携带global参数
   git config --global user.email 15756217611@163.com//和登录无关，只用于识别
       
   ```

4. 工作暂存区状态

   ```java
   git status//第一行分支，第二行已提交，第三行可提交    
   //lf 被替换成 crlf风格
   git rm --cached xxx.txt//将他从暂存区移除恢复成未追踪状态    
   ```

5. 提交到版本区

   ```python
   git commit xxx.txt//等等
   //安装后默认使用vim编辑器，输入i直接开始编辑
   //在更改了曾经提交过的文件后,提交命令有变化
   git commit -m "what i want say" xxx.txt//不用进入vim编辑器写注释了    
   ```

6. 版本查看

   ```python
   git log//查看每次提交的日期时间，hashcode标志，等等信息
   git log --oneline//简介
   git reflog //查看回退到该版本需要移动多少步HEAD@{1}
   git reset --hard 78f9d3//跟随索引的一部分就能唯一确定一个版本了
   git reset --hard ~n/^//奇葩操作，按指针数量跳转
   //soft & mixed 自己查看    
   git diff//直接和当前暂存区比较，如果没有添加到暂存就不会对比到
   git diff 6657474 xxx.txt//和历史版本的双方对应的文件比较    
   git diff head//直接把两个版本的工作区全比较了
   ```

7. 分支

   ```python
   git branch -v//查看所有分支
   git branch hot_fix//直接创建一个叫hot_fix的分支
   git checkout hot_fix//切换到这个分支
   git merge hot_fix//合并分支,切换到master,将hot_fix下的覆盖到master中
   //合并冲突文件前记得提交commit
   //发生冲突后手动修改完成,保存到暂存区,再次提交--不用携带文件名
   ```

8. 

