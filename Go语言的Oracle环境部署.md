# Go语言的oracle驱动安装

## 请求软件包
- 保证系统的架构一致，即golang是64位，那么oracle库也要是64位。

- golang包列表
```
    go1.6.2.linux-amd64.tar.gz
    github.com/mattn/go-oci8
    github.com/rana/ora
```

- oracle包列表
```
    instantclient-basic-linux.x64-12.1.0.2.0.zip (库)
    instantclient-sdk-linux.x64-12.1.0.2.0.zip   (头文件)
```

## 配置
- 将go1.6.2.linux-amd64.tar.gz安装于/usr/local/go目录中。
- 建立目录/opt/oracle/12.1
- 将instantclient-basic-linux.x64-12.1.0.2.0.zip解压后，放入/opt/oracle/12.1/lib目录。
- 将instantclient-sdk-linux.x64-12.1.0.2.0.zip解压后，*.h文件放入/opt/oracle/12.1/include目录。

- 配置环境变量
```
    export GOROOT=/usr/local/go
    export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
    export PATH=$GOROOT/bin:$PATH
    export ORACLE_HOME=/opt/oracle/12.1
    export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$LD_LIBRARY_PATH
```

- 建立链接
```
ln -s libclntsh.so.12.1 libclntsh.so
```

- 新增库搜索目录，在/etc/ld.so.conf文件中添加行/opt/oracle/12.1/lib，执行ldconfig -p 命令，检查库是否已加载到库目录缓存中
```
[root@ali-test-2 lib]# ldconfig -p | grep libclntsh.so
        libclntsh.so.12.1 (libc6,x86-64) => /opt/oracle/12.1/lib/libclntsh.so.12.1
        libclntsh.so.12.1 (libc6,x86-64) => /usr/lib/libclntsh.so.12.1
        libclntsh.so.12.1 (libc6,x86-64) => /usr/lib64/libclntsh.so.12.1
        libclntsh.so (libc6,x86-64) => /opt/oracle/12.1/lib/libclntsh.so
        libclntsh.so (libc6,x86-64) => /usr/lib/libclntsh.so
```

- 在$PKG_CONFIG_PATH目录中放置oci8.pc配置文件，按需调整。
```
[root@ali-test-2 pkgconfig]# cat oci8.pc
libdir=/opt/oracle/12.1/lib
includedir=/opt/oracle/12.1/include

Name: oci8
Description: Oracle database engine
Version: 12.1
Libs: -L${libdir} -lclntsh
Libs.private:
Cflags: -I${includedir}
```

- go install 相关oracle驱动包。

