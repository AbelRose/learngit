Git
Git使用C语言开发的 分布式版本控制系统 不必联网 (SVN是集中式的).
布式版本控制系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库.(实际中 为了方便会设置一台专门用于修改的机器充当"中央服务器").
如何协作工作? 比方说你在自己电脑上改了文件A，你的同事也在他的电脑上改了文件A，这时，你们俩之间只需把各自的修改推送给对方，就可以互相看到对方的修改了.
相比集中式的版本库来说更加安全可靠(不怕"中央服务器"坏掉 也不怕别人把自己的代码搞坏)因为每个人的个人电脑都是一台"中央服务器".

安装完成后，还需要最后一步设置，在命令行输入：
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"

创建一个空的文件夹
$ mkdir learngit
$ cd learngit
$ pwd
/Users/michael/learngit

通过git init命令把这个目录变成Git可以管理的仓库：
$ git init
Initialized empty Git repository in /Users/michael/learngit/.git/
ls -ah 可以看到隐藏的文件夹

把文件添加到版本库 (注意版本控制是没法跟踪World文件的改动的)
建议将所有的编码都改称UTF-8的格式 注意在windows中，不能用记事本编辑文本文件 可以用Notepad++ 把默认编码设置成UTF-8 without BOM即可

编写一个readme.txt文件，内容如下：
Git is a version control system.
Git is free software.

把文件放到Git仓库需要几步?
答案是肯定的 只需要两步
第一步，用命令git add告诉Git，把文件添加到仓库：
$ git add readme.txt
第二步，用命令git commit告诉Git，把文件提交到仓库：
$ git commit -m "wrote a readme file"
[master (root-commit) eaadf4e] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.txt

为什么Git添加文件需要add，commit一共两步呢？因为commit可以一次提交很多文件，所以你可以多次add不同的文件

当修改readme.txt时 使用git status命令
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
git status命令可以让我们时刻掌握仓库当前的状态，上面的命令输出告诉我们，readme.txt被修改过了，但还没有准备提交的修改

如果忘记上次怎么修改的 用git diff这个命令看看
$ git diff readme.txt 
diff --git a/readme.txt b/readme.txt
index 46d49bf..9247db6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
 Git is free software.

知道了对readme.txt作了什么修改后，再把它提交到仓库就放心多了，提交修改和提交新文件是一样的两步
第一步是git add：
$ git add readme.txt
在第二步之前再次运行 git status看看当前仓库的状态 git status告诉我们，将要被提交的修改包括readme.txt，下一步，就可以放心地提交了：
第二部是git commit -m "add distributed"
$ git commit -m "add distributed"
[master e475afc] add distributed
 1 file changed, 1 insertion(+), 1 deletion(-)
之后再看一眼
git status
On branch master
nothing to commit, working tree clean
小结:如果git status告诉你有文件被修改过，用git diff可以查看修改内容。

再次修改readme.txt
Git is a distributed version control system.
Git is free software distributed under the GPL.

$ git add readme.txt
$ git commit -m "append GPL"
[master 1094adb] append GPL
 1 file changed, 1 insertion(+), 1 deletion(-)

每当你觉得文件修改到一定程度的时候，就可以“保存一个快照”，这个快照在Git中被称为commit。一旦你把文件改乱了，或者误删了文件，还可以从最近的一个commit恢复，然后继续工作，而不是把几个月的工作成果全部丢失。

在Git中，我们用git log命令查看
git log命令显示从最近到最远的提交日志，我们可以看到3次提交，最近的一次是append GPL，上一次是add distributed，最早的一次是wrote a readme file

可以试试加上--pretty=oneline参数 会显得很整洁方便
cb58c1e953c5399023ac02862a9a41c350c8e4cf (HEAD -> master) append GPL
2e43c006d7c698042bb26ac5c2a9ee5a23075960 add distributed
8eff5d3c1f44229976d36d1b5b63c653715fcee0 wrote a readme file

cb58c1e953... 这个是commit id(版本号) 和SVN不一样是递增 而是根据SHA1计算出来的一个数字(目的是为了防止) 用16进制表示

回退版本
首先，Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，上一个版本就是HEAD^，上上一个版本就是HEAD^^ 当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100
用git reset命令
$ git reset --hard HEAD^
HEAD is now at e475afc add distributed
$ git log --pretty=oneline 
2e43c006d7c698042bb26ac5c2a9ee5a23075960 (HEAD -> master) add distributed
8eff5d3c1f44229976d36d1b5b63c653715fcee0 wrote a readme file

