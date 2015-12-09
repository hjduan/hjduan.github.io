## Shell知识汇总

### tmux

* reload conf

        $ tmux source-file ~/.tmux.conf
        
* mouse on

        set -g mode-mouse on    # in .tmux.conf
        
### Centos新增用户

        useradd <username>
        
        passwd <username>
        
第一个命令设置用户名，第二个命令设置<username>的密码。

        usermod -d /usr/newfolder username   # 该命令用于修改 user 的默认 home 目录

### 删除pid

        $ ps -ef | grep PROCESS | grep -v grep | awk '{print $2}' | xargs kill -9
    
### 安装ftp

        $ yum install -y vsftpd
        $ service vsftpd start
        
修改配置文件： /etc/vsftpd/vsftpd.conf

        # Allow anonymous FTP? (Beware - allowed by default if you comment this out).
        anonymous_enable=NO
        
        # ASCII mangling is a horrible feature of the protocol.
        ascii_upload_enable=YES
        ascii_download_enable=YES
        #
        # You may fully customise the login banner string:
        ftpd_banner=Welcome to OSTECHNIX FTP service.

        use_localtime=YES
        
### mysql相关

* dump完整的数据库

        mysqldump -u someuser -p mydatabase

* dump数据库中的表数据

        mysqldump -P3308 -u arts -p -h 10.1.6.241 Arts Arts_RedressWord > arts_redressword.sql

        
* dump数据库的表结构，而不dump数据

        mysqldump -d -u someuser -p mydatabase
        or
        mysqldump --lock-tables=false --no-data -u someuser -p mydatabase

        
* 导入sql文件

        mysql> use DATABASE_NAME;
        mysql> source path/to/file.sql;

* 导出远程数据库表内容到本地文件

        mysql  -h 127.0.0.1 -u user -e "use DatabaseName;set names 'utf8'; select * from RemoteTableName;" -p > result.txt 

* reset auto_increment

        ALTER TABLE tablename AUTO_INCREMENT = 1
        
* 查询数据库字符集

        SELECT default_character_set_name FROM information_schema.SCHEMATA S
        WHERE schema_name = "DB_NAME";
    
* 修改数据库字符集

        ALTER DATABASE databasename CHARACTER SET utf8 COLLATE utf8_unicode_ci;

* 添加新的访问用户

        GRANT ALL PRIVILEGES ON dbTest.* To 'user'@'hostname' IDENTIFIED BY 'password';
        
        GRANT ALL PRIVILEGES ON *.* TO admin@localhost IDENTIFIED BY 'password' WITH GRANT OPTION;


        
#### 拷贝文件内容到剪切板

        $ pbcopy < file.txt
        
#### Client端配置Kerberos

* `kinit`  //该命令会提示输入当前用户的kerberos密码，输入完成后，会在/tmp目录下生成一个krb文件，可以通过`klist`查看
* `ktutil` 回车，接着输入：`addent -password -p username@MYDOMAIN.COM -k 1 -e RC4-HMAC`，回车后提示输入密码
* `wkt username.keytab`，此时会在home目录生成一份.keytab文件
* 配置cron job，每天更新ticket：`export KRB5CCNAME=/tmp/krb5_xxx; kinit -k -t /home/username/.keytab yourusername -r 24l; kinit -R;`

#### Git
* 删除本地分支：

        $ git branch -D [local_branch]
        
* 删除远程分支：

        $ git push origin :[remote_branch]
        
* githup clone 分支的时候报 403 错误，是因为没有权限，需要账号密码才能clone：

        $ git clone 'git clone https://username@github.com/username/repository.git'


#### Hadoop 相关
* 查看 Hive 表信息：

        $ hive -e 「describe formatted table_name」


#### 拷贝到内存文件

        cp xx /dev/shm

#### 下载整站

        wget -r -p -np -k -E 'http://www.baidu.com'
        
#### tomcat 部署

1. war包放到$TOMCAT_HOME/webapps

2. 修改$TOMCAT_HOME/conf/server.xml，在Host配置段中添加类似于如下内容：

        <Context path="/" docBase="hdfs-webdav.war" debug="0" privileged="true" reloadable="true"/>
        
ps：如果局域网内其他机器访问不了，很可能是防火墙的问题。通过 `telnet serverip 8080`看能不能访问这台机器，如果连接不上，那么可以确定是防火墙问题，修改 /etc/sysconfig/iptables，增加一行：

        -A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT 
        service iptables restart

#### uwsgi 部署

1. 记录日志

        uwsgi --logto /logs/requests.log


#### 阿里云安装 maven

sudo wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo
sudo sed -i s/\$releasever/6/g /etc/yum.repos.d/epel-apache-maven.repo
sudo yum install -y apache-maven
mvn --version


#### 压力测试

        ab -n 1000 -c 10 http://www.baidu.com
        
参数说明：  
-n requests Number of requests to perform  
-c concurrency Number of multiple requests to make  
-t timelimit Seconds to max. wait for response  
-p postfile File containing data to POST  
-T content-type Content-type header for POSTing  
-v verbosity How much troubleshooting info to print  
-w Print out results in HTML tables  
-i Use HEAD instead of GET


#### 监控网络带宽

        nload   // yum install nload -y 
        
#### nginx 日志分析利器 GoAccess

* 安装

        yum install goaccess
        
* 解析

        goaccess -f <input-file> -o --output-format=csv|json -p --conf-file=<filename> > access.log
        
        goaccess -q -f web.log -a -p /home/yejianfeng/.goaccessrc -o json >test.json
        
        goaccess -q -f web.log -a -p /home/yejianfeng/.goaccessrc -o csv>test.csv