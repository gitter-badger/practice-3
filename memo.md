# memo space
md 形式でメモを残したいときはここに書くことにする。  

### バーチャルホスト設定
210.168.71.158 に以下が設定されたことを確認
```
<VirtualHost *>
    DocumentRoot /home/sites/www.nukinavi-212.com/web
    ServerName  mnt.www.nukinavi-212.com
    ServerAlias dev.mnt.www.nukinavi-212.com
    ErrorLog /home/sites/www.nukinavi-212.com/logs/error_log
    CustomLog /home/sites/www.nukinavi-212.com/logs/access_log combined
    <Directory "/home/sites/www.nukinavi-212.com/web/">
      AllowOverride Authconfig FileInfo Limit Options
    </Directory>
</VirtualHost>
```

また `cat /var/lib/logrotate.status` で確認した限りでは、www.nukinavi-212.com に対してログローテートもされていそうな雰囲気。
