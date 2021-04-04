# Relationship 
每一组模型关系中需要定义基数类型。 以下    **"一"**   代表该列的数值近代表唯一值， **“多”** 代表该列包含重复值
- 一对多:  一列唯一数值对多列重复数据。
- 多对一： 多列重复值对一列唯一数值。 
- 一对一： 表示两列都仅仅含有唯一的数值。 
- 多对多： 表示两列都包含重复值。 

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


# Selectcolumns和Addcolumns

## Selectedcolumns

``` ruby
- 公式：SELECTCOLUMNS ( <Table>, <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )
```
- **重新生成一个新的空表， 从其他的表中添加新的列到空表中，添加的时候可以应用表达式（正常的数学公式即可）**
![image](https://user-images.githubusercontent.com/65394762/113495979-cd909580-9527-11eb-913f-3c37f442915c.png)

## Addcolumns
- **在原始的表格上， 添加新的几列。
![image](https://user-images.githubusercontent.com/65394762/113497862-59abb880-953a-11eb-86a8-f3dcfb2e3f83.png)



# 场景分配
## 如何计算某非重复项的数目
- Distincount(): DISTINCTCOUNT(<column>)  
- 返回： column中非重复值的数量
- 语法和例子：

> ``` ruby
> gaming_model_nub = CALCULATE(DISTINCTCOUNT(ORG[Model Name]), ORG[Quarter]=="2020Q2",ORG[Branded Gaming]="Yes")
> ```
> ![image](https://user-images.githubusercontent.com/65394762/113258671-d18d9f00-92fe-11eb-9221-96811e20b21f.png)
