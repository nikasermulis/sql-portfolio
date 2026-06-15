Проект по SQL: анализ продаж

Данные
Файл: `train.csv` (датасет Superstore) https://www.kaggle.com/datasets/rohitsahoo/sales-forecasting?resource=download
Количество строк:9800

Что я сделала:
Написала SQL-запросы для ответа на бизнес-вопросы: топ товаров по выручке, динамика продаж по месяцам.
Использовала ключевые навыки: `JOIN`, `GROUP BY`, оконные функции (`LAG`), агрегацию (`SUM`, `COUNT`).
Оформила отчёт с запросами и результатами.

 Инструменты
SQL (синтаксис PostgreSQL)
PondPilot (онлайн-редактор)

-- 1. Общая выручка по категориям
SELECT "Category", 
       ROUND(SUM("Sales"), 2) AS total_sales
FROM train
GROUP BY "Category"
ORDER BY total_sales DESC;

-- 2. Топ-10 товаров по выручке
SELECT "Product Name", 
       ROUND(SUM("Sales"), 2) AS total_sales
FROM train
GROUP BY "Product Name"
ORDER BY total_sales DESC
LIMIT 10;

-- 3. Динамика продаж по месяцам 
SELECT DATE_TRUNC('month', "Order Date") AS month,
       COUNT(DISTINCT "Order ID") AS number_of_orders,
       ROUND(SUM("Sales"), 2) AS monthly_sales
FROM train
GROUP BY month
ORDER BY month;

-- 4. Сравнение продаж с предыдущим месяцем (оконная функция LAG)
WITH monthly AS (
    SELECT DATE_TRUNC('month', "Order Date") AS month,
           ROUND(SUM("Sales"), 2) AS sales
    FROM train
    GROUP BY month
)
SELECT month,
       sales,
       LAG(sales, 1) OVER (ORDER BY month) AS prev_month_sales,
       ROUND((sales - LAG(sales, 1) OVER (ORDER BY month)) / 
              NULLIF(LAG(sales, 1) OVER (ORDER BY month), 0) * 100, 2) AS growth_pct
FROM monthly
ORDER BY month;

-- 5. Топ-5 клиентов по выручке 
SELECT "Customer Name", 
       ROUND(SUM("Sales"), 2) AS total_sales
FROM train
GROUP BY "Customer Name"
ORDER BY total_sales DESC
LIMIT 5;

-- 6. Количество заказов по категориям
SELECT "Category", 
       COUNT(DISTINCT "Order ID") AS num_orders
FROM train
GROUP BY "Category"
ORDER BY num_orders DESC;


Подробные запросы и результаты смотрите в файле `1`, `2`.

---
Ника
