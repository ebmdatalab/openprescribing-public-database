# openprescribing-public-database

# Prescribing queries

Documentation about common queries against the prescribing dataset. This document will show you how to query the raw prescribing data in order to get the information that you want.

## Set up a BigQuery Account 


You will need a Google account with the correct permissions (set up by an administrator) to access our BigQuery account. 

This will give you access to https://console.cloud.google.com/bigquery?project=ebmdatalab&p=ebmdatalab&d=public_draft&page=dataset. You should see Query Editor. 

## Familiarise yourself with the basics

Google have a quick start guide that uses publicly available data with SQL. Please follow [this quickstart](https://cloud.google.com/bigquery/quickstart-web-ui) (ignoring the first section "Before you begin")

## Brief introduction to queries in the prescribing data

SQL is structured query language and is used to interactive with data. SQL Databases consists of a number of tables that can be linked together.

This table contains prescribing data. Each practice is denoted by a practice id ('practice_id') and each drug formulation by a bnf code ('bnf_code'). 

![image](public_snapshot.PNG)

For example say we want to extract number of co-amoxiclav prescriptions (BNF codes starting '0501013K0') for one GP practice for 1 month, you can write a simple SQL query. In this scenario we will use practice id F84062 and month of July 2019. 

```sql
SELECT *
FROM ebmdatalab.public_draft.prescribing
WHERE bnf_code LIKE '0501013K0%'
AND month = '2019-07-01'
AND practice_id = 'F84062'
```

If you run this query, you will get a table with 4 rows totalling 16 prescriptions. You can export this as a CSV file. 

<details>
  
<summary>How does this query work? </summary>

<br>
Let's explore what we did:
<br>

```sql
SELECT *   <--- this selects all answers 

FROM ebmdatalab.public_draft.prescribing <--- this tells the query which tables to look at. In this case the prescribing table. 

WHERE bnf_code LIKE '0501013K0%' <---- this chooses a BNF code that starts with the code for co-amoxiclav. See below for more info. 

AND month = '2019-07-01' <---- this chooses the month. You have to select the first day of the month to get results as prescribing data is released monthly

AND practice_id = 'F84062' <---- this chooses the pratice. It needs to exactly fit the practice id

```
</details>


### Methods for selecting BNF codes

```sql
WHERE 
bnf_code LIKE '0501013K0%'  -- as above - the % is used to represent any number of wild characters following the given string
OR   -- use OR and AND in combination with brackets as needed to create your desired combination of criteria
bnf_code LIKE '0205051AAAA__AA' -- as above but using the single-character wildcard 
OR bnf_code = '0205051AAAAAAAA' -- exact match to full bnf code
OR bnf_code IN ('0205051AAAAAAAA','0205051AAAAABAB') -- a list of full bnf codes
OR SUBSTR(bnf_code,1,9) = '0407020B0' -- the substring function is used here to select the first nine characters, i.e. BNF chemical code
OR SUBSTR(bnf_code,1,6) IN ('040702','040703') -- you can't combine `LIKE` with `IN`, so this is handy to select a group of partial BNF codes (such as paragraphs or chemicals)
```


