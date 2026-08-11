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
【ACID特性】
トランザクション処理では、以下の 「All or Nothing（すべて実行されるか、まったく実行されないか）」 の原則を守ることが最重要となります。
- COMMIT（コミット）：すべての処理が正常に完了したため、変更をデータベースへ永久に保存する。
- ROLLBACK（ロールバック）：途中で失敗したため、変更を破棄して開始前の状態に復元する。

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

DISTINCT
=
- 指定した列の中で重複する値をひとつにまとめて表示する。
```
-- 商品テーブルで使われている「カテゴリIDの一覧」を重複なしで取得
SELECT DISTINCT category_id 
FROM products;
```

LIKE句
=
ワイルドカードを使って指定したパターンに一位する文字列を検索する。
- %　０文字以上の任意の文字列
- _　任意の一文字

```
-- 商品名に「ペン」が含まれる商品を検索（'ボールペン', 'シャープペンシル' など）
SELECT * 
FROM products 
WHERE product_name LIKE '%ペン%';
```
BETWEEN句
＝
値1 BETWEEN A AND B で「A以上B以下」の範囲条件を指定します（値 >= A AND 値 <= B と同じ意味です）。
```
-- 価格が 1,000円 以上 10,000円 以下 の商品を検索
SELECT * 
FROM products 
WHERE price BETWEEN 1000 AND 10000;
```
AVG関数
=
指定した列の数値の平均値を計算。
```
-- 全商品の「平均価格」を計算する
SELECT AVG(price) AS 平均価格 
FROM products;

-- 応用：GROUP BY と組み合わせてカテゴリごとの平均価格を出す
SELECT category_id, AVG(price) AS カテゴリ別平均価格
FROM products
GROUP BY category_id;
```
ORDER BY
=
検索結果を指定した列の値で並び替える。
- ASC:昇順（小さい順 / A→Z / 古い順）
- DESC:降順（大きい順 / Z→A / 新しい順）
```
-- 価格が高い順（降順）に並び替える
SELECT * 
FROM products 
ORDER BY price DESC;
```
LIMIT句
=
検索結果から表示する最大件数を制限します
```
-- 価格が高い順に並び替え、トップ3件だけを取得する
SELECT * 
FROM products 
ORDER BY price DESC 
LIMIT 3;
```
INNER JOIN(内部結合)
=
```
-- 商品情報に「カテゴリ名」を結合して表示する
SELECT 
    p.product_id,
    p.product_name,
    p.price,
    c.category_name
FROM products p
INNER JOIN categories c ON p.category_id = c.category_id;
```
LEFT JOIN(外部結合)
=
先ほど登場した INNER JOIN（両方に存在するデータのみ結合）に対し、LEFT JOIN は主となる左側のテーブルを全件残し、対応するデータが右側になければ NULL として表示します。「一度も購入していない顧客も含めて一覧を出したい」ときなどに使います。
```
SELECT 
    c.customer_name,
    o.order_id
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id;
```

OUTER JOIN（FORMAT）
=

TO_CHAR
=
日付や数値などを形式化し、後から見返しやすくする。
```
SELECT 
    -- 日付を '2026/08/11 (火)' のように整形
    TO_CHAR(created_at, 'YYYY/MM/DD (Dy)') AS 注文日,
    
    -- 数値をカンマ区切り＋円マーク表示（例: ￥1,200,000）
    TO_CHAR(price, 'FML999,999,999') AS 価格表示
FROM orders;
```

CASE文
=
プログラミング言語のif-elseにあたる機能。「値によって表示するラベルを変えたい」「条件別に分類・集計したい」ときに必須となる。
```
SELECT 
    user_name,
    age,
    CASE 
        WHEN age < 20 THEN '未成年'
        WHEN age >= 65 THEN 'シニア'
        ELSE '一般'
    END AS 区分
FROM users;
```
EXISTS/NOT EXISTS(存在チェック)
=
IN 句と似ているが、大量のデータを扱う実務では EXISTS の方が処理速度が圧倒的に速いため重宝される。「特定の条件に当てはまるデータが別テーブルに存在するか（しないか）」を判定する。
```
-- 一度でも注文（orders）をしたことがある顧客（customers）だけを抽出
SELECT * 
FROM customers c
WHERE EXISTS (
    SELECT 1 
    FROM orders o 
    WHERE o.customer_id = c.customer_id
);
```
CTE
=
複雑で長いSQLを書く際、サブクエリ（一時的な検索結果）に名前をつけて整理できる構文です。コードの可読性が格段に上がり、バグの防止やレビューのしやすさに繋がります。

```
-- 高額注文（10万円以上）データだけを一時的に定義
WITH HighValueOrders AS (
    SELECT customer_id, total_amount
    FROM orders
    WHERE total_amount >= 100000
)
-- 上記で定義した一時テーブルを使ってメインの検索を実行
SELECT c.customer_name, h.total_amount
FROM HighValueOrders h
JOIN customers c ON h.customer_id = c.customer_id;
```
ウィドウ関数（ROW_NUMBER/RANK）
=
データをグループ化しつつ、行を減らさずに連番や順位を付ける機能です。「顧客ごとに一番最新の注文データだけを取りたい」という実務あるあるの処理が簡単に書けます。
```
SELECT 
    customer_id,
    order_date,
    total_amount,
    -- 顧客（customer_id）ごとに注文日（order_date）が新しい順で1, 2, 3... と連番を振る
    ROW_NUMBER() OVER (
        PARTITION BY customer_id 
        ORDER BY order_date DESC
    ) AS row_num
FROM orders;
```
GROUP BY+HAVING
=
グループ化した後の条件絞り込み

```
-- 注文合計金額が「10万円を超えている顧客」だけをグループ化後に抽出
SELECT customer_id, SUM(total_amount) AS 合計金額
FROM orders
GROUP BY customer_id
HAVING SUM(total_amount) > 100000;
```
