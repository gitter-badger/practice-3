# 手順関係のまとめ

## リモートリポジトリの削除方法

1.対象のリモートリポジトリに Github 上からアクセスする。  
2.右の各メニューから Settings をクリックする。  
3.Delete repository をクリックする。  
4.削除したいリポジトリ名の入力を求められるので入力。  
5.I understand the consequences, delete this repository ボタンが有効になるので押す。  
6.Successfully のメッセージが表示されれば完了。  

#### 参考
<https://help.github.com/articles/deleting-a-repository>  

## コマンドプロンプト、gitbashのフォントを好きなものに変える

通常はMSゴシック、ラスターフォントの2種類しかないですが、レジストリの編集で追加できるようです。  
デフォルトフォントじゃ味気ないなー、という人向け。  

■手順  
1. レジストリエディタの起動
Windowsキー + Rキー のあと、`regedit` と入力  
2. レジストリ編集  
`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Console\TrueTypeFont` を開き、そこに新しい文字列を追加する。  

＜文字列の追加内容＞  
* 値の名前：932.
* 値のデータ：(使いたいフォント名)

細かい理屈は理解できていませんが、932を指定することでCP932でフォントが適用されるらしいです。  
値の名前をさらに追加したいときは、932.1、932.2 みたいなかんじで増やせました。  
フォント名は「コントロールパネル」→「フォント」から設定したいフォントを探してください。  

あとはコマンドプロンプトやgitbashのプロパティからフォントタブで変更可能になります。

##本アップ ～ 案件終了までの流れ
### 本番サーバーへのアップ
1. `git diff master --name-only` 等で編集を行ったファイルを確認
2. 各ファイルの差分を確認しながらアップを行う。(Winmergeを使うと楽)
3. ブランチの掃除
  * master への merge手順
    * master に checkout して pull を実行しておく
    * master にブランチを merge する
    * origin に push する(pull request は自動クローズされる)
  * リモートブランチの削除
    * `git push origin :proj/example/xxxxxx`(ブランチ名は適宜書き換えてください。)
4. pivotal のステータスを Accept に変更する。
(基本、本番サーバーへのアップを以てAcceptを押す。作業は終わっているがアップは確認待ち などの場合はdeliver、またはAcceptで止めておく。)

### 備考
アップ先、本番サーバーへのアップ可否、pivotalのステータス、については自分で判断しにくい場合は都度確認したほうがよさげです。

## dockerマシンのデータを更新
`bash /vagrant/VAGRANT.bash` を実行するだけでOKのはずが以下の問題あり。

* `vagrant up` 後、`vagrant ssh` でログインし、`docker ps` を実行すると、動いているのはいつも
`etcd` と `master-fuzoku-db-images` の2つだけ。
* `bash /vagrant/VAGRANT.bash` を実行し、`master-fuzoku-db コンテナセットを起動しますか？[y/N]` で、`y` を選択すると、次のエラーが出る。

```bash
Error: Conflict, The name master-fuzoku-db-images is already assigned to "コンテナID". You have to delete (or rename) that container to be able to assign master-fuzoku-db-images to a container again.
master-fuzoku-db-images の起動に失敗しました。
```

### 以下の手順でとりあえず解決
* エラーの内容的に既に master-fuzoku-db-images コンテナがあることが問題っぽいので、該当コンテナを削除。
  * `docker stop "画像コンテナのID"`
  * `docker rm "画像コンテナのID"`
* master-fuzoku-db-mysql コンテナも残っているので上記と同じ手順でついでに削除
* `bash /vagrant/VAGRANT.bash` を再度実行

docker マシンを作り直すときは、少なくともfuzoku-db 関連のコンテナは全部削除してからやらないとエラーになるっぽい。

### ところが首都圏閲覧時だけエラーになる
```error
Fatal error: Maximum execution time of 30 seconds exceeded
```
開発モードで見たら上記エラーあり。ところがリロードのたびにエラーのソースが変わる。フレームワーク側だったりそうでなかったり。ネットで調べてみたらそれっぽい解決策もあったのでとりあえず試してみる。

1. php.ini の max_execution_time を増やす
2. 仮想CPUコア数を2に増やす

2を行ったところ一応閲覧できるようになる。それでもめっちゃ重い。なんだろう。。。

※追記
`configure.bash` に `NO_FUZOKU_DB_IMAGES=1` を書いてもみたけど効果はほとんど見られず。画像の取得で重くなっているわけではなさそう。
