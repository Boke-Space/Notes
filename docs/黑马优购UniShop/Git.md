> *Git*

> - 提交到master

```bash
git add  文件夹/
git commit -m "描述"
git remote add 别名 github网址
git push -u	别名 master
```



> - 提交到分支并合并

```bash
git checkout -b 分支名							创建分支				
git add	文件夹/
git commit -m "描述"
git merge 分支名
git push -u	别名 master
git branch -d cate							   删除分支			
```

