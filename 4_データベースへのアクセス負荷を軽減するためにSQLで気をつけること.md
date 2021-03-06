# dデータベースへのアクセス負荷を軽減する方法
## N+1問題（ORM）
仮に10記事取得するケースだとSELECT文が  
「1回（記事一覧を取得）＋10回（各記事に紐づくタグを取得）＝合計11回」  
発行されてしまうことがる。

Railsの場合は、includesを使用することで最初の**記事**を取得する際に一緒に**紐づくタグ**を取得できるので、sqlの発行回数を抑えることができる。

## インデックスの使用
### インデックスとは
テーブルの中の特定のカラムを複製し、検索をしやすくする仕組み。  
  
**（例）Usersテーブルのnameにindexを張らない場合**  
プログラムはUsersテーブルのnameカラムを上から順に探し、該当ユーザーの情報を取得する。  
→indexを張るとソートした上で（アルファベット順？）nameを並べ複製するので、値の取得効率が上昇する。

### メリット・デメリット
#### メリット  
データの読み込み・取得の高速化  
（whereでの検索、Joinによる結合、orderによるソート等が効率的に行えるようになる）
#### デメリット
書き込みの速度低下

## LIKE句
実は、indexが効くのは、ワイルドカード（％）の前の句まで。  
つまり、前方一致の検索（「%aaa%」のような）にはインデックスが無効になり、重いクエリになる。

## テーブル結合の多用
「ネステッドテーブル結合」で外部キーから主キーを探す際、**上から順に探す**ため、レコード量が多いとクエリが重くなる。  
  
**mysql**は、「複雑なアルゴリズムはなるべくサポートしない」という設計思想で作られているため、**テーブル結合**を得意としない。  
→「ネステッドテーブル結合」を多用する際は、PostgreSQLの方が良かったりする。

## 大量のソートの発生
ソートをかける行為は**データをメモリ常に展開し並べ替える行為**。  
インデックスを張っていると、既にソートして保存されているので、処理が早い。

## データ量が増えると処理が重たい？
開発環境の時点で**本番環境を意識した**データ量を格納し、開発すると未然にトラブルを防げる。


