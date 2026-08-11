SQL文法まとめ
======
SQL文とはDBMS（データベース管理システム）に指示する命令文のこと

CRUD処理
=
- C…CREATE：登録
- R…READ：表示
- U…UPDATE:更新
- D…DELETE:削除

INSERT文
=
例１顧客リストテーブルにデータを挿入する。
```
INSERT INTO 顧客リスト (顧客ID, 氏名, メールアドレス, 年齢)
VALUES (1, '山田太郎', 'yamada@example.com', 30);
```
例２複数のデータをまとめて追加する。
```
INSERT INTO 顧客リスト (顧客ID, 氏名, メールアドレス, 年齢)
VALUES 
    (2, '鈴木花子', 'suzuki@example.com', 25),
    (3, '佐藤健', 'sato@example.com', 42),
    (4, '高橋由美', 'takahashi@example.com', 19);
```
例３一部のカラムにだけデータを追加する
```
-- メールアドレスと年齢を省略して挿入
INSERT INTO 顧客リスト (顧客ID, 氏名)
VALUES (5, '田中一郎');
```

SELECT文
=
例１「顧客リスト」からすべてのデータを読み出す
```
SELECT * FROM 顧客リスト;
```
例２条件を指定してデータを読み出す（年齢が30歳以上の顧客）
```
SELECT 顧客ID, 氏名, メールアドレス 
FROM 顧客リスト 
WHERE 年齢 >= 30;
```

UPDATE文
=
例１顧客IDが「1」の人の住所と年齢を更新する
```
UPDATE 顧客リスト
SET 住所 = '東京都新宿区', 年齢 = 31
WHERE 顧客ID = 1;
```
例２全商品の価格を10%引きにする
```
UPDATE 商品リスト
SET 価格 = 価格 * 0.9;
```

例３特定の部署（部署ID: 10）の社員の給与を10,000円アップする
```
UPDATE 社員リスト
SET 給与 = 給与 + 10000
WHERE 部署ID = 10;
```
DELETE文
=
例１-- 顧客IDが「5」のデータを削除する
```
DELETE FROM 顧客リスト
WHERE 顧客ID = 5;
```
例２最終ログイン日時が1年以上前のデータを削除する
```
DELETE FROM ユーザーリスト
WHERE 最終ログイン日時 < '2025-01-01';
```
例３ステータスが「退会済み」のデータを削除する
```
DELETE FROM 顧客リスト
WHERE ステータス = '退会済み';
```
例４一時データ用テーブルのレコードをすべて全削除する
<br>WHERE句を入れないと全データ削除されるので注意！
```
DELETE FROM 一時データ;
```
<br>安全にDELETE文を実行するコツ
- 実行前にSELECTで対象を確認する。
- トランザクションを使用する（もとに戻さるようにする）

トランザクション
=
```
-- 1. トランザクションの開始
BEGIN TRANSACTION;  -- ※DBMSによっては 'START TRANSACTION;' や 'BEGIN;'

-- 2. 処理①：Aさんの口座から10,000円を引く
UPDATE accounts
SET balance = balance - 10000
WHERE account_id = 'A';

-- 3. 処理②：Bさんの口座に10,000円を加算する
UPDATE accounts
SET balance = balance + 10000
WHERE account_id = 'B';

-- 4. 処理の確定（すべての処理が成功した場合）
COMMIT;
```
【ACID特性】
トランザクション処理では、以下の 「All or Nothing（すべて実行されるか、まったく実行されないか）」 の原則を守ることが最重要となります。
- COMMIT（コミット）：すべての処理が正常に完了したため、変更をデータベースへ永久に保存する。
- ROLLBACK（ロールバック）：途中で失敗したため、変更を破棄して開始前の状態に復元する。
NULL値
=
