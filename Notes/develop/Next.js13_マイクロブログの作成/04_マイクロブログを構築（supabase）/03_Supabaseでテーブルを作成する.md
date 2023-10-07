## TableEditorでテーブルを作成する

https://supabase.com/dashboard/project/zazuxvafmvugpqzgwpxh/editor

Create a new table ボタンを押下

- テーブル名：posts
- カラム：
	 - id: text, Primary
	 - title: text
	 - content: text 
	 - created_at: timestamp

他はデフォルト設定で Save ボタンでテーブルを作成する。

## RLSを設定する

RLSポリシーがデフォルトで有効になっている。そのためポリシー設定を追加する。
認証機能は特に設けていないので、全ロールで有効なポリシーを追加する。

参考：https://qiita.com/kaho_eng/items/6f9ac01d77ab198881f4

### 記事一覧を取得する

Policy name: 記事一覧を取得する
Target roles: Defaults to all (public) roles if none selected
USING expression: true

```sql
CREATE POLICY "記事一覧を取得する" ON "public"."posts"
AS PERMISSIVE FOR SELECT
TO public

WITH CHECK (true)
```

### 記事を作成する

Policy name: 記事を作成する
Target roles: Defaults to all (public) roles if none selected
USING expression: true

```sql
CREATE POLICY "記事を作成する" ON "public"."posts"
AS PERMISSIVE FOR INSERT
TO public

WITH CHECK (true)
```

### 記事を削除する

Policy name: 記事を削除する
Target roles: Defaults to all (public) roles if none selected
USING expression: true

```sql
CREATE POLICY "記事を削除する" ON "public"."posts"
AS PERMISSIVE FOR DELETE
TO public
USING (true)
```
