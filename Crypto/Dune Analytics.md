# Dune Analytics Home Page

Using this project to get better with [[SQL]] and deepen my understanding of [[Crypto]]

Initial questions
- When was the first luna transaction on any dex and ust
	- luna = 2020-12-23 14:13
	- ust = 2020-12-03 17:32
- then the number of transactions every month since the first transaction and ust 

```
with luna as (
select date_trunc('month', block_time) as month_start, *
from dex."trades"
where  token_a_symbol = 'LUNA' or token_b_symbol = 'LUNA'
), ust as (
select date_trunc('month', block_time) as month_start, *
from dex."trades"
where  token_a_symbol = 'UST' or token_b_symbol = 'UST'
),luna_agg as (
select count(*) as numLuna, month_start
from luna
group by month_start
),ust_agg as (
select count(*) as numUst, month_start
from ust
group by month_start
)

select numLuna, numUst, luna_agg.month_start as month_start
from luna_agg
inner join ust_agg
on luna_agg.month_start = ust_agg.month_start




```


- luna and ust correlation

```
with a as (select caller_id, sum(duration)
from calls
group by caller_id),
b as (select callee_id, sum(duration)
from calls
group by callee_id),
c as (select caller_id, avg(duration)
from calls
group by caller_id)

select *
from person
inner join a
on a.caller_id
```