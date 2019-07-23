# git恢复删除的stash代码

## 列出删除列表

```git
git fsck --lost-found
```

## 复制列表中dangling commit 中的 id

```git
dangling commit **7010e0447be96627fde29961d420d887533d7796**
```

## 查看具体信息

```git
git show 7010e0447be96627fde29961d420d887533d7796
```

## 根据信息找到并恢复记录

```git
git merge 7010e0447be96627fde29961d420d887533d7796
```