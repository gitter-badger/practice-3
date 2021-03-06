MySQL SOCKET ERROR まとめ
--------------------------

### 現象内容
01/09 に sunrise を pull したときから仮想マシン起動時に  
以下のエラーが出るようになった。  

> board.sunrisedigital.jp 接続時  
```error
SQLSTATE[HY000] [2002] Can't connect to local MySQL server through socket 
'/var/lib/mysql/mysql.sock' (111)#0 /home/source/sites/sdx/models/Zend/Db/Adapter/Pdo/Mysql.php(109):
Zend_Db_Adapter_Pdo_Abstract->_connect()
```  

> vagrant provision 時  
```error
Error executing action `start` on resource 'service[mysqld]'
STDOUT: Another MySQL daemon already running with the same unix socket.
```  

### 調査内容
database.yml に記載のアドレスと、実際のソケットファイルの場所は一致していて  
実際に mysql.sock もあった。ただ、ファイルサイズが 0 になっていた。Filezilaで  
直接編集してみようとしたがパーミッションではじかれた。  

なんか他にunix socketが動いているとか書いてあるのでプロセスを確認。  

```ps
	[vagrant@localhost ~]$ ps -aux
	Warning: bad syntax, perhaps a bogus '-'? See /usr/share/doc/procps-3.2.8/FAQ
	USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
	root         1  0.3  0.1  19232  1488 ?        Ss   03:29   0:01 /sbin/init
	root         2  0.0  0.0      0     0 ?        S    03:29   0:00 [kthreadd]
	root         3  0.1  0.0      0     0 ?        S    03:29   0:00 [migration/0]
	root         4  0.0  0.0      0     0 ?        S    03:29   0:00 [ksoftirqd/0]
	root         5  0.0  0.0      0     0 ?        S    03:29   0:00 [migration/0]
	root         6  0.0  0.0      0     0 ?        S    03:29   0:00 [watchdog/0]
	root         7  5.4  0.0      0     0 ?        S    03:29   0:29 [migration/1]
	root         8  0.0  0.0      0     0 ?        S    03:29   0:00 [migration/1]
	root         9  0.0  0.0      0     0 ?        S    03:29   0:00 [ksoftirqd/1]
	root        10  3.3  0.0      0     0 ?        S    03:29   0:18 [watchdog/1]
	root        11  0.1  0.0      0     0 ?        S    03:29   0:00 [events/0]
	root        12  0.4  0.0      0     0 ?        S    03:29   0:02 [events/1]
	root        13  0.0  0.0      0     0 ?        S    03:29   0:00 [cgroup]
	root        14  0.0  0.0      0     0 ?        S    03:29   0:00 [khelper]
	root        15  0.0  0.0      0     0 ?        S    03:29   0:00 [netns]
	root        16  0.0  0.0      0     0 ?        S    03:29   0:00 [async/mgr]
	root        17  0.0  0.0      0     0 ?        S    03:29   0:00 [pm]
	root        18  0.0  0.0      0     0 ?        S    03:29   0:00 [sync_supers]
	root        19  0.0  0.0      0     0 ?        S    03:29   0:00 [bdi-default]
	root        20  0.0  0.0      0     0 ?        S    03:29   0:00 [kintegrityd/0]
	root        21  0.0  0.0      0     0 ?        S    03:29   0:00 [kintegrityd/1]
	root        22  0.0  0.0      0     0 ?        S    03:29   0:00 [kblockd/0]
	root        23  0.0  0.0      0     0 ?        S    03:29   0:00 [kblockd/1]
	root        24  0.0  0.0      0     0 ?        S    03:29   0:00 [kacpid]
	root        25  0.0  0.0      0     0 ?        S    03:29   0:00 [kacpi_notify]
	root        26  0.0  0.0      0     0 ?        S    03:29   0:00 [kacpi_hotplug]
	root        27  0.0  0.0      0     0 ?        S    03:29   0:00 [ata_aux]
	root        28  0.2  0.0      0     0 ?        S    03:29   0:01 [ata_sff/0]
	root        29  0.0  0.0      0     0 ?        S    03:29   0:00 [ata_sff/1]
	root        30  0.0  0.0      0     0 ?        S    03:29   0:00 [ksuspend_usbd]
	root        31  0.0  0.0      0     0 ?        S    03:29   0:00 [khubd]
	root        32  0.0  0.0      0     0 ?        S    03:29   0:00 [kseriod]
	root        33  0.0  0.0      0     0 ?        S    03:29   0:00 [md/0]
	root        34  0.0  0.0      0     0 ?        S    03:29   0:00 [md/1]
	root        35  0.0  0.0      0     0 ?        S    03:29   0:00 [md_misc/0]
	root        36  0.0  0.0      0     0 ?        S    03:29   0:00 [md_misc/1]
	root        37  0.0  0.0      0     0 ?        S    03:29   0:00 [linkwatch]
	root        38  0.0  0.0      0     0 ?        S    03:29   0:00 [khungtaskd]
	root        39  0.0  0.0      0     0 ?        S    03:29   0:00 [kswapd0]
	root        40  0.0  0.0      0     0 ?        SN   03:29   0:00 [ksmd]
	root        41  0.0  0.0      0     0 ?        SN   03:29   0:00 [khugepaged]
	root        42  0.0  0.0      0     0 ?        S    03:29   0:00 [aio/0]
	root        43  0.0  0.0      0     0 ?        S    03:29   0:00 [aio/1]
	root        44  0.0  0.0      0     0 ?        S    03:29   0:00 [crypto/0]
	root        45  0.0  0.0      0     0 ?        S    03:29   0:00 [crypto/1]
	root        50  0.0  0.0      0     0 ?        S    03:29   0:00 [kthrotld/0]
	root        51  0.0  0.0      0     0 ?        S    03:29   0:00 [kthrotld/1]
	root        55  0.0  0.0      0     0 ?        S    03:29   0:00 [kpsmoused]
	root        56  0.0  0.0      0     0 ?        S    03:29   0:00 [usbhid_resumer]
	root        87  0.0  0.0      0     0 ?        S    03:29   0:00 [kstriped]
	root       156  0.0  0.0      0     0 ?        S    03:29   0:00 [scsi_eh_0]
	root       157  0.0  0.0      0     0 ?        S    03:29   0:00 [scsi_eh_1]
	root       167  0.0  0.0      0     0 ?        S    03:29   0:00 [scsi_eh_2]
	root       257  0.0  0.0      0     0 ?        S    03:29   0:00 [kdmflush]
	root       258  0.0  0.0      0     0 ?        S    03:29   0:00 [kdmflush]
	root       276  0.0  0.0      0     0 ?        S    03:29   0:00 [jbd2/dm-0-8]
	root       277  0.0  0.0      0     0 ?        S    03:29   0:00 [ext4-dio-unwrit]
	root       357  0.0  0.0  10880   944 ?        S<s  03:29   0:00 /sbin/udevd -d
	root       409  0.0  0.0      0     0 ?        S    03:30   0:00 [iprt/0]
	root       410  0.0  0.0      0     0 ?        S    03:30   0:00 [iprt/1]
	root       611  0.0  0.0  10880   976 ?        S<   03:30   0:00 /sbin/udevd -d
	root       640  0.0  0.0      0     0 ?        S    03:30   0:00 [jbd2/sda1-8]
	root       641  0.0  0.0      0     0 ?        S    03:30   0:00 [ext4-dio-unwrit]
	root       674  0.0  0.0      0     0 ?        S    03:30   0:00 [kauditd]
	root       810  0.0  0.0      0     0 ?        S    03:30   0:00 [flush-253:0]
	root      1290  0.0  0.0   9120   684 ?        Ss   03:30   0:00 /sbin/dhclient -1 -q -cf /etc/dhcp/
	root      1519  0.0  0.0  27640   788 ?        S<sl 03:30   0:00 auditd
	root      1535  0.0  0.1 249084  1572 ?        Sl   03:30   0:00 /sbin/rsyslogd -i /var/run/syslogd.
	rpc       1558  0.0  0.0  18976   892 ?        Ss   03:30   0:00 rpcbind
	rpcuser   1576  0.0  0.1  23348  1324 ?        Ss   03:30   0:00 rpc.statd
	root      1673  0.0  0.1 315628  1248 ?        Sl   03:30   0:00 /usr/sbin/VBoxService
	root      1692  0.0  0.1  66608  1232 ?        Ss   03:30   0:00 /usr/sbin/sshd
	root      1802  0.0  0.3  81280  3408 ?        Ss   03:30   0:00 /usr/libexec/postfix/master
	postfix   1807  0.0  0.3  81360  3372 ?        S    03:30   0:00 pickup -l -t fifo -u
	postfix   1808  0.0  0.3  81528  3412 ?        S    03:30   0:00 qmgr -l -t fifo -u
	root      1812  0.0  0.9 278232 10124 ?        Ss   03:30   0:00 /usr/sbin/httpd
	apache    1818  0.1  2.0 290584 20944 ?        S    03:30   0:00 /usr/sbin/httpd
	apache    1820  0.0  1.6 286948 16684 ?        S    03:30   0:00 /usr/sbin/httpd
	apache    1822  0.0  2.1 291328 21488 ?        S    03:30   0:00 /usr/sbin/httpd
	root      1823  0.0  0.1 117292  1240 ?        Ss   03:30   0:00 crond
	apache    1824  0.0  0.5 278232  5608 ?        S    03:30   0:00 /usr/sbin/httpd
	apache    1826  0.0  0.5 278232  5608 ?        S    03:30   0:00 /usr/sbin/httpd
	apache    1828  0.0  0.5 278232  5608 ?        S    03:30   0:00 /usr/sbin/httpd
	apache    1829  0.0  0.5 278232  5608 ?        S    03:30   0:00 /usr/sbin/httpd
	apache    1830  0.0  0.5 278232  5608 ?        S    03:30   0:00 /usr/sbin/httpd
	root      1841  0.0  0.0   4064   568 tty1     Ss+  03:30   0:00 /sbin/mingetty /dev/tty1
	root      1843  0.0  0.0   4064   568 tty2     Ss+  03:30   0:00 /sbin/mingetty /dev/tty2
	root      1845  0.0  0.0  10876   936 ?        S<   03:30   0:00 /sbin/udevd -d
	root      1846  0.0  0.0   4064   572 tty3     Ss+  03:30   0:00 /sbin/mingetty /dev/tty3
	root      1848  0.0  0.0   4064   564 tty4     Ss+  03:30   0:00 /sbin/mingetty /dev/tty4
	root      1850  0.0  0.0   4064   568 tty5     Ss+  03:30   0:00 /sbin/mingetty /dev/tty5
	root      1852  0.0  0.0   4064   568 tty6     Ss+  03:30   0:00 /sbin/mingetty /dev/tty6
	root      2584  0.0  0.0   9120   744 ?        Ss   03:31   0:00 /sbin/dhclient -1 -q -lf /var/lib/d
	root      3245  0.0  0.0      0     0 ?        S    03:33   0:00 [flush-vboxsf-2]
	root      3322  0.2  0.3 100356  3956 ?        Ss   03:38   0:00 sshd: vagrant [priv]
	vagrant   3324  0.5  0.1 100356  1788 ?        R    03:38   0:00 sshd: vagrant@pts/0
	vagrant   3325  0.7  0.1 108296  1868 pts/0    Ss   03:38   0:00 -bash
	vagrant   3342  2.0  0.1 110224  1172 pts/0    R+   03:38   0:00 ps -aux
```  

