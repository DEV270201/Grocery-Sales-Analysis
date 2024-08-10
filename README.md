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


