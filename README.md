# Relationship 
每一组模型关系中需要定义基数类型。 以下    **"一"**   代表该列的数值近代表唯一值， **“多”** 代表该列包含重复值
- 一对多:  一列唯一数值对多列重复数据。
- 多对一： 多列重复值对一列唯一数值。 
- 一对一： 表示两列都仅仅含有唯一的数值。 
- 多对多： 表示两列都包含重复值。 






# 筛选函数
## Filter()，表函数

- Filter与Related函数嵌套的使用。
	sale_in表格跟model managment是"many_to_one"的关系，即多个model_id对应唯一的model_mangament； 然后通过对model_mang的筛选把honor(honor在sale_in[brand]表中没有)过滤出来。 
```python

Honor = FILTER(salein,RELATED('Model Management Export'[Brand])="Honor")
```
![image](https://user-images.githubusercontent.com/65394762/113961805-3003d180-9859-11eb-91de-fe6e2e774d59.png)



## EARLIER函数
之前的Power BI都是对整列的字段进行操作的，没有细分对整行，或者某一行进行分析。 因此在Power BI中可以借助EARLIER函数进行整行分析。 
- EARLIER(<columns>,<number>)
-本行的品牌总量
``` ruby 
row_total_brand = CALCULATE(sum(ORG[Units]),FILTER(ORG,'ORG'[Brand]=EARLIER(ORG[Brand])))
```
![image](https://user-images.githubusercontent.com/65394762/113964299-cfc35e80-985d-11eb-9cb3-3e1926cb37a1.png)

-累计求和公式

``` ruby

# 第一层级筛选通过eailer把model_name筛选出来， FILTER(org,ORG[Model Name]=EARLIER(ORG[Model Name])
# 第二层级筛选通过生成表格，把所有某季度时间前的进行加和。  SUMX(FILTER([同系列表],ORG[Quarter]<=EARLIER(ORG[Quarter])),ORG[Units])
累计系列销量 = SUMX(FILTER(FILTER(org,ORG[Model Name]=EARLIER(ORG[Model Name])),ORG[Month]<=EARLIER(ORG[Month])),ORG[Units])
```



# 条件函数 
## hasonevalue()
返回TRUE值，当所选列有唯一不重复的数值

 
``` ruby 
# 例如：下面hasonevalue中的total为汇总数值，并不是唯一的数值，因此返回为空白值。
hasonevalue = IF(HASONEFILTER(ORG[Month]),[mea_unit],BLANK())
```
![image](https://user-images.githubusercontent.com/65394762/113832511-43605f80-97bb-11eb-80ad-c45c345e480f.png)

``` ruby
# brand数值中大于3,000,000的项目被筛选出来。 
units_3w = 
var brand_units =CALCULATE([mea_unit],all(ORG[Brand]))
return 
IF(HASONEVALUE(ORG[Month]),CALCULATE([mea_unit],FILTER(org, brand_units>3000000)),BLANK())
```

# 迭代函数
在DAX中，迭代函数是指对整个表执行DAX()表达式，再根据不同的函数执行不同的后续操作。 
- 先每行进行计算，再根据迭代函数的结果进行函数运行
常用的迭代函数有两类
-以X结尾的聚合函数，比如sumx(), Averagex()等。
-Filter, addcolumns, selectcolumns,rankx等其他函数， 



## sumx()函数
-公式： ```SUMX(<table>, <expression>)```
可以加总运算。
确保calculate后，可以累计相加。 这样保证汇总的数值与单行的总值相等。 
```ruby
units_3w_total = SUMX(VALUES(ORG[Month]), [units_3w])
```
![image](https://user-images.githubusercontent.com/65394762/113843498-3432df00-97c6-11eb-983d-de9721ef4a5b.png)

## max函数()
```
MAX(<expression1>, <expression2>)
MAX(<column>)  
```
- 返回最大的数值，表达式或是字符串
- Max()是聚合函数，忽略行上下文（与sum函数相似),需要通过calculate把行上下文转成筛选上下文。 




# 排名函数
## rank()排名
```RANKX(<table>, <expression>[, <value>[, <order>[, <ties>]]])```  
RANKX是一个迭代函数，它对’产品'表中的每一行都计算了该函数第二个参数处的表达式，然后再根据计算结果进行排名。

- sum()函数
把数据进行聚合。

![image](https://user-images.githubusercontent.com/65394762/115329287-b0fd8a00-a1c4-11eb-8e7e-21d36fb56a58.png)

- 度量值
把数据进行度量数值的计算。

![image](https://user-images.githubusercontent.com/65394762/115329380-cecaef00-a1c4-11eb-9e84-2c7a30b2ca02.png)


```
排名1 = rankx('产品',[销售额])
排名2 = rankx('产品',SUM('销售记录'[销售]))
排名3 = RANKX('产品',CALCULATE(SUM('销售记录'[销售])))
```
因为聚合函数，例如SUM，MIN和MAX只能感知筛选上下文，而忽略行上下文，所以得到每行相同的值，而[销售额]中存在隐性的CALCULATE函数引发了上下文转换，
将现有的行上下文转化成了等价的筛选上下文。所以，如果我们还想在排名公式中使用SUM函数的话，需要在其外面加一个CALCULATE函数（排名3），这样便可以达到同样的效果。



# ALL类函数
## ALL函数
- **返回值**当作为表函数使用时，ALL 返回完整的表或具有一列或多列的表； 当作为 CALULCATE 调节器使用时，ALL 移除参数中已应用的任何直接筛选器。
- 用作Calculate调节器时， 移除所有 <table> or <columns>的筛选器。
- 用作表函数的时， ALL(<column_Name>)返回一列或者多列所有不重复的值； ALL(<TableName>)返回表中所有的行。 

```
ALL ( [<TableNameOrColumnName>] , [ <ColumnName>, [ <ColumnName>, [ … ] ] ] )
```

 - **<作为清除筛选项>ALL函数可以忽略筛选上下文。本文中如average和total都是清除筛选条件。**
```ruby 
# 对某一列进行清除上下文的筛选。 例如对Brand列进行清除上下文。 那么 All(ORG)与All(ORG[Brand])的效果是一致的
total_units = CALCULATE(ORG[mea_unit],  ALL(ORG ) )
total_units = CALCULATE(ORG[mea_unit],  ALL(ORG[Brand]) )
```
![image](https://user-images.githubusercontent.com/65394762/113401113-75e21500-93d5-11eb-8dc4-a3b2c690cb06.png)

- **<作为表函数> ALL函数清除排名的影响。**
``` ruby 
# 如果不使用All()函数，由于是度量值，受筛选上下文的影响。 因而返回数值永远为1.
# 首先，由于使用了Measure，其特点是会根据创建表单所使用的列动态获取计算结果。
# Measure的计算结果会受到筛选上下文影响。由于没有使用ALL函数，导致RANKX函数中使用的参数表单Sales，会根据筛选条件的变化而变化，相当于每一行数据都在跟自己做排序，因此结果有误。
# 计算Rankx排名的时候，ALL(ORG)的时候是把所有列都去除筛选。返回的是ORG的原表， 而Brand_units则与每一行进行大小比较。 
# ALL(ORG[Brand])只把Brand列去除筛选。 返回一个不重复的Brand列。 
ALL_Brand = RANKX(ALL(ORG[Brand]),[mea_unit]) 
All_sheets = RANKX(ALL(ORG),[mea_unit])
```
![image](https://user-images.githubusercontent.com/65394762/113477489-992fc180-94b4-11eb-8082-8516a5ee52cb.png)




- **<作为表函数时> ALL函数如果对某一列进行嵌套的时候，则返回一列不重复的数值。**
![image](https://user-images.githubusercontent.com/65394762/113402243-4b915700-93d7-11eb-9fd9-d47478c20033.png)
- **<作为表函数时> ALL函数可以对某几列进行嵌套， 然后返回都不重复的数值**
![image](https://user-images.githubusercontent.com/65394762/113403055-a4152400-93d8-11eb-8eb9-576b01d62a97.png)


## AllSelected函数

用作表函数时，ALLSELECTED 返回表(或列)在最后一个影子筛选上下文中的筛选结果。用作调节器时，它恢复每列的最后一个影子筛选上下文。
如果在不同的影子筛选上下文中存在多列，ALLSELECTED 使用每个列的最后一个影子筛选上下文。

```ruby
ALLSELECTED ( [<表名或列名>], [ <列名>, <列名>, … ] )
```
- 返回值:表函数，忽略除了外部筛选器的所有筛选。 

- 当使用Allselected函数的时候， 会忽略除了外部筛选器的所有筛选工具
![image](https://user-images.githubusercontent.com/65394762/113478110-53292c80-94b9-11eb-8e7a-59c4063e0666.png)

## Allexcept函数
如果希望用 ALL 函数调用表的多数列，那么可以使用 ALLEXCEPT 替代。ALLEXCEPT 的语法需要一个表，后跟要从结果中排除的列。因此，ALLEXCEPT 返回一个表，包含了表中其他列现有值组合的唯一列表。

- 返回值： 表函数
- 筛选功能上： **ALL函数是设置哪些列不起筛选作用，而ALLEXCEPT函数是保留哪些列起筛选作用。**
![image](https://user-images.githubusercontent.com/65394762/113479269-a5218080-94c0-11eb-85fb-f0e0079a4f3f.png)

- 筛选上下文上: **如果是ALL函数的话，是把所有列都取消筛选， 但是Allexpected相当于忽略某一特定列的筛选。**
              **g_all是取消所有的筛选条件， g_all_acc取消除了brand列的所有筛选条件，因此当上下文为brand的时候，仍然有上下文筛选** 
![image](https://user-images.githubusercontent.com/65394762/113495117-a931ba80-9521-11eb-9a6a-6cb7597dc59e.png)



# 关系函数
## related函数（many to one）
  1.数值函数， 将返回一个值函数。  
  2.可以简单的理解为一个**vlookup函数**， 但是应用的前提是必须要保证“多对一”的关系。 而且是以**新建列**的形式进行出现。  
  3.related函数必须保证两个表有关联关系。 函数将进行多对一的关系。 **如果不是表之间不是"many to one"，如果是"many to many"则也无法运行公式** 

- 多对一关系（必须）
在使用related函数的时候，**插入列所在的表**，比较要保证有一个“Many to one”的对应关系。 
> ![image](https://user-images.githubusercontent.com/65394762/113377426-d60d9280-93a6-11eb-973b-ff9bf91bc28c.png)

- 可以与IF函数进行连立。
![image](https://user-images.githubusercontent.com/65394762/113383259-1a545f00-93b6-11eb-96c6-bb79ae7a2b98.png)

## relatedtable函数(one to many)
- 一对多的情况。 relatedtable将返回一个表函数。 RELATEDTABLE ( <表名> )
- 可以对返回的表函数，进行嵌套操作，再返回成一个值函数。 
  
![image](https://user-images.githubusercontent.com/65394762/113386818-9f8f4200-93bd-11eb-9cfe-3ab0aa91e4a9.png)

## Treatas函数
- 表达式：``` TREATAS(table_expression, <column>[, <column>[, <column>[,…]]]} ) 
                         Treate columns as columns_from other table           ```

利用表函数(table expression)对其他表中的关联列进行筛选。 

- 通过筛选格式，建立虚拟链接。
此图中，"新月度表"和"ORG"表并没有建立如何的关联，如果需要引用另外一个表中的度量值的话， 必须通过treatas进行关联。 
![image](https://user-images.githubusercontent.com/65394762/113554068-53493980-962b-11eb-906f-85885cd304d9.png)

- 正常案例
因此，我们在这里应当使用关联的形式进行计算。 
mea_unit表示没有发生联立的结果，因此返回的数值都是一样的数值
units_brand表示通过在org_brand中筛选brand进行联立
units_compy表示通过在org_company中筛选brand,但是，有一些品牌在company中是没有的，如Alienware,Dell,Honor,因此返回为空值。 

``` ruby
# mea_unit是代表ORG(其他表)的汇总和。 # units_brand表示通过brand之间进行筛选联立 ； # units_compy表示通过在[org]company列进行筛选brand的内容
mea_unit = SUM(ORG[Units]) 
units_brand = CALCULATE([mea_unit],TREATAS(VALUES('最新月度表'[Brand]),ORG[Brand])) 
units_compy = CALCULATE([mea_unit],TREATAS(VALUES('最新月度表'[Brand]),ORG[Company])) 

```
![image](https://user-images.githubusercontent.com/65394762/113554644-36613600-962c-11eb-9ce6-ec894b5e3d01.png)

- treate列与表现列不同时。
 当建立表格的时候，母列为Brand,而子列为proessor。 Treat brand 作为筛选对象。
 则brand为母列下面只要包含一个processor brand就返回总值。因为是通过value()和calculate()进行计算返回总值。

```units_modelgpu = CALCULATE(SUM(ORG[Units]),TREATAS(VALUES('最新月度表'[Processor Brand]),ORG[Processor Brand]))```

![image](https://user-images.githubusercontent.com/65394762/113556070-7b866780-962e-11eb-9bca-97a5695a2571.png)



# 创建表函数
## DATATABLE()
- 通过Datatable的函数生成一个辅助表。以字典的形式传入一系列的数据
``` 
# values 可以包括black(),Date,string,etc...
DATATABLE (ColumnName1, DataType1, ColumnName2, DataType2..., {{Value1, Value2...}, {ValueN, ValueN+1...}...}) 
```

- 实操案例。 
``` ruby
datatables = DATATABLE("index",INTEGER,"选择形式",STRING,{
                        {1,"大于平均数值"},
                        {2,"小于平均数值"}    })
```
![image](https://user-images.githubusercontent.com/65394762/113656555-b213bf00-96ce-11eb-8259-dbdb9920f93d.png)




## Selectedcolumns
选择某一列，返回带有运算公式的不重复的数值。
``` ruby
- 公式：SELECTCOLUMNS ( <Table>, <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )
```
- **重新生成一个新的空表， 从其他的表中添加新的列到空表中，添加的时候可以应用表达式（正常的数学公式即可）**
![image](https://user-images.githubusercontent.com/65394762/113495979-cd909580-9527-11eb-913f-3c37f442915c.png)

## Addcolumns
- **在原始的表格上， 添加新的几列。**(红色为原表，蓝色为新列)
![image](https://user-images.githubusercontent.com/65394762/113497862-59abb880-953a-11eb-86a8-f3dcfb2e3f83.png)
- **在原始的表格是， 添加新的几列并且带着运算公式**
![image](https://user-images.githubusercontent.com/65394762/113500260-1576e300-954f-11eb-8973-999fe93415c6.png)

## selectvalue
- 当上下文过滤为一个数值时，则就返回该值。 
``` ruby
SELECTEDVALUE(<columnName>[, <alternateResult>])
```
- 示例: 常以Switch()进行套用，当使用外部切片器的时候， 会返回选择的数值。 
``` ruby
value_units = SWITCH(TRUE(),
                SELECTEDVALUE('Value参数表'[value])="销售量",ORG[mea_unit],
                SELECTEDVALUE('Value参数表'[value])="销售价值",ORG[mea_value])
```
![image](https://user-images.githubusercontent.com/65394762/113509388-dd3fc680-9587-11eb-8e78-47b9bd4499d6.png)


## summarize函数
- 公式： ```SUMMARIZE (<table>, <groupBy_columnName>[, <groupBy_columnName>]…[, <name>, <expression>]…)```
- 通过groupby的形式摘取某表中的某列，返回一个表函数， 也可以重新定义某列（引入某种表达公式）

```table_summ = SUMMARIZE(ORG,ORG[Brand],ORG[Gaming],ORG[Processor Vendor],"汇总Units数值",sum(ORG[Units])) ```
![image](https://user-images.githubusercontent.com/65394762/113653965-6579b500-96c9-11eb-81c4-a38ac0b48aba.png)







## 应用场景
- **拼接新表**
selectedcolumns与addcolumns结合。 
先通过Addcolumns(values(),"new_name","new_columns")的形式生成新的表格，然后再通过union()函数把表格自动拼接在一起。 

``` ruby
table_try = 
var brand_units_in = CALCULATE([salein_unit],ALL(salein[Vendor]))
var brand_units_out = CALCULATE([mea_unit],ALL(ORG[Brand]))
var table1 = SELECTCOLUMNS(ADDCOLUMNS(VALUES(ORG[Brand]),"Brand_new",ORG[Brand],"Channels","Sale_out"),"columns",[Brand_new],"seg_column",brand_units_out,"channels",[Channels])
var table2 = SELECTCOLUMNS(ADDCOLUMNS(VALUES(salein[Vendor]),"vendor_new",salein[Vendor],"Channels","Sale_in"),"columns",[vendor_new],"seg_column",brand_units_in,"channels",[Channels])
return UNION(table1,table2)
```

![image](https://user-images.githubusercontent.com/65394762/113507415-d6f81d00-957c-11eb-9a15-7ea3dabc1421.png)


- **动态轴交互**
不同轴，可以用切片器筛选X轴。 X轴的参数来源于虚拟表，因此需要通过Treatas函数对其进行虚拟联立。
ruby
```
in_out_units = SWITCH(TRUE(),
        SELECTEDVALUE(table_try[channels])="Sale_out",CALCULATE([mea_unit],TREATAS(VALUES(table_try[columns]),ORG[Brand])),
        SELECTEDVALUE(table_try[channels])="Sale_in",CALCULATE([salein_unit],TREATAS(VALUES(table_try[columns]),salein[Vendor])))
        
```
![image](https://user-images.githubusercontent.com/65394762/113509995-044bc780-958b-11eb-90cc-e9591352479d.png)

# 时间函数表
## 时间日期表的建立
 
- 标准时间表的确立：
``` ruby
日期表 = 
ADDCOLUMNS (
CALENDAR (DATE(2017,1,1), DATE(2020,12,31)),
"年度", YEAR ( [Date] ),
"季度", "Q" & FORMAT ( [Date], "Q" ),
"月份", FORMAT ( [Date], "MM" ),
"日",FORMAT ( [Date], "DD" ),
"年度季度", FORMAT ( [Date], "YYYY" ) & "Q" & FORMAT ( [Date], "Q" ),
"年度月份", FORMAT ( [Date], "YYYY/MM" ),
"星期几", WEEKDAY ( [Date],1 )
)
```

## 计算上个年/月/日的方法: DATEADD()函数
```ruby 
# 这里的时间为MONTH，表示以MONTH为时间做平移。 
PREV_MONTH = CALCULATE(SUM(ORG[Units]), DATEADD('日期表'[Date].[Date], -1, MONTH))
# 同样Date也可以调节为了yoy%的形式，这里的时间函数为YEAR.
PREV_Year = CALCULATE(SUM(ORG[Units]), DATEADD('日期表'[Date].[Date], -1, YEAR))
```
使用pre_month的方法，可以计算度量值“月”上一个月的数值。 
![image](https://user-images.githubusercontent.com/65394762/113669360-3aea2500-96e6-11eb-867d-91a737a518ab.png)

## 计算下个年/月/日的方法： NEXTMONTH()函数
``` ruby
Next_month = CALCULATE(SUM(ORG[Units]),NEXTMONTH('日期表'[Date].[Date]))
```
![image](https://user-images.githubusercontent.com/65394762/113807898-7ee93280-9797-11eb-9296-882e6f4e4a46.png)

## TOTALYTD/QTD/MTD:年/季/月初至今
- 公式： TOTALMTD(<expression>,<dates>[,<filter>])  



## DATESBETWEEN
- ``` DATESBETWEEN ( <Dates>, <StartDate>, <EndDate> )```
-  返回两个时间段中间的所有数值的时间差。 返回类型： 表函数

- 区间算数值
```period_units = CALCULATE(sum(ORG[Units]),DATESBETWEEN('日期表'[Date], DATE(2020,5,1),DATE(2020,8,1)))```
![image](https://user-images.githubusercontent.com/65394762/115177565-13da1d00-a102-11eb-9232-c743336c318b.png)

-生成一个区间时间表
```Table_data_per = DATESBETWEEN('日期表'[Date], DATE(2020,5,1),DATE(2020,8,1))```
![image](https://user-images.githubusercontent.com/65394762/115177263-6d8e1780-a101-11eb-9443-031f9deb08da.png)


## DATAESINPERIOD
- ```DATESINPERIOD ( <Dates>, <StartDate>, <NumberOfIntervals>, <Interval> )```
- NumberOfIntervals表示间隔数量，负数代表过去，正数代表未来； Interval间隔类型:年，季度，月和日
-示例
```period_units = CALCULATE(sum(ORG[Units]), DATESINPERIOD('日期表'[Date],DATE(2020,5,1),4,MONTH))```
![image](https://user-images.githubusercontent.com/65394762/115178341-a929e100-a103-11eb-948f-c8ffe3937a90.png)
``` mysql
DATESINPERIOD('date'[Date],DATE(2018,2,1),1,DAY)     // 返回 2018 年 2 月 1 日
DATESINPERIOD('date'[Date],DATE(2018,2,1),1,MONTH)   // 返回 2018 年 2 月 1 日至 2018 年 2 月 28 日，不含 3 月 1 日
DATESINPERIOD('date'[Date],DATE(2018,2,1),-1,MONTH)  // 返回 2018 年 1 月 2 日至 2018 年 2 月 1 日，不含 1 月 1 日
DATESINPERIOD('date'[Date],DATE(2018,2,1),1,QUARTER) // 返回 2018 年 2 月 1 日至 2018 年 4 月 30 日
DATESINPERIOD('date'[Date],DATE(2018,2,1),1,YEAR)    // 返回 2018 年 2 月 1 日至 2019 年 1 月 31 日，不含 19 年 2 月 1 日
```


## 计算环比变化的方法
核心的要求是通过_pre_month计算出平移时间的units数值， 然后通过divide进行计算。 

``` ruby 
Units MoM% = 
IF(
	ISFILTERED('日期表'[Date]),
	ERROR("时间智能快速度量值只能按 Power BI 提供的日期层次结构或主日期列进行分组或筛选。"),
	VAR __PREV_MONTH = CALCULATE(SUM('ORG'[Units]), DATEADD('日期表'[Date].[Date], -1,MONTH))
    vAR MOM= DIVIDE(SUM('ORG'[Units]) - __PREV_MONTH, __PREV_MONTH)
	RETURN
        SWITCH(TRUE(),MOM=-1,0,MOM<>-1, MOM
		))
```






# 场景分配
## 如何计算某非重复项的数目
- Distincount(): DISTINCTCOUNT(<column>)  
- 返回： column中非重复值的数量
- 语法和例子：

> ``` ruby
> gaming_model_nub = CALCULATE(DISTINCTCOUNT(ORG[Model Name]), ORG[Quarter]=="2020Q2",ORG[Branded Gaming]="Yes")
> ```
> ![image](https://user-images.githubusercontent.com/65394762/113258671-d18d9f00-92fe-11eb-9221-96811e20b21f.png)

