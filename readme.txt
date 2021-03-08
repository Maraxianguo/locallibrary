git命令行
  初期化:git init
  添加所有文件：git add .
  提交：git commit -m "git init commit"   后边是提交时写的注释
  关联github：git remote add origin https://github.com/Maraxianguo/locallibrary.git
  把上述提交进 master 分支 ：git push -u origin master

  第二次及以后提交更新都是 git add . 和 git commit
  然后 git push
  查看状态命令 git status
  差分查看 git diff

 django教程网址
 https://developer.mozilla.org/zh-CN/docs/learn/Server-side/Django/skeleton_website

设置Django开发环境
  安装虚拟环境软件:pip3 install virtualenvwrapper-win
  创建虚拟环境 : mkvirtualenv my_django_environment
  使用一个虚拟环境
    deactivate —退出当前的Python虚拟环境
    workon — 列出可用的所有虚拟环境
    workon name_of_environment —激活特定的Python虚拟环境
    rmvirtualenv name_of_environment — 移除特定的虚拟环境
  安装Django
    再虚拟环境中，pip3 install django
    测试Django是否安装 : py -m django --version  #windows系统
    运行web ： py manage.py runserver
  运行数据库迁移
    python3 manage.py makemigrations
    python3 manage.py migrate
    重要信息: 每次模型改变，都需要运行以上命令，来影响需要存储的数据结构（包括添加和删除整个模型和单个字段）。
