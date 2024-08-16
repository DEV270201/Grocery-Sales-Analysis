# Grocery-Sales-Analysis
Using SQL along with Common Table Expressions for building queries and getting insights from the data. I have uploaded the SQL file which contains the related script for loading data into the database.

#### 1. For each customer, compute the minimum and maximum sales quantities along with the corresponding products (purchased), dates (i.e., dates of those minimum and maximum sales quantities) and the states in which the sale transactions took place. If there are >1 occurrences of the min or max, display all

--> with cte1 as ( <br/>
select cust, min(quant) as minimum, max(quant) as maximum from sales ,<br/>
group by cust<br/>
),<br/>
cte2 as (<br/>
select s.cust,s.prod,s.quant,s.state,s.date from sales s<br/>
inner join cte1 c on s.cust = c.cust and s.quant = c.maximum<br/>
),<br/>
cte3 as (<br/>
select s.cust,s.prod,s.quant,s.state,s.date from sales s<br/>
inner join cte1 c on s.cust = c.cust and s.quant = c.minimum<br/>
)<br/>
select c2.cust as CUSTOMER, c2.prod as MAXI_PROD, c2.quant as MAXI_QUANT, c2.date as MAXI_DATE, c2.state as ST, <br/>
                            c3.prod as MINI_PROD, c3.quant as MINI_QUANT, c3.date as MINI_DATE, c3.state as ST<br/>
                            from cte2 c2 inner join cte3 c3 on c2.cust = c3.cust;<br/>

