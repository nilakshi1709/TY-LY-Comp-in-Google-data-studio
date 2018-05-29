# TY-LY-Comp-in-Google-data-studio
To find TY, LY &amp; Comp (for fiscal calendar) in Google data studio using custom queries and calculated fields.

#### Where we can use:
- To find the “This year” vs “Last year” comparisons for various KPIs for retail year.
- This feature is essential for decision makers in any organization.

#### How to Implement?
Consider the below example where we want to find the This Year (TY) sales comparison with Last Year (LY) for fiscal year.
( Note : Please refer the fiscal calendar 454 for more details )

#### Step 1: Create a view in BigQuery to find TY & LY weekly comparison
This will give the TY & LY data at daily level:
```
Table1:
with
data_ty as
(select date,
        date_add(date,interval - 364 DAY) as date_ly,
        fiscal_week_start_date,
        sum(visits) as visits_ty
  from dataset.project.table
  group by 1,2,3),

data_ly as
(select date,
        fiscal_week_start_date,
        sum(visits) as visits_ly
  from dataset.project.table
  group by 1,2)

select                           
  a.date,
  a.date_ly,
  a.fiscal_week_start_date,
  visits_ty, visits_ly
  from data_ty as a left join data_ly as b
on a.date_ly=b.date
```

#### Step 2: Create view in BQ to Join the Table1 created in 1st step with dim_date_454  to get customised time dimension in data 
studio:

So that we can add the custom time dimension as fiscal week, fiscal month, fiscal quarter, fiscal year to data studio:
```
Table 2:

SELECT
      date,
      fiscal_week_start_date,
      fiscal_month_start_date,
      Fiscal_quarter_start_date,
      Fiscal_year_start_date,
      sum(visits_ty) as visits_ty,
      sum(visits_ly) as visits_ly,
              
  From
 `Table1` as a join
 `dim_date_454` as b
  on a.date=(b.date_value)
 
 group by 1,2,3,4,5
```
#### Step 3: Add the Table2 created in step 2 to data studio

- Click on add new data source
- Select BigQuery connector
- Select Table2
- Click on connect at the top right corner of the screen and add the table to the report

![alt text](https://drive.google.com/uc?id=1bNghiutQnrntNZmjmih05rCpjVaMQhpL)

#### Step 4: Add the scorecard to report and select metric as below:

![alt text](https://drive.google.com/uc?id=1C51I7MD4BwsnjOBWmMiY8qwKjtYMbgWB)

- Add the scorecards for “visits_ty” and “visits_ly”
- Add Filter control to the report and select “fiscal_week_start_date” as Dimension

![alt text](https://drive.google.com/uc?id=1KhfGM392k0EkU3aJ-qq8CRS1X-7mGgC4)

#### Step 5: Calculate “COMP” as below:
![alt text](https://drive.google.com/uc?id=1Os_sxoVU0EntDIc9L-t6KaAgch_zPSha)

 1. Select “create new field” at bottom right corner
![alt text](https://drive.google.com/uc?id=1n_8INq_iBUSakpqOYuGXjyrcgiGxU5Fs)

It will be directed to the page below:
![alt text](https://drive.google.com/uc?id=1VLFtBlz_MLFwXWtkAEWCMFGChmwg2Wfs)

Insert formula for vists_comp as :
Comp = (visits_ty - visits_ly) / visits_ly
Click on Save
Go to All Fields 
![alt text](https://drive.google.com/uc?id=1PBG1S0fETM96d02-8wjBFAMvsFwzfOtJ)

Change type for visits_comp from “number” to ‘percent’ and click on Done

![alt text](https://drive.google.com/uc?id=1xFGUgejytKE69Ghk783DxIKP9_nDZdRS)

Add scorecard for COMP as below:
Click on view report 

![alt text](https://drive.google.com/uc?id=12zo36ubFjtezk-lDPg9YUJHGse7zyiZe)







