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
    * `git push origin:proj/example/xxxxxx`(ブランチ名は適宜書き換えてください。)
4. pivotal のステータスを Accept に変更する。
(基本、本番サーバーへのアップを以てAcceptを押す。作業は終わっているがアップは確認待ち などの場合はdeliver、またはAcceptで止めておく。)

### 備考
アップ先、本番サーバーへのアップ可否、pivotalのステータス、については自分で判断しにくい場合は都度確認したほうがよさげです。