･･･特にそんなのなさげ。  

ちなみに正常に動作している際はこういうプロセスを吐いていた。  
```ps
mysql     3415  0.2  2.8 443008 28792 pts/0    Sl   00:56   0:00 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql
```  

### 解決策
pull 前の状態に戻した上で再起動  
```bash
$ git reset --hard HEAD^t 
$ vagrant reload
```

変わらず。  

ネットでソケットファイルを再生成させる方法があったので試してたところ  
うまくいった。手順は以下のようなもの。  

```bash
	[root@localhost ~]# service mysqld stop
	Stopping mysqld:                                           [  OK  ]
	[root@localhost ~]# mv /var/lib/mysql/mysql.sock /var/lib/mysql/mysql.sock.bak
	[root@localhost ~]# service mysqld start
	Starting mysqld:                                           [  OK  ]
	[root@localhost ~]#
```

仮想マシンにログインし、root権限で前述コマンドを実行。  
ソケットファイルの名前だけを変えて、mysql.sock に一致するファイルが  
存在しない状態を作り、擬似的に削除と同じ状態にする。  
ソケットファイルはMySQLサーバー起動時に自動生成されるため、この方法で  
再生成させる。  

mysql サーバー自体が前は起動できない状態だったが、ソケットファイルを  
リネームしたら無事起動できるようになった。WEBサイトもちゃんと見れるようになった。  
よかった。。。pull が原因なのかよくわからんけど、とりあえずしばらくpull はやめとこ。  

