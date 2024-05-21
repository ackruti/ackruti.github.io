---
title: 'SQL Case: The Swiss Army Knife'
date: 2024-05-20
draft: true
authors:
  - name: Akruti Ambade
    link: https://github.com/ackruti
    image: https://github.com/ackruti.png
summary: Unlocking the power and versatility of SQL CASE expression and achieving the Ninja 🥷 status.
tags:
  - sql
  - tutorial
---

Ever felt like you needed a Swiss Army knife for your SQL adventures? Well, let me introduce you to the `CASE` expression. This multifaceted tool has been traditionally used in the `SELECT` expression mostly, but its true potential and versatility can be unlocked beyond that. In this blog, we're re-discovering the quirky ways you can fully utilize the `CASE` expression in SQL right from the Beginner and to the Pro levels.

## Getting Started
We will be using MySQL throughout this tutorial, so before we deep dive further let's create some sample dataset in MySQL to run our queries against.

{{< callout type="info" >}}
  If you do not have MySQL installed, you can utilize an online service like [dbfiddle.uk](https://dbfiddle.uk/) to run the queries on a web browser.
{{< /callout >}}

```sql
-- Create and insert into sales table
CREATE TABLE IF NOT EXISTS sales (
    id INT,
    product VARCHAR(20),
    color VARCHAR(10),
    status_id INT,
    units INT
);

INSERT INTO sales VALUES
    (1, 'iPhone 15 Pro Max', 'Black', 1, 10),
    (2, 'iPhone 15 Pro', 'Black', 1, 20),
    (3, 'iPhone 15', 'Gray', 2, 15),
    (4, 'MacBook Air 13', 'Black', 3, 25),
    (5, 'MacBook Air 15', 'Gray', 4, 5),
    (6, 'MacBook Air 13', 'Black', NULL, 15);

-- Create and insert into names table
CREATE TABLE IF NOT EXISTS names (
    fname VARCHAR(20),
    lname VARCHAR(20)
);

INSERT INTO names VALUES
    ('John','Smith'),
    ('James','Lee'),
    (NULL,'Sam Miller');

-- Create and insert into ages table
CREATE TABLE IF NOT EXISTS ages (
    fname VARCHAR(20),
    lname VARCHAR(20),
    age INT
);

INSERT INTO ages VALUES
    ('John','Smith',30),
    ('James','Lee',35),
    ('Sam','Miller',40);
```
Here is a sample of how our table will look like.

`sales`
| id | product           | color | status_id | units |
|----|-------------------|-------|-----------|-------|
| 1  | iPhone 15 Pro Max | Black | 1         | 10    |
| 2  | iPhone 15 Pro     | Black | 1         | 20    |
| 3  | iPhone 15         | Gray  | 2         | 15    |
| 4  | MacBook Air 13    | Black | 3         | 25    |
| 5  | MacBook Air 15    | Gray  | 4         | 5     |
| 6  | MacBook Air 13    | Black | NULL      | 15    |

## CASE Syntax
If you are familiar with the `IF` function in Excel or the `for` loop in Python, then you already know how the `CASE` expression works. To keep it simple, the `CASE` expression evaluates column values based on specified conditions and outputs a specific result. If the conditions do not match any column value, a default result can be specified.

In the following query, we are evaluating the values in the `col` column with three conditions. The CASE expression will,
- Evaluate the conditions starting from the top and output the corresponding result if the condition is true in the `result_col`.
- If there is no true condition, it evaluates to the `ELSE` part.
- If there is no true condition and no `ELSE` part written, it outputs the result as `NULL`.

```sql
SELECT
    col,
    CASE
        WHEN col = value1 THEN result1
        WHEN col = value2 THEN result2
        WHEN col = value3 THEN result3
        ELSE result4
    END result_col
FROM tab;
```

We are not restricted to evaluating just one condition on one column at a time, but can run as many conditions on as many columns to generate a resulting output using a combination of logical operators.

```sql
SELECT
    col1,
    col2,
    CASE
        WHEN col1 = value1                      THEN result1
        WHEN col2 = value2                      THEN result2
        WHEN col1 = value3 AND col2 = value4    THEN result3
        WHEN col1 = value5 OR  col2 = value6    THEN result4
        ELSE result5
    END
FROM tab;
```

{{< callout type="warning" >}}
__Important Note__  
The `CASE` expression evaluates to the first true condition for a row. Once the first true condition has been met, it will proceed to the next row and will skip evaluating the rest of the conditions. Hence, it is quite important to arrange the conditions in the right order from the top.
{{< /callout >}}

## CASE in SELECT
Traditionally, we all have used `CASE` expression in the `SELECT` statement. So, let's run some queries against our sample dataset to warm-up with it. In the following exercise, we are writing two `CASE` expressions to,
- Group the Products into higher-level Categories.
- Translate the Status IDs to more reader-friendly Status descriptions.

```sql
SELECT
    id,
    CASE
        WHEN product LIKE 'iPhone%' THEN 'Phone'
        WHEN product LIKE 'MacBook%' THEN 'Laptop'
    END category,
    product,
    color,
    status_id,
    CASE
        WHEN status_id = 1 THEN 'Pending'
        WHEN status_id = 2 THEN 'Processing'
        WHEN status_id = 3 THEN 'Rejected'
        WHEN status_id = 4 THEN 'Completed'
        ELSE 'Error'
    END status_desc,
    units
FROM sales;
```

| id | category | product           | color | status_id | status_desc | units |
|----|----------|-------------------|-------|-----------|-------------|-------|
| 1  | Phone    | iPhone 15 Pro Max | Black | 1         | Pending     | 10    |
| 2  | Phone    | iPhone 15 Pro     | Black | 1         | Pending     | 20    |
| 3  | Phone    | iPhone 15         | Gray  | 2         | Processing  | 15    |
| 4  | Laptop   | MacBook Air 13    | Black | 3         | Rejected    | 25    |
| 5  | Laptop   | MacBook Air 15    | Gray  | 4         | Completed   | 5     |
| 6  | Laptop   | MacBook Air 13    | Black | NULL      | Error       | 15    |

With that, we have improved our data with the `category` and the `status_desc` columns and made it more reader-friendly, as compared to the way it was previously.

### Nested CASE
If you thought we are limited to only single-dimension of the `CASE` expression, then think again. We have the freedom to go into deeper levels with the `CASE` expression by simply using `NESTING`, aka, `CASE` within a `CASE`.

In the following example, we are creating two `status_desc` columns with the same results, but with the following differences
- `status_desc_1`: Using a `CASE` expression to evaluate multiple conditions.
- `status_desc_2`: Using a Nested `CASE` expression to evaluate multiple conditions.

```sql
SELECT
    id,
    CASE
        WHEN product LIKE 'iPhone%' THEN 'Phone'
        WHEN product LIKE 'MacBook%' THEN 'Laptop'
    END category,
    product,
    color,
    status_id,
    -- CASE with multiple conditionals
    CASE
        WHEN status_id = 1 AND units > 15 THEN 'Pending - Bulk Order'
        WHEN status_id = 1 AND units < 15 THEN 'Pending - Small Order'
        WHEN status_id = 1 THEN 'Pending'
        WHEN status_id = 2 THEN 'Processing'
        WHEN status_id = 3 THEN 'Rejected'
        WHEN status_id = 4 THEN 'Completed'
        ELSE 'Error'
    END status_desc_1,

    -- Nested CASE
    CASE
        WHEN status_id = 1 THEN (
            CASE
                WHEN units > 15 THEN 'Pending - Bulk Order'
                WHEN units < 15 THEN 'Pending - Small Order'
                ELSE 'Pending'
            END
        )
        WHEN status_id = 2 THEN 'Processing'
        WHEN status_id = 3 THEN 'Rejected'
        WHEN status_id = 4 THEN 'Completed'
        ELSE 'Error'
    END status_desc_2,
    units
FROM sales;
```

| id | category | product           | color | status_id | status_desc_1         | status_desc_2         | units |
|----|----------|-------------------|-------|-----------|-----------------------|-----------------------|-------|
| 1  | Phone    | iPhone 15 Pro Max | Black | 1         | Pending - Small Order | Pending - Small Order | 10    |
| 2  | Phone    | iPhone 15 Pro     | Black | 1         | Pending - Bulk Order  | Pending - Bulk Order  | 20    |
| 3  | Phone    | iPhone 15         | Gray  | 2         | Processing            | Processing            | 15    |
| 4  | Laptop   | MacBook Air 13    | Black | 3         | Rejected              | Rejected              | 25    |
| 5  | Laptop   | MacBook Air 15    | Gray  | 4         | Completed             | Completed             | 5     |
| 6  | Laptop   | MacBook Air 13    | Black | NULL      | Error                 | Error                 | 15    |

{{< callout type="info" >}}
Although we are achieving the same result with two different `CASE` expressions, the decision about which one to use depends upon various factors like code readability, complexity, etc.
{{< /callout >}}

### CASE as an alternative
In the following query, we are converting the numerical price column to a more reader-friendly format with commas. This can be easily achieved using the in-built `FORMAT()` function in MySQL. But, if we have to achieve the same result without the use of a function, we can use a `CASE` expression to achieve the same result.

```sql
WITH data AS (
    SELECT 1000         AS amount UNION
    SELECT 10000        AS amount UNION
    SELECT 100000       AS amount UNION
    SELECT 1000000      AS amount UNION
    SELECT 10000000     AS amount UNION
    SELECT 100000000    AS amount
)

SELECT
    amount,
    CONCAT('$', FORMAT(amount, 0)) formatted_amount_1,
    CASE
        WHEN LENGTH(amount) <= 3 THEN CONCAT('$', CONVERT(amount,CHAR(10)))
        WHEN LENGTH(amount) > 3 AND LENGTH(amount) <= 6
            THEN CONCAT('$', SUBSTRING(amount, 1, LENGTH(amount) - 3), ',', SUBSTRING(amount, -3))
        WHEN LENGTH(amount) > 6 AND LENGTH(amount) <= 9
            THEN CONCAT('$', SUBSTRING(amount, 1, LENGTH(amount) - 6), ',', SUBSTRING(amount, LENGTH(amount) - 5, 3), ',', SUBSTRING(amount, -3))
    END AS formatted_amount_2
FROM data;
```

| amount    | formatted_amount_1 | formatted_amount_2 |
|-----------|--------------------|--------------------|
| 1000      | $1,000             | $1,000             |
| 10000     | $10,000            | $10,000            |
| 100000    | $100,000           | $100,000           |
| 1000000   | $1,000,000         | $1,000,000         |
| 10000000  | $10,000,000        | $10,000,000        |
| 100000000 | $100,000,000       | $100,000,000       |

## CASE in JOIN
Now that you have had a good hands-on on the basic `CASE` expression in the `SELECT` statement, let's jump into some intermediate difficulty territory. Let's consider the following two tables that we created earlier. We are assuming that the data has not been normalized properly in the `names` table, and we have to join them together to get all the records in the `ages` table.

`names`
| fname | lname      |
|-------|------------|
| John  | Smith      |
| James | Lee        |
| NULL  | Sam Miller |

`ages`
| fname | lname  | age |
|-------|--------|-----|
| John  | Smith  | 30  |
| James | Lee    | 35  |
| Sam   | Miller | 40  |

In our case the following simple `JOIN` condition will not be able to join all the values as a result of the misplaced data entry in the third row of the `names` table.

```sql
SELECT ages.*
FROM ages
INNER JOIN names
    ON ages.fname = names.fname
    AND ages.lname = names.lname;
```

| fname | lname | age |
|-------|-------|-----|
| John  | Smith | 30  |
| James | Lee   | 35  |

Well, how about using a `CASE` expression to save the day? The `CASE` expression gives us the flexibility to go around the erroneous record and make the `JOIN` work again.

```sql
SELECT ages.*
FROM ages
INNER JOIN names
    ON CONCAT_WS(' ', ages.fname, ages.lname)
        = (CASE
              WHEN names.fname IS NULL THEN names.lname
              ELSE CONCAT_WS(' ',names.fname, names.lname)
           END);
```

| fname | lname  | age |
|-------|--------|-----|
| John  | Smith  | 30  |
| James | Lee    | 35  |
| Sam   | Miller | 40  |

## CASE in WHERE
In our previous example of using the `SELECT` statement, we explicitly created a `status_desc` column to convert the `status_id` into a reader-friendly status description. If we want to filter that data on a specific status description, we can easily use the `status_desc` column to do that.

But, what if we want to avoid creating a column to just filter based on a status description? In that case, we can use the `CASE` statement in the `WHERE` clause to filter accordingly.

```sql
SELECT
    id, product, color, status_id, units
FROM sales
WHERE
    CASE
        WHEN status_id = 1 THEN 'Pending'
        WHEN status_id = 2 THEN 'Processing'
        WHEN status_id = 3 THEN 'Rejected'
        WHEN status_id = 4 THEN 'Completed'
    END = 'Pending';
```

| id | product           | color | units | status_id |
|----|-------------------|-------|-------|-----------|
| 1  | iPhone 15 Pro Max | Black | 10    | 1         |
| 2  | iPhone 15 Pro     | Black | 20    | 1         |

The above example shows an example which might not necessarily require a `CASE` expression, but when it comes to complex filtering a `CASE` expression will be the better option.

In the following example, we are evaluating a bunch of conditions together. We can use multiple conditions as shown below, but we are definitely making it hard for the reader to understand what we are trying to achieve.

```sql
SELECT product, color
FROM sales
WHERE NOT (
    product IN ('iPhone 15', 'iPhone 15 Pro') AND
    color = 'Black' AND
    (status_id IS NULL OR status_id != 2)
);
```

| product           | color |
|-------------------|-------|
| iPhone 15 Pro Max | Black |
| iPhone 15         | Gray  |
| MacBook Air 13    | Black |
| MacBook Air 15    | Gray  |
| MacBook Air 13    | Black |

However, using a `CASE` expression to substitute the above filtering is better in this case, as we are make the query reader-friendly while achieving the same result as above.

```sql
SELECT product, color
FROM sales
WHERE
    CASE
        WHEN product IN ('iPhone 15','iPhone 15 Pro') AND color = 'Black' AND status_id != 2 THEN 'Exclude'
        ELSE 'Include'
    END = 'Include'
;
```

| product           | color |
|-------------------|-------|
| iPhone 15 Pro Max | Black |
| iPhone 15         | Gray  |
| MacBook Air 13    | Black |
| MacBook Air 15    | Gray  |
| MacBook Air 13    | Black |

## CASE in ORDER BY
Traditionally, when it comes to ordering the columns using the `ORDER BY` clause, we have to rely on the columns and their values to order them in the ascending or descending order. For e.g., the `sales` table can be sorted as per two types of columns,
- the `VARCHAR` columns to sort the values as per ASCII value.
- the `INT` columns to sort the values by the numerical value.

| id | product           | color | status_id | units |
|----|-------------------|-------|-----------|-------|
| 1  | iPhone 15 Pro Max | Black | 1         | 10    |
| 2  | iPhone 15 Pro     | Black | 1         | 20    |
| 3  | iPhone 15         | Gray  | 2         | 15    |
| 4  | MacBook Air 13    | Black | 3         | 25    |
| 5  | MacBook Air 15    | Gray  | 4         | 5     |
| 6  | MacBook Air 13    | Black | NULL      | 15    |

But, what if we want a custom ordering of the above data, but by using the Dollar Amount of the product? In the absence of `amount` column in the table, we can easily resort to using the `CASE` statement to arrange the data manually by giving a numerical value to the `product` column, that does not need to show up as a separate column. 

In the above dataset, we already know that MacBook Air 15 will be the most expensive product and the iPhone 15 the least expensive, and the rest of the products can be sorted manually based on the `product` name itself. So, let's arrange that manually using a `CASE` expression.

```sql
SELECT *
FROM sales
ORDER BY
    (CASE
        WHEN product = 'MacBook Air 15'     THEN 1
        WHEN product = 'MacBook Air 13'     THEN 2
        WHEN product = 'iPhone 15 Pro Max'  THEN 3
        WHEN product = 'iPhone 15 Pro'      THEN 4
        WHEN product = 'iPhone 15'          THEN 5
    END)
;
```

| id | product           | color | status_id | units |
|----|-------------------|-------|-----------|-------|
| 5  | MacBook Air 15    | Gray  | 4         | 5     |
| 4  | MacBook Air 13    | Black | 3         | 25    |
| 6  | MacBook Air 13    | Black | NULL      | 15    |
| 1  | iPhone 15 Pro Max | Black | 1         | 10    |
| 2  | iPhone 15 Pro     | Black | 1         | 20    |
| 3  | iPhone 15         | Gray  | 2         | 15    |

## CASE in GROUP BY
Now, let's switch gears to using `CASE` in an aggregation. In the following example, we want to establish the total units sold at the product `category` level. We can accomplish this using a `CASE` expression as follows,

```sql
SELECT
    CASE
        WHEN product LIKE 'iPhone%' THEN 'Phone'
        WHEN product LIKE 'MacBook%' THEN 'Laptop'
    END category,
    SUM(units) total_units
FROM sales
GROUP BY product;
```

| category | total_units |
|----------|-------------|
| Phone    | 10          |
| Phone    | 20          |
| Phone    | 15          |
| Laptop   | 40          |
| Laptop   | 5           |

But, if we look at the above result, it seems that although the `CASE` expression we implemented is correct the data has not been grouped at a higher product category level as expected. This is due to the data being aggregated at the lower `product` level, instead of the newly created higher `category` level.

To rectify this issue, we need to make sure the `CASE` expression defined in the `SELECT` statement is also explicitly mentioned in the `GROUP BY` clause to group the data at the higher `category` level.

```sql
SELECT
    CASE
        WHEN product LIKE 'iPhone%' THEN 'Phone'
        WHEN product LIKE 'MacBook%' THEN 'Laptop'
    END category,
    SUM(units) total_units
FROM sales
GROUP BY
    CASE
        WHEN product LIKE 'iPhone%' THEN 'Phone'
        WHEN product LIKE 'MacBook%' THEN 'Laptop'
    END;
```
| category | total_units |
|----------|-------------|
| Phone    | 45          |
| Laptop   | 45          |

### CASE to Pivot
MySQL does not natively support the `PIVOT` function which are offered by Oracle SQL, Microsoft T-SQL, etc. To overcome this, we can recreate the same using the `CASE` expression.

In the following example, we are using the `status_id` dimension column and creating separate columns out of its members (`pending`, `processing`, `rejected`, etc.) while also grouping them at a higher product `category` level to get a total count of the various order `status_id` the product `category` falls under.

```sql
SELECT
    CASE
        WHEN product LIKE 'iPhone%' THEN 'Phone'
        WHEN product LIKE 'MacBook%' THEN 'Laptop'
    END category,
    SUM(CASE WHEN status_id = 1 THEN 1 ELSE 0 END) AS pending,
    SUM(CASE WHEN status_id = 2 THEN 1 ELSE 0 END) AS processing,
    SUM(CASE WHEN status_id = 3 THEN 1 ELSE 0 END) AS rejected,
    SUM(CASE WHEN status_id = 4 THEN 1 ELSE 0 END) AS completed,
    SUM(CASE WHEN status_id IS NULL THEN 1 ELSE 0 END) AS error
FROM sales
GROUP BY
    CASE
        WHEN product LIKE 'iPhone%' THEN 'Phone'
        WHEN product LIKE 'MacBook%' THEN 'Laptop'
    END;
```

| category | pending | processing | rejected | completed | error |
|----------|---------|------------|----------|-----------|-------|
| Phone    | 2       | 1          | 0        | 0         | 0     |
| Laptop   | 0       | 0          | 1        | 1         | 1     |

## CASE in DML
So far we have looked at `CASE` as part of the DQL (Data Query Language), but let's expand our horizon to the DML (Data Manipulation Language) as well.

### CASE in UPDATE
In the [CASE in SELECT](#case-in-select) section above, we used a `CASE` expression to convert the `status_id` column to reader-friendly `status_desc` column. Instead of writing a `CASE` expression every time we query the data, let's create a column in the `sales` table and update it with the `status_desc` info. This can be achieved using the following,

```sql
-- Add column
ALTER TABLE sales
ADD status_desc VARCHAR(20);

-- Update the status_desc column
UPDATE sales
SET status_desc =
    (CASE
        WHEN status_id = 1 THEN 'Pending'
        WHEN status_id = 2 THEN 'Processing'
        WHEN status_id = 3 THEN 'Rejected'
        WHEN status_id = 4 THEN 'Completed'
        ELSE 'Error'
    END)
;
```
| id | product           | color | status_id | units | status_desc |
|----|-------------------|-------|-----------|-------|-------------|
| 1  | iPhone 15 Pro Max | Black | 1         | 10    | Pending     |
| 2  | iPhone 15 Pro     | Black | 1         | 20    | Pending     |
| 3  | iPhone 15         | Gray  | 2         | 15    | Processing  |
| 4  | MacBook Air 13    | Black | 3         | 25    | Rejected    |
| 5  | MacBook Air 15    | Gray  | 4         | 5     | Completed   |
| 6  | MacBook Air 13    | Black | NULL      | 15    | Error       |

### CASE in DELETE
Similarly to updating the table, we can combine the `DELETE` and `CASE` expression to conditionally evaluate and delete specific rows from the table as follows,

```sql
DELETE FROM sales
WHERE TRUE =
    (CASE
        WHEN color = 'Black' AND product NOT LIKE 'iPhone%' THEN TRUE
        ELSE FALSE
    END)
;
```

## CASE Exercises
Let's test out our newfound knowledge of the `CASE` expression and solve the following two riddles:

__Q:__ What percentage of all tournament games did Italy win?

| country | result |
|---------|--------|
| Germany | Win    |
| Spain   | Loss   |
| Italy   | Win    |
| Germany | Loss   |
| Italy   | Loss   |

Expected result:
| italy_win_pct |
|---------------|
| 0.2000        |

{{% details title="Reveal Answer" closed="true" %}}

```sql
WITH data AS (
    SELECT 'Germany' country, 'Win' result  UNION ALL
    SELECT 'Spain', 'Loss'                  UNION ALL
    SELECT 'Italy', 'Win'                   UNION ALL
    SELECT 'Germany', 'Loss'                UNION ALL
    SELECT 'Italy', 'Loss'
)
SELECT
    SUM(CASE WHEN country = 'Italy' AND result = 'Win' THEN 1 END)/ COUNT(1) italy_win_pct1,
    AVG(CASE WHEN country = 'Italy' AND result = 'Win' THEN 1 ELSE 0 END) italy_win_pct2
FROM data;
```
| italy_win_pct1 | italy_win_pct2 |
|----------------|----------------|
| 0.2000         | 0.2000         |

With the above query we have achieved the same result, but with 2 different approaches as shown in the columns,
- `italy_win_pct1`: Using `CASE` expression with a standard numerator/denominator approach.
- `italy_win_pct1`: Using `CASE` expression but with a clever way of using the `AVERAGE` aggregation.

{{% /details %}}

__Q:__ What is the percentage of all rows where the `old_date` and `new_date` values match?

| id | old_date   | new_date   |
|----|------------|------------|
| 1  | 2023-01-01 | 2023-01-01 |
| 2  | 2023-01-01 | 2023-01-02 |
| 3  | 2023-01-01 | 2023-01-01 |

Expected result:
| date_match_pct |
|----------------|
| 0.6667         |

{{% details title="Reveal Answer" closed="true" %}}

```sql
WITH dates AS (
    SELECT 1 id,'2023-01-01' old_date,'2023-01-01' new_date UNION
    SELECT 2,'2023-01-01','2023-01-02'                      UNION
    SELECT 3,'2023-01-01','2023-01-02'
)
SELECT
    SUM(CASE WHEN old_date <> new_date THEN 1 END) / COUNT(1) date_match_pct1,
    AVG(CASE WHEN old_date <> new_date THEN 1 ELSE 0 END) date_match_pct2,
    AVG(CASE WHEN old_date <> new_date THEN 1 END) date_match_pct3
FROM dates;
```

| date_match_pct1 | date_match_pct2 | date_match_pct3 |
|-----------------|-----------------|-----------------|
| 0.6667          | 0.6667          | 1.0000          |

The first two columns are the correct results using 2 different approaches. The third column gives the wrong result because we excluded the crucial `ELSE` portion from the `CASE` expression. Always make sure you are including the ELSE portion as well, in this case.
{{% /details %}}

## Summary
If you have gone through all the above examples and have been to grasp the magnificent and multipurpose `CASE` expression firmly, I will grant you the coveted status of the `CASE` Ninja 🥷 and wish you luck on your future SQL adventures.