找到那个append GPL的commit id是8eff5d3c1f4422....，于是就可以指定回到未来的某个版本：
$ git reset --hard 1094a
HEAD is now at 83b0afe append GPL(版本号没必要写全，前几位(一般是7位)就可以了，Git会自动去找)
Git的版本回退速度非常快，因为Git在内部有个指向当前版本的HEAD指针 然后顺便把工作区的文件更新了。所以你让HEAD指向哪个版本号，你就把当前版本定位在哪
找不到新版本的commit id怎么办？
Git提供了一个命令git reflog用来记录你的每一次命令：
$ git reflog 回到未来!
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
小结:
HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。
穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。

工作区和 暂存区(和SVN的不同之处)
工作区(Working Directory)就是你在电脑里能看到的目录 比如learngit文件夹就是一个工作区
隐藏目录.git 是Git的版本库Repository(不算是工作区) Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。

前面讲了我们把文件往Git版本库里添加的时候，是分两步执行的：
第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区；
第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。

可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

Git跟踪并管理的是修改(增删改甚至创建新文件)，而非文件
Git管理的是修改，当你用git add命令后，在工作区的第一次修改被放入暂存区，准备提交
但是，在工作区的第二次修改并没有放入暂存区
所以，git commit只负责把暂存区的修改提交了
也就是第一次的修改被提交了，第二次的修改不会被提交。

用git diff HEAD -- readme.txt命令可以查看工作区和版本库里面最新版本的区别
小结:
每次修改，如果不用git add到暂存区，那就不会加入到commit中

git checkout -- file可以丢弃工作区的修改(在工作区)
git checkout -- readme.txt
总之，就是让这个文件回到最近一次git commit或git add时的状态。
git checkout -- file命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到git checkout命令

如果此时提交了 使用了git add 到暂存区了(此时没有commit)
用命令git reset HEAD <file>可以把暂存区的修改撤销掉（unstage），重新放回工作区：
然后再次用git checkout -- readme.txt

如果提交到了版本中 那么用版本回退的方法 $ git reset --hard HEAD^ 进行回退到上一个版本(前提是没有将自己的版本库退送到远程)如果真的集教导远程版本库那就...

删除操作
在Git中，删除也是一个修改操作
用rm 把文件删除后状态 deleted: test.txt
确实要从版本库中删除该文件，那就用命令git rm test.txt删掉，并且git commit -m "remove tesst.txt"
另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：
$ git checkout -- test.txt
git checkout其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。
注意：从来没有被添加到版本库就被删除的文件，是无法恢复的！ 

远程仓库
Git是分布式版本控制系统，同一个Git仓库，可以分布到不同的机器上
怎么分布呢？最早，肯定只有一台机器有一个原始版本库，此后，别的机器可以“克隆”这个原始版本库，而且每台机器的版本库其实都是一样的，并没有主次之分。
实际情况往往是这样，找一台电脑充当服务器的角色，每天24小时开机，其他每个人都从这个“服务器”仓库克隆一份到自己的电脑上，并且各自把各自的提交推送到服务器仓库里，也从服务器仓库中拉取别人的提交
由于你的本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以，需要一点设置：
1.创建SSH Key 
$ ssh-keygen -t rsa -C "youremail@example.com"
在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。
第2步：登陆GitHub，打开“Account settings”，“SSH Keys”页面：
点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容
只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。
“有了远程仓库，妈妈再也不用担心我的硬盘了。”——Git点读机

现在的情景是，你已经在本地创建了一个Git仓库后，又想在GitHub创建一个Git仓库，并且让这两个仓库进行远程同步，这样，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作，真是一举多得
现在，我们根据GitHub的提示，在本地的learngit仓库下运行命令：
$ git remote add origin git@github.com:michaelliao/learngit.git
添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库
下一步，就可以把本地库的所有内容推送到远程库上
$ git push -u origin master
把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。
由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令

推送成功后，可以立刻在GitHub页面中看到远程库的内容已经和本地一模一样

从现在起，只要本地作了提交，就可以通过命令
$ git push origin master
把本地master分支的最新修改推送至GitHub，现在，你就拥有了真正的分布式版本库！

小结:
要关联一个远程库，使用命令git remote add origin git@server-name:path/repo-name.git
关联后，使用命令git push -u origin master第一次推送master分支的所有内容
此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改
分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！当有网络的时候，再把本地提交推送一下就完成了同步，真是太方便了！

