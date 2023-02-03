# Part2: SQL 代码实现

## 题目一 ：

请输出在 ‘2022 10 05’ 至‘2022 11 05’ 时间段内有效订单（订单状态为完成）的

- 1）平均客单价 (平均每单花费)

  ```

  SELECT AVG(total_payment / COUNT(DISTINCT order_id)) as average_order_payment,

         AVG(total_payment / COUNT(DISTINCT member_id)) as average_customer_payment

  FROM order

  WHERE order_status = 'Finished'

    AND trans_date BETWEEN '2022-10-05' AND '2022-11-05'

  ```
- 2）单件比 (平均每单购买商品件数，赠品不计入计算)

  ```

  SELECT AVG(product_qty / COUNT(DISTINCT order_id)) as average_item_per_order

  FROM order_product

  JOIN order ON order_product.order_id = order.order_id

  WHERE order_product.product_type != 'gift'

    AND order_status = 'Finished'

    AND trans_date BETWEEN '2022-10-05' AND '2022-11-05'

  ```
- 3）平均下单频次 (平均每人下几单)

  ```

  SELECT AVG(COUNT(DISTINCT order_id) / COUNT(DISTINCT member_id)) as average_order_frequency

  FROM order

  WHERE order_status = 'Finished'

    AND trans_date BETWEEN '2022-10-05' AND '2022-11-05'

  ```
- 4）新客占比
- ```

  SELECT (COUNT(DISTINCT order.member_id)

           / COUNT(DISTINCT CASE WHEN order.is_member_new = 'Y' THEN order.member_id END)) as new_customer_ratio

  FROM order

  WHERE order_status = 'Finished'

    AND trans_date BETWEEN '2022-10-05' AND '2022-11-05'

  ```

## **题目二：**

- 请输出会员的周复购率（上周存在购买且在本周也存在购买的）

  ```

  WITH WeeklyPurchase AS (

    SELECT

      member_id,

      DATE_TRUNC('week', trans_date) AS week

    FROM

      order

    WHERE

      order_status = 'Finished'

    GROUP BY

      member_id, week

  )

  SELECT

    member_id,

    week,

    SUM(CASE WHEN EXISTS (

      SELECT 1

      FROM CompletedOrders AS prev

  ```

## **题目三：**

- 输出购买产品001 (product_id = '001') 的
- 1）会员男女比例

  ```

  SELECT AVG(CASE WHEN gender = 'male' THEN 1 ELSE 0 END) as male_ratio,

         AVG(CASE WHEN gender = 'female' THEN 1 ELSE 0 END) as female_ratio

  FROM 

      order_product op 

      JOIN user_master um ON op.member_id = um.member_id 

  WHERE 

      op.product_id = '001' 

  GROUP BY 

      gender 

  ```

-`HERE member_id IN ( SELECT member_id FROM order_product WHERE product_id = '001' )`

- 2）年龄分布(分为(0,20] (20,30] (30.40] (40,50] 这五档)
- ```

  SELECT 

    CASE 

      WHEN datediff(year, date_of_birth, getdate()) BETWEEN 0 AND 20 THEN '(0,20]'

      WHEN datediff(year, date_of_birth, getdate()) BETWEEN 20 AND 30 THEN '(20,30]'

      WHEN datediff(year, date_of_birth, getdate()) BETWEEN 30 AND 40 THEN '(30,40]'

      WHEN datediff(year, date_of_birth, getdate()) BETWEEN 40 AND 50 THEN '(40,50]'

    END AS age_range,

    COUNT(*) AS num_of_customers

  FROM user_master

  INNER JOIN order_product ON user_master.member_id = order_product.member_id

  WHERE order_product.product_id = '001'

  GROUP BY 

    CASE 

      WHEN datediff(year, date_of_birth, getdate()) BETWEEN 0 AND 20 THEN '(0,20]'

      WHEN datediff(year, date_of_birth, getdate()) BETWEEN 20 AND 30 THEN '(20,30]'

      WHEN datediff(year, date_of_birth, getdate()) BETWEEN 30 AND 40 THEN '(30,40]'

      WHEN datediff(year, date_of_birth, getdate()) BETWEEN 40 AND 50 THEN '(40,50]'

    END


  ```
