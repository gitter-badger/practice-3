MySQL SOCKET ERROR まとめ
--------------------------

### 現象内容
01/09 に sunrise を pull したときから仮想マシン起動時に  
以下のエラーが出るようになった。

> vagrant up 時  
```error
Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock'
```
vagrant provision 時  
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
