SQL文法まとめ
======
SQL文とはDBMS（データベース管理システム）に指示する命令文のこと

CRUD処理
=
- C…CREATE：登録
- R…READ：表示
- U…UPDATE:更新
- D…DELETE:削除

CREATE文
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

READ文
=
例１「顧客リスト」からすべてのデータを読み出す
```
SELECT * FROM 顧客リスト;
```






