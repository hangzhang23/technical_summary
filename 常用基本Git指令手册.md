# 常用基本Git指令学习手记

本手册整理于如下Git练习网站，强推一下(https://learngitbranching.js.org/?demo=&locale=zh_CN)，用过关的形式来逐步练习，并用动画来呈现git中的关系，非常直观有趣。常用本地和远程指令讲解练习都有，几个小时就可以做完。

## 本地git操作

### 1. 建立分支，commit操作

1. ```git commit```：在当前分支下留下commit。

2. ```git branch <b-name>```：建立新分支。

3. ```git checkout <b-name>```：从当前分支切换新分支。

   2和3也可以联合起来用一个```git checkout -b <b-name>```实现，表示建立并直接切换至新分支。

### 2. 合并分支

1. ```git merge <b-name>```：合并分支到main（或者master）上（当前在main上）。

   如果需要吧main合并到分支b1上，则：

   ```
   git checkout b1
   git merge main
   ```

   先切换分支，然后把main合并过去。

2. ```git rebase <b-name>```: rebase的第一个作用是合并多个commit为一个commit，第二个作用是将一段commits粘贴到另一个分支上。假设现在在b1分支上，如果执行```git rebase main```则会把main当前HEAD的commits都复制到b1上，并且还保有main。

### 3. 分支内操作

1. HEAD为路径下指向的最近提交，在当前路径上可以用```git checkout <node name>```来移动HEAD的位置。
2. ```^数字```：表示在当前分支或HEAD处向上移动数字n个提交记录。```引用^```这时放在引用后面表示寻找引用的父节点，比如```main^```表示main的父节点，```main^^```表示main^的父节点。
3. ```~```:表示向上移动，```~n```表示向上移动n个点，n最小为1。
4. ```git branch -f main HEAD~3```：表示切换到main路径并指向HEAD的向上3级父提交。
5. 撤销变更```git reset HEAD~1```和```git revert HEAD```。reset直接倒退回上一次提交，这样会导致commit不可见，所以一般用在本地。revert一般用在远程，也是返回上一次提交，但是他是延伸出一个节点并表示上一次提交，是可见的撤销。

### 4. 自由修改提交树

1. ```git cherry-pick <n1,n2...>```：表示把节点n1，n2等等直接接到当前路径的HEAD下。
2. ```git rebase -i HEAD~3```：当想修改当前路径下多个commits时，可以在```rebase -i```来打开交互界面，通过omit来隐藏不需要的节点，通过拖动改变节点顺序，确认之后就可以按照交互操作生成一个新的分支。

### 5. 本地提交杂项

1. 快速提交，可以用```rebase -i```或者```cherry-pick```来选择需要提交的commit，补充一条```git rebase branch1 main```就相当于```git checkout main```加上```git rebase branch1```，也就是从branch1跳到main后再将branch1接到main候面。
2. 在一个分支上b1又建立了新分支b2，并且已经有提交，现在想返回老分支b1做修改。
   - ```git rebase -i HEAD~2```：调整顺序并建立新分支。
   - ```git commit --amend```：对HEAD节点加入修改。
   - ```git rebase -i HEAD~2```：在对当前分支把顺序调回来并生成新分支。
   - ```git rebase b1 main```：把main移动到b1最前端。
3. 上面的操作会导致排序太多，所以还可以用```cherry-pick```来操作：
   - ```git checkout main```：返回main。
   - ```git cherry-pick C2```：只把原本的C2点拿出来加到main下面。
   - ```git commit --amend```：在C2点上做修改。
   - ```git cherry-pick C3```：再把原本C2的子节点C3也拿过来接在C2上。
4. 可以用```git tag <tag-name> <n-name>```来给node添加tag，也可以用checkout找到node再直接tag。
5. ```git describe <ref>```的返回值是```<tag>_<numCommits>_g<hash>```，其作用是查询离当前最近tag差多少个提交记录，hash表示给定的ref的前几位。

### 6. 本地进阶

1. 多项rebase, 使用```git rebase b1 b2```来把b2接到b1上并且路径设为b2.
2. ```main^2```表示main的另一个父递交，此外^和~可以链式操作。



## 远程仓库git操作

### 1. 基本操作

1. ```git clone ```: 从远程仓库复制到本地，或者把本地很远程连接。一般复制完本地会有```o/main```的分支，这个是远程分支。远程分支的命名规范是```<remote name>/<branch name>```，所以这里的o是origin。

2. 远程分支在检出时，会进入分离HEAD状态。为的是不让main的操作直接留在o/main上。在本地上要注意做提交时所处的分支。

3. ```git fetch```：从远程仓库获取数据（通信，或者就是下载），获得本地仓库缺失的提交记录，并更新远程分支指针（如：o/main）。但是并不会改变本地仓库的状态，也不会更新main分支，也不会修改磁盘上的文件。

   通常通过互联网（```http://```或者```git://```与远程仓库通信）。

4. ```git pull```：先抓取更新在进行合并。其作用和```git  fetch```加上```git merge```是一样的效果。

5. ```git push```：将我的变更上传到指定远程仓库，并在远程仓库上合并我的新提交记录，也就是说在push之后小组其他成员就可以从远程仓库上下载我的推送成果了。

   push不带参数时的行为与```git default```配置有关。默认值取决于正在使用的git版本。

6. 提交偏离得问题会导致在push得时候跟不上远程仓库的版本，git会强制开发者先合并最新的代码，然后才能push。

   - 可以先fetch远程仓库，然后rebase到本地的o/main上，把个人工作移动到最新提交记录下，然后再用push（简写指令为```git pull --rebase```）。
   - fetch-merge-push的顺序也可以，这种与rebase的不同在于，将本地的提交与o/main无抹除合并（以分支的形式），然后提交到远程仓库时也是分支得形式(简写```git pull```)。

7. 但是一般都不允许直接在master上进行push，需要配置pull requests来提交更新。

   具体操作：

   - ```git reset --hard o/main```把o/main与本地的main重置到一起。
   - 然后```git checkout -b feature C2```建立新的分支节点。
   - 最后```git push origin feature```到远程仓库。

### 2. 高级操作

1. 推送主分支：在个人本地仓库分之多的情况下，需要把所有分支做集成，然后统一推送。

   - ```git fetch```：抓取更新
   - ```git rebase o/main side1```：合并并定位到side1
   - ```git rebase side1 main1```：合并并定位到main
   - ```git push```：将集成完毕的记录推送到远程仓库

2. 在合并远程仓库中，既可以选择rebase也可以选择merge，rebase的优点是提交树干净，所有提交都在一条线上。缺点是修改了提交树的历史。merge能够保留提交历史。

   - ```git checkout main```：移到本地main上。
   - ```git pull```：抓取远程仓库并与之合并。
   - ```git merge side1```：合并side1到main上（此时head在main并且main和o/main一起联动）。
   - ```git push```：保留所有提交历史将相同的提交记录推送到远程仓库。

3. 远程跟踪，在clone得时候git就会在本地创造一个远程分支（如o/main），以及跟踪远程仓库中活动分支的本地妇女之（如main）。

   可以通过自型设定任意分支来跟踪远程分支，这样在push得时候merge目标就是你设定的分支。

   - 可通过如下第一种方法设定属性

   ```git checkout -b foo o/main```

   这样foo在pull或者push得时候就可以直接跟踪远程仓库的main分支。

   - 或者用第二种方法：

   ```git branch -u o/main foo```

   这样foo就会跟踪o/main，如果本身就在foo上，可以直接省略foo。

4. ```git push <remote> <place>```：对push指定参数，切换本地分支<place>获取所有提交，并在远程仓库origin中找到这个分支并添加上去。如果不指定参数，则会以HEAD的位置进行push。同样可用于pull指令。

5. ```<place>```参数：上面的参数可以指定来源和目的地```<source>:<destination>```。在push指令的情况下，source为本地，destionation为远程仓库的指定位置。当使用push如果远程不存在，可以自动生成新分支。

6. 接第五条，在使用fetch指令时，source和destination刚好和上面相反，前者是远程，后者是本地。同样在fetch时本地不存在分支，则会自动生成新分支。如果```git fetch```没有指定参数，则会下载提交记录到所有分支。

7. ```<source>```：在push和fetch时也可以不指定任何source，仅保留冒号和destionation。

   ```git push origin :foo```传一个空值给source，则直接删除了远程的foo。

   ```git fetch origin :bar```则会在本地创建一个新分支。

8. ```git pull```就等于```git fetch```加上```git merge```，而pull唯一关注的时是提交最终合并到哪里（也就是fetch所提供的destination参数）