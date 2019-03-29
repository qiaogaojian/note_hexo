# gitignore不起作用的解决办法

## 问题原因

首先，.gitignore文件我们在.gitignore文件中增加需要忽略的文件并更新后，有时会出现相关文件并未被忽略的情况，即更新后的.gitignore并未生效。原因是.gitignore只能忽略未被track的文件，而git有本地缓存。如果增加的ignore文件原来被track过，则忽视.gitignore的规则。

## 解决方案

.gitignore文件必须存放在项目根目录下。
首先进入项目根目录，输入git status：如果要忽略的文件或文件夹不在untracked files列表中，则说明该文件已加入git cache。
这时，输入

```git
git rm --cached app/build/UnusedFiles
```

清除 UnusedFiles 缓存，现在该文件不会被track了。

## 参考链接

[Why doesn't Git ignore my specified file?
](https://stackoverflow.com/questions/3833561/why-doesnt-git-ignore-my-specified-file)