1/17追記：  
またソケットエラー出たのでこんどはリネームじゃなくて削除でやってみた。  
```bash
[root@localhost ~]# rm /var/lib/mysql/mysql.sock
rm: remove socket `/var/lib/mysql/mysql.sock'? yes
[root@localhost ~]# service mysqld start
Starting mysqld:                                           [  OK  ]
```  
これでもOKみたい。  


#### 余談
よく見るとプロセスを確認した際にこんな警告が出ている。  
```Warning: bad syntax, perhaps a bogus '-'? See /usr/share/doc/procps-3.2.8/FAQ```  
プロセスは確認できているので無視してもいいかもだけどなんか気になる。  
文法的に"-"はいらないってこと？  

ここが参考になりました。結論としては "-" は付けずに`ps aux`だけでよかったみたい。(実験済)  
* [ps -auxがエラー！](http://achakodisney.blog49.fc2.com/blog-entry-24.html "ps -auxがエラー！")
* [LinuxServer　プロセス管理について](http://www.sakc.jp/blog/archives/13597 "LinuxServer　プロセス管理について")  

### 2/3 再発時にログをとってみたのでメモ。
ログの確認箇所はネットで「このへんを確認するとよさげ？」なものを調べてみただけなので  
もしかしたら他に見るべきログがあるかもだけど、とりあえず。  


mysql.sock の作成日時が先週の金曜(1/31)になってる。  
今日(2/3)に仮想マシンを起動しているので、今日の日付でないとヘン。  
```bash
$ vagrant ssh
Last login: Fri Jan 31 01:11:07 2014 from 10.0.2.2
Welcome to your Vagrant-built virtual machine.
[vagrant@localhost ~]$ ls -al /var/lib/mysql
total 28692
drwxr-xr-x   5 mysql mysql     4096 Jan 31 00:59 .
drwxr-xr-x. 23 root  root      4096 Oct  7 08:22 ..
drwx------   2 mysql mysql     4096 Nov 28 04:50 board
-rw-rw----   1 mysql mysql 18874368 Jan 31 09:19 ibdata1
-rw-rw----   1 mysql mysql  5242880 Jan 31 09:19 ib_logfile0
-rw-rw----   1 mysql mysql  5242880 Oct  7 08:22 ib_logfile1
drwx------   2 mysql mysql     4096 Oct  7 08:22 mysql
srwxrwxrwx   1 mysql mysql        0 Jan 31 00:59 mysql.sock
drwx------   2 mysql mysql     4096 Oct  7 08:22 test
```  
とりあえず直近のログ。50件くらいでいいか。UTC表記なので実時間は+9時間で。  
日本時間に直したいんだけどやり方がわからん。。。  
```bash
[root@localhost ~]# tail -50 /var/log/mysqld.log
140129  8:17:46 [Note] Event Scheduler: Purging the queue. 0 events
140129  8:17:46  InnoDB: Starting shutdown...
140129  8:17:49  InnoDB: Shutdown completed; log sequence number 0 563110
140129  8:17:49 [Note] /usr/libexec/mysqld: Shutdown complete

