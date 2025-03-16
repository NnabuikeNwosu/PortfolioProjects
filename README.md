# EDA-with-SQL
In this Project we explored Car Sales Data with SQL
skill used: window functions, CTE, Aggregate Functions, creating Tables etc.
*/

select*
from car_sales1;

select `Date`,
str_to_date(`Date`,'%d/%m/%Y')
from car_sales1;

update car_sales1
set `date` = str_to_date(`Date`,'%d/%m/%Y');

alter table car_sales1
modify column `Date` DATE;

SELECT*
FROM car_sales1;

create table car_sales2
like car_sales1;
insert car_sales2
select*
from car_sales1;

select*
from car_sales2;



----- CHECK FOR DUPLICATES----
select*,
row_number() over (partition by Car_id, 'date',Customer_Name,Gender,Annual_Income,Dealer_Name,Company,Model,'Engine',
Transmission,Color,Price,Dealer_No,Body_Style,Phone,Dealer_Region ) as row_num

from car_sales2;

with duplicate_cte as
(
select*,
row_number() over (partition by Car_id, 'date',Customer_Name,Gender,Annual_Income,Dealer_Name,Company,Model,'Engine',
Transmission,Color,Price,Dealer_No,Body_Style,Phone,Dealer_Region ) as row_num

from car_sales2
)
select* 
from duplicate_cte
where row_num > 1;

select*
from car_sales2;

select Annual_Income,
replace (replace (Gender,'Man','Male'),'Woman','female') as sex
from car_sales2;

update car_sales2
set `Gender` = replace (replace (Gender,'Man','Male'),'Woman','female');

alter table car_sales2
rename column Gender to sex;

alter table car_sales2
rename column Price to Amount;

------ TOP COMPANY BY YEAR ----
select Company,Dealer_Region, sum(Amount)
from car_sales2
group by Company, Dealer_Region
order by 2 desc;

select Company, (`date`), count(Amount)
from car_sales2
group by Company, year(`date`)
order by year(`date`) desc;

with Company_Year (Company, years,Amount) as
(
select Company, year(`date`), count(Amount)
from car_sales2
group by Company, year(`date`)
), Company_Year_Rank as
(
select*,
 dense_rank() over(partition by years order by amount desc ) as ranking
from Company_Year
)
select*
from Company_Year_Rank
where ranking <= 5;
--- TOP MODEL SOLD BY YEAR---

select year(`date`), Model, Company, count(Amount) 
from car_sales2
group by Model, Company,year(`date`)
order by 2 desc;

with Top_Model_Sold (Years, Model, Company, Amount) as
(
select year(`date`), Model, Company, count(Amount) 
from car_sales2
group by Model, Company,year(`date`)
order by 2 desc
), Top_Model_Sold_Ranking as
(
select*, dense_rank() over(partition by years order by Amount desc) as ranking
from Top_Model_Sold
)
select*
from Top_Model_Sold_Ranking
where ranking <= 5;


----- TOP COMPANY BY REGION----
select Company,year(`date`), Dealer_Region, count(Amount)
from car_sales2
group by Company, Dealer_Region, year(`date`)
order by 2 desc;

with Region_Sales (Company, Years, Dealer_Region, Amount) as
(
select Company,year(`date`), Dealer_Region, count(Amount)
from car_sales2
group by Company, Dealer_Region, year(`date`)
order by 2 desc
), 
Region_Sales_Ranking as 
(
select*,
 dense_rank() over(partition by years order by Amount desc) as ranking
from Region_Sales
)
select*
from Region_Sales_Ranking
where ranking <=5;

--- Top Dealer by Year---

select year(`date`),Dealer_Name, Dealer_Region, count(Amount)
from car_sales2
group by Dealer_Name, Dealer_Region, year(`date`);

with Top_Dealer_Year ( Years,Dealer_Name, Dealer_Region,Amount) as
(
select year(`date`),Dealer_Name, Dealer_Region, count(Amount)
from car_sales2
group by Dealer_Name, Dealer_Region, year(`date`)
), Top_Dealer_Ranking as
(
select*, dense_rank() over(partition by Years order by Amount desc) as ranking
from Top_Dealer_Year
)
select*
from Top_dealer_Ranking
where ranking <= 5;


--- TOP Region sales by year ---

select year(`date`), Dealer_Region, count(Amount)
from car_sales2
group by  Dealer_Region, year(`date`);

with Top_Region (Years,Dealer_Region,Amount) as
(
select year(`date`), Dealer_Region, count(Amount)
from car_sales2
group by  Dealer_Region, year(`date`)
)
select*, dense_rank() over(partition by years order by Amount desc) as ranking
from Top_Region;

--- TOP car Colors Sold--
select year(`date`), Color, Company, count(Amount)
from car_sales2
group by Company, Color, year(`date`);

with Top_Color (Years,Company,Color,Amount) as
(
select year(`date`), Color, Company, count(Amount)
from car_sales2
group by Company, Color, year(`date`)
), Top_Rank_Color_sold as
(
select*, dense_rank() over(partition by  Years order by Amount desc) as ranking
FROM Top_Color
)
select*
from Top_Rank_Color_sold
where ranking <= 5;

/*



















