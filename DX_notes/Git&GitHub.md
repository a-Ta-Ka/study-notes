
# Git/GitHub

## Git
### 用户和仓库配置

首先我们要设置一个全局用户名和邮箱
```cpp
git config --global user.name "名称"
git config --global user.email "邮箱"
// config  是一个子命令，用于读写Git配置项
// 作用范围 --local/global/system 分别是仓库、用户、系统级
git config --list     //输出所有对于Git的配置
```

然后我们可以弄一个仓库
```cpp
git init              //在当前目录新建一个Git仓库
git clone <url>       //把别人的仓库克隆到此目录下

```

### 常见指令

#### 日常提交

```cpp
git status     //身处一个git处，可以查看此git的文件状态

git add<file>  //将 file 添加到暂存区
git add .      //将当前目录所有更改添加到暂存区
git diff       //查看尚未暂存的修改

git commit -m "说明"   //将暂存区内容提交到本地仓库
git commit --amend -m "新的说明"  //修改最后一次提交，可以改名字追加文件
```

#### 分支管理

```cpp
git branch xx
git branch            //查询分支及所处分支
git branch -v         //同时显示每个分支的最后一次提交
git branch -m yy      //更改所处分支的名字（适用于未提交过的分支）
git branch -d <分支名>  //删除已合并的分支
git merge <分支名>              //将指定分支合并到当前分支
  
git checkout xx yy
//checkout依旧xx的不同有多种功能，yy指 命名，但新版的git里有更易懂的名字

git checkout -b yy             //创造名为yy的新分支
git switch -c yy

git checkout yy                //切换到已有分支yy
git switch yy

git checkout -- yy             //撤销工作区yy文件修改
git restore yy

git checkout <commit> -- yy    //把某文件恢复到某次提交的版本
git restore --source=<commit> yy

```

## GitHub

GitHub就是一个远端仓库，我们通过命令与其交互

```cpp
git remote -v      //查看关联了的远程仓库地址
git remote add origin <url>   //添加远端仓库

git fetch        //只拉取远程更新到本地，但要手动merge合并到当前分支
git pull         //拉取远程更新并自动合并到当前分支

git push origin <分支名>       //将本分支推送到远程仓库
git push -u origin <分支名>    //首次推送并建立跟踪关系,后可直接push
git push origin --delete <分支名>    //删除远程分支

```
