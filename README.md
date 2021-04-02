
# Relationship 
每一组模型关系中需要定义基数类型。 以下**“一”** 代表该列的数值近代表唯一值， **“多”** 代表该列包含重复值
- 一对多: 
- 多对一
- 一对一： 表示两列都仅仅含有唯一的数值。 
- 多对多： 表示两列都包含重复值。 




# 关系函数
## related函数
- related函数必须保证两个表有关联关系。 函数将进行多对一的关系。 **如果不是表之间不是"one to many"，如果是"many to many"则也无法运行公式**
- Related进行查找的时候，将检查指定表的所有值，不考虑任何筛选器。 


# 场景分配
## 如何计算某非重复项的数目
- Distincount(): DISTINCTCOUNT(<column>)  
- 返回： column中非重复值的数量
- 语法和例子：

> ``` ruby
> gaming_model_nub = CALCULATE(DISTINCTCOUNT(ORG[Model Name]), ORG[Quarter]=="2020Q2",ORG[Branded Gaming]="Yes")
> ```
> ![image](https://user-images.githubusercontent.com/65394762/113258671-d18d9f00-92fe-11eb-9221-96811e20b21f.png)

