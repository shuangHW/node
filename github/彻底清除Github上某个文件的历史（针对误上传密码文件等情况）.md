原文地址：http://blog.csdn.net/ysy950803/article/details/53383582

这几天用JDBC写一个开源项目玩，测试的时候为了方便连接数据库就直接把root密码都写进代码文件了，然后Commit再Sync，成功把密码暴露到Github上。

大家肯定不想自己数据库所在服务器的IP和密码暴露，所以这里提供3个办法：
1. 把Github上整个项目删了重新创建并上传不含密码的代码，这个对已经维护过很久的项目是致命的，此乃下策；
2. 直接把数据库密码改了，暴露就暴露呗，别人看见了也连不上，此乃中策；
3. **上策** 也就是我们今天要说的了，Github for Windows/Mac桌面应用以及网页版都没有提供清除某个文件操作记录的功能，就是说即便你删了这个文件重新Push，那么别人依然可以查看你上一个版本。所以我们需要的是把和这个文件有关的所有Commit等记录全部删掉当然也包括文件本身。
首先在Git Bash或者CMD或者PowerShell中cd进入到你的本地项目文件夹，然后依次执行下面 **6行命令** 即可：

```
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch FILE_PATH' --prune-empty --tag-name-filter cat -- --all

git push origin master --force

rm -rf .git/refs/original/

git reflog expire --expire=now --all

git gc --prune=now

git gc --aggressive --prune=now
```

（注意上面的FILE_PATH要是路径而不只是文件名字，
例如src/main/java/com/ysy/demo/filename.java）

上述方法亲测有效。
参考资料：
https://help.github.com/articles/remove-sensitive-data/
