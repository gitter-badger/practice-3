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
* Windowsキー + Rキー のあと、`regedit` と入力  

2. レジストリ編集
`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Console\TrueTypeFont` を開き、そこに新しい文字列を追加する。  

＜文字列の追加内容＞  
* 値の名前：932.
* 値のデータ：(使いたいフォント名)

細かい理屈は理解できていませんが、932を指定することでCP932でフォントが適用されるらしいです。  
値の名前をさらに追加したいときは、932.1、932.2 みたいなかんじで増やせました。  
フォント名は「コントロールパネル」→「フォント」から設定したいフォントを探してください。  

あとはコマンドプロンプトやgitbashのプロパティからフォントタブで変更可能になります。