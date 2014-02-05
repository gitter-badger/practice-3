devモード表示がへん？
---------------------
xdebugによるvar_dumpのHTML整形機能によるものらしい。  
こんな風に表示される。見づらい･･･  

```dump
'genre_list' =>
  array(2) {
    'gm_dump_var_class' =>
    string(17) "Bd_Db_Record_List"
    'gm_dump_var_func_list' =>
    array(64) {
      'gm_dump_var_func_name_private _createOverlapList' =>
      array(1) {
        ...
      }
      'gm_dump_var_func_name_public __construct' =>
      array(1) {
        ...
      }
```  

`xdebug.overload_var_dump = 0` をxdebug.ini.erbに追記。  
** 既にxdebug.iniが存在する場合、上書きはされない **ので予めxdebug.iniを  
削除なりリネームなりしておく。その上で、`vagrant provision` もしくは  
`vagrant up --provision` を実行する。