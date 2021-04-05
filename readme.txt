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

SQlite配置和使用
    下载sqlite的dll文件和tool文件 https://www.sqlite.org/download.html
    解压在一个文件夹里，我的是在c：\work\sqlite
    配置windows路径，打开cmd，输入  sqlite3 看到有反应
    在django里，
      python manage.py dbshell

改变模型需要这三步：
      编辑 models.py 文件，改变模型。
      运行 python manage.py makemigrations 为模型的改变生成迁移文件。
      运行 python manage.py migrate 来应用数据库迁移。

EC2:
    安装python3:
      sudo yum install python3 -y
    仮想環境の作成
      mkdir myweb
      cd myweb
      python3 -m venv env
    进入虚拟环境
      source env/bin/activate
    安装django
      pip3 install django
      确认: python3 -m django --version
    安装mod_wsgi
      直接安装mod_wsgi会出错，所以先安装下面的东西
      sudo yum install gcc
      sudo yum install httpd-devel
      sudo yum install python3-devel
      进入虚拟环境 source env/bin/activate

      pip3 install mod_wsgi
      确认：find -name 'mod_*.so'
      结果：./env/lib/python3.7/site-packages/mod_wsgi/server/mod_wsgi-py37.cpython-37m-x86_64-linux-gnu.so
      全path：/home/ec2-user/myweb/env/lib/python3.7/site-packages/mod_wsgi/server/mod_wsgi-py37.cpython-37m-x86_64-linux-gnu.so

    升级SQLite
      到此为止，如果想测试django可不可以运行，实际上是不够的，因为执行runserver会出现下面的err
      jango.core.exceptions.ImproperlyConfigured: SQLite 3.8.3 or later is required (found 3.7.17).
      需要升级django自己带的数据库SQLite
      下载:
        wget https://www.sqlite.org/2021/sqlite-autoconf-3350400.tar.gz  注意，这个是安装时点，在sqlite主页上最新的版本
      解压&编译安装：
        tar xzf sqlite-autoconf-3350400.tar.gz
        cd sqlite-autoconf-3350400
        ./configure --prefix=/usr/local
        make     注意，等待时间较长
        sudo make install
        確認：which sqlite3
             sqlite3 --version
      置換
      　sudo mv /usr/bin/sqlite3 /usr/bin/sqlite3_old
      　sudo ln -s /usr/local/bin/sqlite3 /usr/bin/sqlite3
      　ll /usr/bin/sqlite3
      sqlitelib設定
        echo export LD_LIBRARY_PATH="/usr/local/lib" >> ~/.bash_profile
        source ~/.bash_profile

        sudo vi /etc/ld.so.conf.d/sqlite3-x86_64.conf
      　#以下を書き込む
　　　　/usr/local/lib

        sudo ldconfig
        ldconfig -p | grep sqlite


    到此为止，可以试着运行一下django自己带的server，应该好使中
       python manage runserver
    但是，由于在ec2中，安全协议没有打开8000端口，所以接下来走httpd的配置。
    （最开始配置的时候忘了，途中更改安全协议会导致这个安全协议失效，这台服务器不可访问，我又不想重新配置一边，所以继续走httpd的配置
     但是上边的命令是能够显示，测试服务器是正常运行中的）
     (在网上找了一下如何用root用户跑80端口django的测试服务器，直接出了sqlite版本不足的异常，我特么。。。想去改root的profile，又担心
     改不好root也特么不好使了，突然想着，既然本番环境肯定是httpd，我在这缘木求鱼干哈呢？放弃了，坚决走httpd的配置！！
     )

    Apacheとmod_wsgiの連携設定
    conf内容：
    ----
    LoadModule wsgi_module /home/ec2-user/myweb/env/lib/python3.7/site-packages/mod_wsgi/server/mod_wsgi-py37.cpython-37m-x86_64-linux-gnu.so

    WSGIScriptAlias / /home/ec2-user/django_test/mytestsite/mytestsite/wsgi.py
    WSGIPythonPath /home/ec2-user/myweb/env/lib/python3.7/site-packages
    WSGIPythonHome /home/ec2-user/myweb/env

    <Directory /home/ec2-user/django_test/mytestsite/mytestsite>
      <Files wsgi.py>
        Require all granted
      </Files>
    </Directory>
    ----
    sudo vi /etc/httpd/conf.d/django.conf