从远程仓库克隆
假设我们从零开发，那么最好的方式是先创建远程库，然后，从远程库克隆。
现在，远程库已经准备好了，下一步是用命令git clone克隆一个本地库：
$ git clone git@github.com:michaelliao/gitskills.git
如果有多个人协作开发，那么每个人各自从远程克隆一份就可以了

分支管理(平行宇宙)
现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。
创建与合并分支
截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即master分支。HEAD严格来说不是指向提交，而是指向master，master才是指向提交的，所以，HEAD指向的就是当前分支
一开始的时候，master分支是一条线，Git用master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点
当我们创建新的分支，例如dev时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上，Git创建一个分支很快，因为除了增加一个dev指针，改改HEAD的指向，工作区的文件都没有任何变化！
不过，从现在开始，对工作区的修改和提交就是针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变
假如我们在dev上的工作完成了，就可以把dev合并到master上。Git怎么合并呢？最简单的方法，就是直接把master指向dev的当前提交，就完成了合并
合并完分支后，甚至可以删除dev分支。删除dev分支就是把dev指针给删掉，删掉后，我们就剩下了一条master分支
首先，我们创建dev分支，然后切换到dev分支

$ git checkout -b dev
Switched to a new branch 'dev'
-b参数表示创建并切换，相当于以下两条命令：
$ git branch dev
$ git checkout dev
Switched to branch 'dev'

用git branch命令查看当前分支
* dev 当前分支
  master
现在就已经在branch分支上了

dev分支的工作完成，我们就可以切换回master分支
git checkout master 
Switched to branch 'master'
Your branch is up to date with 'origin/master'.
切换回master分支后，再查看一个readme.txt文件，刚才添加的内容不见了！因为那个提交是在dev分支上，而master分支此刻的提交点并没有变 我们还要把dev分支的工作成果合并到master分支上：
git merge dev
Updating 59431aa..2174c01
Fast-forward
 README.md | 3 ++-
 1 file changed, 2 insertions(+), 1 deletion(-)

git merge命令用于合并指定分支到当前分支。合并后，再查看readme.txt的内容，就可以看到，和dev分支的最新提交是完全一样的。
注意到上面的Fast-forward信息，Git告诉我们，这次合并是“快进模式”，也就是直接把master指向dev的当前提交，所以合并速度非常快。
合并完成后，就可以放心地删除dev分支了
git branch -d dev 
Deleted branch dev (was 2174c01).
git branch 就只剩下master分支了

实际上，切换分支这个动作，用switch更科学。因此，最新版本的Git提供了新的git switch命令来切换分支：
创建并切换到新的dev分支，可以使用：
$ git switch -c dev
直接切换到已有的master分支，可以使用：
$ git switch master

创建+切换分支：git checkout -b <name>或者git switch -c <name>
解决冲突
一个新的分支featurel 和 和master分支 分别对文件进行修改 并且在都执行了git add git commit 命令后
Git无法执行 快速合并
git merge featurel 
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容

小结
当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。
解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。
用git log --graph命令可以看到分支合并图。

分支管理策略
如果使用fast forward 模式 删除分支后会丢失原来的分支信息
如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。
git switch -c dev 然后add commit
切换到master
git switch master
只能合并dev分支请注意 --no-ff参数表示禁用Fast forward
git merge --no-ff -m "merge with no-ff" dev
Merge made by the 'recursive' strategy.
 README.md | 1 +
 1 file changed, 1 insertion(+)
小结
在实际开发中，我们应该按照几个基本原则进行分支管理：
首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；
你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。
合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。

Bug分支
Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：
首先确定要在哪个分支上修复bug，假定需要在master分支上修复，就从master创建临时分支
刚才的工作现场存到哪去了？用git stash list命令看看：
$ git stash list
stash@{0}: WIP on dev: f52c633 add merge
工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：
一是用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除；
另一种方式是用git stash pop，恢复的同时把stash内容也删了：
Git专门提供了一个cherry-pick命令，让我们能复制一个特定的提交到当前分支
git cherry-pick，我们就不需要在dev分支上手动再把修bug的过程重复一遍。
小结
修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
当手头工作没有完成时，先把工作现场git stash一下，然后去修复bug，修复后，再git stash pop，回到工作现场；
在master分支上修复的bug，想要合并到当前dev分支，可以用git cherry-pick <commit>命令，把bug提交的修改“复制”到当前分支，避免重复劳动。

Feature分支
在开发的时候可能会出现各种实验性质的代码 在feature上开发试验合并后就删掉了
$ git switch -c feature-vulcan
Switched to a new branch 'feature-vulcan'
小结
开发一个新feature，最好新建一个分支；
如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>强行删除。

