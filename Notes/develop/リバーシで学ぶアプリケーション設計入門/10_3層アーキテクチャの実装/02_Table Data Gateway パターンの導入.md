## Table Data Gateway パターン

### データアクセス層の実装には以下のようなパターンがある

- Table Data Gateway 
- Repository
- Active Record

### Table Data Gateway パターン

テーブルと1対1対応するクラスを作成し、対象のテーブルとのやりとりを記述するTable Data Gatewayパターンを実装する。
Table Data Gateway パターンはJava界隈では「DAO（Data Access Object）パターン」と呼ばれることがある。