140129 08:17:49 mysqld_safe mysqld from pid file /var/run/mysqld/mysqld.pid ended
140129 08:20:03 mysqld_safe Starting mysqld daemon with databases from /var/lib/mysql
140129  8:20:04  InnoDB: Initializing buffer pool, size = 8.0M
140129  8:20:04  InnoDB: Completed initialization of buffer pool
140129  8:20:04  InnoDB: Started; log sequence number 0 563110
140129  8:20:04 [Note] Event Scheduler: Loaded 0 events
140129  8:20:04 [Note] /usr/libexec/mysqld: ready for connections.
Version: '5.1.71'  socket: '/var/lib/mysql/mysql.sock'  port: 3306  Source distribution
140129 08:26:31 mysqld_safe Starting mysqld daemon with databases from /var/lib/mysql
140129  8:26:31  InnoDB: Initializing buffer pool, size = 8.0M
140129  8:26:31  InnoDB: Completed initialization of buffer pool
140129  8:26:31  InnoDB: Started; log sequence number 0 563110
140129  8:26:31 [Note] Event Scheduler: Loaded 0 events
140129  8:26:31 [Note] /usr/libexec/mysqld: ready for connections.
Version: '5.1.71'  socket: '/var/lib/mysql/mysql.sock'  port: 3306  Source distribution
140129 10:37:43 [Note] /usr/libexec/mysqld: Normal shutdown

