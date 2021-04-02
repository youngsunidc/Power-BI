# Relationship 
每一组模型关系中需要定义基数类型。 以下    **"一"**   代表该列的数值近代表唯一值， **“多”** 代表该列包含重复值
- 一对多:  一列唯一数值对多列重复数据。
- 多对一： 多列重复值对一列唯一数值。 
- 一对一： 表示两列都仅仅含有唯一的数值。 
- 多对多： 表示两列都包含重复值。 

# ALL类函数
## ALL函数
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




- **<作为表函数时> ALL函数如果对某一列进行嵌套的时候，则返回一列不重复的数值。**
![image](https://user-images.githubusercontent.com/65394762/113402243-4b915700-93d7-11eb-9fd9-d47478c20033.png)
- **<作为表函数时> ALL函数可以对某几列进行嵌套， 然后返回都不重复的数值 **
![image](https://user-images.githubusercontent.com/65394762/113403055-a4152400-93d8-11eb-8eb9-576b01d62a97.png)


## AllSelected函数



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
  1. 一对多的情况。 relatedtable将返回一个表函数。 RELATEDTABLE ( <表名> )
  2. 可以对返回的表函数，进行嵌套操作，再返回成一个值函数。 
  
![image](https://user-images.githubusercontent.com/65394762/113386818-9f8f4200-93bd-11eb-9cfe-3ab0aa91e4a9.png)




# 场景分配
## 如何计算某非重复项的数目
- Distincount(): DISTINCTCOUNT(<column>)  
- 返回： column中非重复值的数量
- 语法和例子：

> ``` ruby
> gaming_model_nub = CALCULATE(DISTINCTCOUNT(ORG[Model Name]), ORG[Quarter]=="2020Q2",ORG[Branded Gaming]="Yes")
> ```
> ![image](https://user-images.githubusercontent.com/65394762/113258671-d18d9f00-92fe-11eb-9221-96811e20b21f.png)
