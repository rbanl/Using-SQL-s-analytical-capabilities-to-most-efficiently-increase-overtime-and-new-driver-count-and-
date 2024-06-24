# Using-SQL-s-analytical-capabilities-to-most-efficiently-increase-overtime-and-new-driver-count-and-
Using SQL, I analysed this business' data to understand which bonus options (to get drivers to work overtime ) were the most cost efficient for the business. I also calculated how much more money drivers would make partnering with the business and which cars saved the most money for drivers.

Please see code below:
CREATE TABLE dataset_2_converted AS
SELECT *,
       CAST(REPLACE("Accept Rate", '%', '') AS FLOAT) AS "Accept Rate Float"
FROM dataset_2;

CREATE TABLE first_option AS
SELECT *
FROM dataset_2_converted
WHERE "Supply Hours" >= 8
  AND "Trips Completed" >= 10
  AND "Accept Rate Float" >= 90
  AND "Rating" >= 4.7;

SELECT 4 * SUM("Trips Completed") AS second_option_total_payout
FROM second_option;

CREATE TABLE merged_options AS
SELECT first_option.*,
       CASE
           WHEN second_option."Trips Completed" IS NULL THEN 'left_only'
           ELSE 'both'
       END AS merge_indicator
FROM first_option
LEFT JOIN second_option
ON first_option."column1" = second_option."column1";

SELECT *
FROM merged_options
WHERE merge_indicator = 'left_only';

DECLARE @gas_per_week FLOAT = <your_value>;
DECLARE @total_weeks_per_year INT = <your_value>;
DECLARE @weeks_off INT = <your_value>;
DECLARE @insurance_per_month FLOAT = <your_value>;
DECLARE @total_months_per_year INT = <your_value>;
DECLARE @total_revenue FLOAT = <your_value>;
DECLARE @profit_margin FLOAT = <your_value>;

SET @gas_per_week = @gas_per_week * 1.05;
SET @insurance_per_month = @insurance_per_month * 0.8;
DECLARE @new_car_cost FLOAT = 40000;

DECLARE @new_total_expenses FLOAT;
SET @new_total_expenses = @new_car_cost + @gas_per_week * (@total_weeks_per_year - @weeks_off) + @insurance_per_month * @total_months_per_year;

PRINT '$' + CAST(@new_total_expenses AS VARCHAR);

DECLARE @new_profit_margin FLOAT;
SET @new_profit_margin = (@total_revenue - @new_total_expenses) / @total_revenue;

PRINT CAST(ROUND(@new_profit_margin * 100, 2) AS VARCHAR) + '%';

DECLARE @fare_increase FLOAT;
SET @fare_increase = @total_revenue * (@profit_margin - @new_profit_margin) / (@total_weeks_per_year - @weeks_off);

PRINT '$' + CAST(ROUND(@fare_increase, 2) AS VARCHAR);