140129 10:37:43 [Note] Event Scheduler: Purging the queue. 0 events
140129 10:37:43  InnoDB: Starting shutdown...
140129 10:37:45  InnoDB: Shutdown completed; log sequence number 0 563120
140129 10:37:45 [Note] /usr/libexec/mysqld: Shutdown complete

140129 10:37:45 mysqld_safe mysqld from pid file /var/run/mysqld/mysqld.pid ended
140130 00:51:12 mysqld_safe Starting mysqld daemon with databases from /var/lib/mysql
140130  0:51:13  InnoDB: Initializing buffer pool, size = 8.0M
140130  0:51:13  InnoDB: Completed initialization of buffer pool
140130  0:51:13  InnoDB: Started; log sequence number 0 563120
140130  0:51:13 [Note] Event Scheduler: Loaded 0 events
140130  0:51:13 [Note] /usr/libexec/mysqld: ready for connections.
Version: '5.1.71'  socket: '/var/lib/mysql/mysql.sock'  port: 3306  Source distribution
140130 10:16:55 [Note] /usr/libexec/mysqld: Normal shutdown

140130 10:16:55 [Note] Event Scheduler: Purging the queue. 0 events
140130 10:16:55  InnoDB: Starting shutdown...
140130 10:17:00  InnoDB: Shutdown completed; log sequence number 0 570700
140130 10:17:00 [Note] /usr/libexec/mysqld: Shutdown complete

140130 10:17:00 mysqld_safe mysqld from pid file /var/run/mysqld/mysqld.pid ended
140131 00:59:36 mysqld_safe Starting mysqld daemon with databases from /var/lib/mysql
140131  0:59:36  InnoDB: Initializing buffer pool, size = 8.0M
140131  0:59:36  InnoDB: Completed initialization of buffer pool
140131  0:59:37  InnoDB: Started; log sequence number 0 570700
140131  0:59:37 [Note] Event Scheduler: Loaded 0 events
140131  0:59:37 [Note] /usr/libexec/mysqld: ready for connections.
Version: '5.1.71'  socket: '/var/lib/mysql/mysql.sock'  port: 3306  Source distribution
```  
1/31にmysqlのシャットダウンが記録されていない。ということくらいしかわからん。。。
1/30が正常な動きのはずなので、1/31のログと比べて何が違うのかをとりあえず比較。  
パッと見ではエラー等は見受けられない。  

ログを比較した限りだと、1/30にあって1/31には無い動作がコレ。  
```bash
140130 10:16:55 [Note] /usr/libexec/mysqld: Normal shutdown

140130 10:16:55 [Note] Event Scheduler: Purging the queue. 0 events
140130 10:16:55  InnoDB: Starting shutdown...
140130 10:17:00  InnoDB: Shutdown completed; log sequence number 0 570700
140130 10:17:00 [Note] /usr/libexec/mysqld: Shutdown complete

140130 10:17:00 mysqld_safe mysqld from pid file /var/run/mysqld/mysqld.pid ended
```  

ちなみに同サーバー内につくった別ドメイン(DB非使用)にはアクセス可能。  
MySQL依存っぽい。
