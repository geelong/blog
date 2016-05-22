###本地新建仓库

cd /test

git init

###提交文件

git add.

git commit -m "add file"


###克隆仓库
cd /test2

git clone /test

###修改
touch a

###提交
git add . && git commit -m "test" && git push origin  master


###本地仓库更新
cd /test

(很奇怪)

 git reset HEAD . && git checkout -- .