多人协作
当你从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了
并且，远程仓库的默认名称是origin
$ git remote (git remote -v显示更详细的信息)
origin

git remote -v
origin  git@github.com:michaelliao/learngit.git (fetch)
origin  git@github.com:michaelliao/learngit.git (push)
上面显示了可以抓取和推送的origin的地址。

推送分支 就是把该分支上的所有!本地提交!推送到远程库。
推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：
$ git push origin master
如果要推送其他分支，比如dev，就改成：
$ git push origin dev

其他人抓取分支 git clone git@github.com:micabelrose/learngit.git(需要配置SSH Key到Github上)默认是master分支
若其他人要在dev上开发 需创建远程origin的dev分支到本地 git checkout -b dev origin/dev
如果我在本地的dev上作出修改 再提交会发生冲突 可以先用git pull 把最新的提交从origin/dev上抓下来 然后在本地合并，解决冲突后在推
git pull也失败了，原因是没有指定本地dev分支与远程origin/dev分支的链接，根据提示，设置dev和origin/dev的链接：
$ git branch --set-upstream-to=origin/dev dev
Branch 'dev' set up to track remote branch 'dev' from 'origin'. 
再git pull 解决冲突后 git commit -m "fix env conflit" push 
  git push origin dev
小结
多人协作的工作模式通常是这样：
    首先，可以试图用git push origin <branch-name>推送自己的修改；
    如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
    如果合并有冲突，则解决冲突，并在本地提交；
    没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！
在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致
如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>。
这就是多人协作的工作模式，一旦熟悉了，就非常简单。

Rebass
Git的提交历史能不能是一条干净的直线? 答案是肯定的
Git有一种称为rebase的操作，有人把它翻译成“变基”。
小结
rebase操作可以把本地未push的分叉提交历史整理成直线；
rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

标签管理
发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。
Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。 通常来说commit(12b3bhj)号是一些杂七杂八的数字和字母 如果把容易理解的tag和commit结合在一起那么仅需指明tag(V1.1)就能知道上次得到的commit

创建标签
git tag v1.0 可用git tag 查看所有标签 默认的tag是打在最新的commit上的
如果之前应该打的tag却没有打 可以找到之前的commit 然后打上就行了 git tag v0.9 f52c633 
PS:标签不是按照时间顺序排序的 而是按照字母顺序的 可以用git show <tag> 查看标签信息
还可以创建带有说明的标签，用-a指定标签名，-m指定说明文字：$ git tag -a v0.1 -m "version 0.1 released"1094adb
注意：git tag可以查看所有标签 标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。 
操作标签
如果打错了标签是可以删除的 git tag -d v0.1 (打的标签都在本地的 所以可以安全删除)
如果要推送某个标签到远程，使用命令git push origin <tagname>或者，一次性推送全部尚未推送到远程的本地标签： git push origin --tags
如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：
$ git tag -d v0.9
然后，从远程删除。删除命令也是push，但是格式如下：
$ git push origin :refs/tags/v0.9

Github
git clone git@github.com:abelrose/bootstrap.git
一定要从自己的账号下clone仓库，这样你才能推送修改。如果从bootstrap的作者的仓库地址git@github.com:twbs/bootstrap.git克隆，因为没有权限，你将不能推送修改。
如果你想修复bootstrap的一个bug，或者新增一个功能，立刻就可以开始干活，干完后，往自己的仓库推送。
如果你希望bootstrap的官方库能接受你的修改，你就可以在GitHub上发起一个pull request。当然，对方是否接受你的pull request就不一定了。
小结
    在GitHub上，可以任意Fork开源仓库；
    自己拥有Fork后的仓库的读写权限；
    可以推送pull request给官方仓库来贡献代码。

Gitee
在本地库上使用命令git remote add把它和Gitee的远程库关联：
git remote add origin git@gitee.com:liaoxuefeng/learngit.git
之后，就可以正常地用git push和git pull推送了！
注意此时会发生报错 因为本地库已经关联到github上了 一种方法是删除github上的 git remote rm origin
另一种是 换名
仍然以learngit本地库为例，我们先删除已关联的名为origin的远程库：
git remote rm origin
然后，先关联GitHub的远程库：
git remote add github git@github.com:michaelliao/learngit.git
注意，远程库的名称叫github，不叫origin了。
接着，再关联Gitee的远程库：
git remote add gitee git@gitee.com:liaoxuefeng/learngit.git
同样注意，远程库的名称叫gitee，不叫origin。
现在，我们用git remote -v查看远程库信息，可以看到两个远程库

配置别名
$ git config --global alias.st status