O/P) ![image](https://github.com/user-attachments/assets/d0e9888d-b358-4f72-881a-6f2f396cd821)

#### 2. For each year and month combination, find the “busiest” and the “slowest” day (those days with the most and the least total sales quantities of products sold) and the corresponding total sales quantities (i.e., SUMs).

--> with cte1 as (<br/>
select day,month,year, sum(quant) as sale from sales<br/>
group by day,month,year<br/>
),<br/>
cte2 as (<br/>
 select month,year,min(sale) as minimum from cte1<br/>
 group by month,year<br/>
),<br/>
cte3 as (<br/>
 select month,year,max(sale) as maximum from cte1<br/>
 group by month,year<br/>
),<br/>
cte4 as (<br/>
select c2.year as YEAR, c2.month as MONTH, c1.day as SLOWEST_DAY, c2.minimum as MIN_QUANT<br/>
from cte1 c1 inner join cte2 c2 on c1.month = c2.month and c1.year = c2.year and c1.sale = c2.minimum<br/>
),<br/>
cte5 as (<br/>
select c3.year as YEAR, c3.month as MONTH, c1.day as BUSIEST_DAY, c3.maximum as MAX_QUANT<br/>
from cte1 c1 inner join cte3 c3 on c1.month = c3.month and c1.year = c3.year and c1.sale = c3.maximum<br/>
)<br/>
select * from cte4 natural join cte5 order by cte4.year,cte4.month;<br/>

O/P) ![image](https://github.com/user-attachments/assets/18506343-4927-40cc-bdd6-96f205bde907)

#### 3. For each customer, find the “most favorite” product (the product that the customer purchased the most) and the “least favorite” product (the product that the customer purchased the least).

--> with cte1 as (<br/>
  select cust,prod,sum(quant) as Quantity from sales<br/>
	group by cust,prod<br/>
),<br/>
cte2 as (<br/>
   select cust,min(Quantity) as min_quant from cte1<br/>
	group by cust<br/>
),<br/>
cte3 as (<br/>
   select cust,max(Quantity) as max_quant from cte1<br/>
	group by cust<br/>
)<br/>
select * from ((select p.cust as "CUSTOMER", p.prod as "MOST_FAV_PROD" from cte1 p<br/>
inner join cte3 m on m.cust=p.cust and m.max_quant=p.Quantity) temp1<br/>
natural join (<br/>
select p.cust as "CUSTOMER", p.prod as "LEAST_FAV_PROD" from cte1 p<br/>
inner join cte2 l on l.cust=p.cust and l.min_quant=p.Quantity) temp2);<br/>

O/P) ![image](https://github.com/user-attachments/assets/68115ea3-5edc-43ff-847e-c841f8cd2957)

#### 4. For each customer and product combination, show the average sales quantities for the four seasons, Spring, Summer, Fall and Winter in four separate columns – Spring being the 3 months of March, April and May; and Summer the next 3 months (June, July and August); and so on – ignore the YEAR component of the dates (i.e., 10/25/2016 is considered the same date as 10/25/2017, etc.). Additionally, compute the average for the “whole” year (again ignoring the YEAR component, meaning simply compute AVG) along with the total quantities (SUM) and the counts (COUNT).

--> select cust as CUSTOMER, prod as PRODUCT,<br/>
round(avg(case when month=3 then quant <br/>
         when month=4 then quant <br/>
         when month=5 then quant else 0 end),2) as SPRING_AVG,<br/>
round(avg(case when month=6 then quant <br/>
         when month=7 then quant <br/>
         when month=8 then quant else 0 end),2) as SUMMER_AVG,<br/>
round(avg(case when month=9 then quant <br/>
         when month=10 then quant <br/>
         when month=11 then quant else 0 end),2) as FALL_AVG,<br/>
round(avg(case when month=12 then quant <br/>
         when month=1 then quant <br/>
         when month=2 then quant else 0 end),2) as WINTER_AVG,<br/>
round(avg(quant),2) as AVERAGE,<br/>
sum(quant) as TOTAL,<br/>
count(*) as COUNT<br/>
from sales<br/>
group by cust,prod;<br/>

O/P) ![image](https://github.com/user-attachments/assets/bde40a4c-f528-481f-888a-633f84232126)

#### 5. For each product, output the maximum sales quantities for each quarter in 4 separate columns. Like the first report, display the corresponding dates (i.e., dates of those corresponding maximum sales quantities). Ignore the YEAR component of the dates (i.e., 10/25/2016 is considered the same date as 10/25/2017, etc.)

--> with cte as (<br/>
select prod as PRODUCT,<br/>
max( case when month=1 then quant <br/>
          &ensp;when month=2 then quant <br/>
          &ensp;when month=3 then quant else 0 end) as Q1_MAX,<br/>
max( case when month=4 then quant <br/>
         &ensp;when month=5 then quant <br/>
         &ensp;when month=6 then quant else 0 end) as Q2_MAX,<br/>
max( case when month=7 then quant <br/>
         &ensp;when month=18 then quant <br/>
         &ensp;when month=9 then quant else 0 end) as Q3_MAX,<br/>
max( case when month=10 then quant <br/>
         &ensp;when month=11 then quant <br/>
         &ensp;when month=12 then quant else 0 end) as Q4_MAX<br/>
from sales<br/>
group by prod),<br/>
dquarter_1 as (<br/>
  &ensp; select c.PRODUCT, c.Q1_MAX, s.date as DATE from sales s<br/>
   &ensp;inner join cte c on s.prod=c.PRODUCT and s.quant=c.Q1_MAX<br/>
	&ensp;where s.month in (1,2,3)<br/>
),<br/>
dquarter_2 as (<br/>
  &ensp; select c.PRODUCT, c.Q2_MAX, s.date as DATE from sales s<br/>
  &ensp; inner join cte c on s.prod=c.PRODUCT and s.quant=c.Q2_MAX<br/>
	&ensp;where s.month in (4,5,6)<br/>
),<br/>
dquarter_3 as (<br/>
  &ensp; select c.PRODUCT, c.Q3_MAX, s.date as DATE from sales s<br/>
  &ensp; inner join cte c on s.prod=c.PRODUCT and s.quant=c.Q3_MAX<br/>
	&ensp;where s.month in (7,8,9)<br/>
),<br/>
dquarter_4 as (<br/>
&ensp;   select c.PRODUCT, c.Q4_MAX, s.date as DATE from sales s<br/>
&ensp;   inner join cte c on s.prod=c.PRODUCT and s.quant=c.Q4_MAX<br/>
	&ensp;where s.month in (10,11,12)<br/>
)<br/>
select q1.*, q2.Q2_MAX, q2.DATE, q3.Q3_MAX, q3.DATE, q4.Q4_MAX, q4.DATE from dquarter_1 q1<br/>
inner join dquarter_2 q2 on <br/>
q1.PRODUCT = q2.PRODUCT<br/>
inner join dquarter_3 q3 on q1.PRODUCT = q3.PRODUCT<br/>
inner join dquarter_4 q4 on q1.PRODUCT = q4.PRODUCT order by q1.PRODUCT;<br/>

O/P) ![image](https://github.com/user-attachments/assets/38a880d1-3f28-4a8e-b2db-445d583b3887)

#### 6. For each customer, product and month, count the number of sales transactions that were between the previous and the following month's average sales quantities. For January and December, display <NULL> or 0

--> with q1 as (<br/>
select cust,prod,month,round(avg(quant)) as avg from sales s<br/>
group by cust,prod,month),<br/>
q2 as (<br/>
select l.cust,l.prod,l.month curr_month,l.avg curr_avg,r.avg next_avg<br/>
from q1 l left join q1 r on l.month+1=r.month and l.cust=r.cust and l.prod=r.prod),<br/>
q3 as (<br/>
select l.*, r.avg prev_avg<br/>
from q2 l left join q1 r on l.curr_month=r.month+1 and l.cust=r.cust and l.prod=r.prod),<br/>
q4 as (<br/>
select q3.*,s.quant from q3 <br/>
inner join sales s <br/>
on q3.cust = s.cust and q3.prod = s.prod <br/>
and q3.curr_month = s.month)<br/>
select cust as CUSTOMER, prod as PRODUCT, curr_month as MONTH, count(*) as SALES_COUNT_BETWEEN_AVGS<br/>
from q4 where quant between prev_avg and next_avg or quant between next_avg and prev_avg<br/>
group by cust,prod,month<br/>
order by cust,prod;<br/>

O/P) ![image](https://github.com/user-attachments/assets/d9cf1f82-52cd-4cd1-b13c-c6faee7bac5c)

#### 7. For customer and product, show the average sales before, during and after each month (e.g., for February, show average sales of January and March. For “before” January and “after” December, display <NULL>. The “YEAR” attribute is not considered for this query – for example, both January of 2017 and January of 2018 are considered January regardless of the year

--> with q1 as (<br/>
select cust,prod,month,round(avg(quant)) as curr_avg from sales s<br/>
group by cust,prod,month),<br/>
q2 as (<br/>
  select l.cust, l.prod, l.month, l.curr_avg, r.month as next, r.curr_avg as next_avg<br/>
  from q1 l left join q1 r on l.month+1 = r.month and l.cust = r.cust and l.prod = r.prod<br/>
),<br/>
q3 as (<br/>
  select l.*, r.month as prev,r.curr_avg as prev_avg<br/>
  from q2 l left join q1 r on l.month = r.month+1 and l.cust = r.cust and l.prod = r.prod<br/>
)<br/>
select a.cust as CUSTOMER, a.prod as PRODUCT, a.month as MONTH, <br/>
a.prev_avg as BEFORE_AVG, a.curr_avg as DURING_AVG, a.next_avg as AFTER_AVG<br/>
from q3 a;<br/>

O/P) ![image](https://github.com/user-attachments/assets/5969a399-4101-4184-9416-e6e921922836)



