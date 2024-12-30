# ibmdb2whl
pip直接安装ibm_db库报错处理

原因：

```
直接安装对应的ibm_db(ibm-db==3.2.4，macOS arm64)显示安装成功,但是在调用时报错

base) woo@Mac ~ % conda activate py311
(py311) woo@Mac ~ % python
Python 3.11.11 (main, Dec 11 2024, 10:25:04) [Clang 14.0.6 ] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import ibm_db
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ImportError: dlopen(/Users/woo/anaconda3/envs/py311/lib/python3.11/site-packages/ibm_db.cpython-311-darwin.so, 0x0002): symbol not found in flat namespace '_SQLAllocHandle'
>>> 
```



准备工作

1、安装 准备

```
xcode-select --install      #  Xcode Command Line Tools
brew install unixodbc
```

2、下载**IBM Db2 CLI Driver**

链接： https://public.dhe.ibm.com/ibmdl/export/pub/software/data/db2/drivers/odbc_cli/

~~~
直接下载
https://public.dhe.ibm.com/ibmdl/export/pub/software/data/db2/drivers/odbc_cli/v12.1.0/macarm64_odbc_cli.tar.gz
~~~

3、解压并配置环境变量

假设下载的 clidriver 已解压到 /opt/ibm/clidriver，添加以下内容到 ~/.zshrc 或 ~/.bashrc：

~~~
export IBM_DB_HOME=/opt/ibm/clidriver
export PATH=$IBM_DB_HOME/bin:$IBM_DB_HOME/lib:$PATH
export DYLD_LIBRARY_PATH=$IBM_DB_HOME/lib:$DYLD_LIBRARY_PATH  

source ~/.zshrc
~~~

​	

~~~
验证安装,确保动态库路径正确：  echo $DYLD_LIBRARY_PATH

或者直接调用    /opt/ibm/clidriver/bin/db2cli validate
~~~

~~~
添加库路径到系统范围

sudo mkdir -p /usr/local/lib
sudo ln -s /opt/ibm/clidriver/lib/* /usr/local/lib/


解锁目录安全限制
sudo xattr -r -d com.apple.quarantine /opt/ibm/clidriver
~~~

下载最新源码

~~~
git clone git@github.com:ibmdb/python-ibmdb.git
切换到需要生成的对应环境，执行build命令
python setup.py bdist_wheel

build后的文件在dist/里
(py311) woo@Mac python-ibmdb % pip install dist/ibm_db-3.2.4-cp311-cp311-macosx_11_0_arm64.whl
Looking in indexes: https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
Processing ./dist/ibm_db-3.2.4-cp311-cp311-macosx_11_0_arm64.whl
Installing collected packages: ibm-db
Successfully installed ibm-db-3.2.4
(py311) woo@Mac python-ibmdb % python
Python 3.11.11 (main, Dec 11 2024, 10:25:04) [Clang 14.0.6 ] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import ibm_db
>>> 


~~~


