
# 如何计算某非重复项的数目
- Distincount(): DISTINCTCOUNT(<column>)  
- 返回： column中非重复值的数量
- 语法和例子：

> ``` ruby
> gaming_model_nub = CALCULATE(DISTINCTCOUNT(ORG[Model Name]), ORG[Quarter]=="2020Q2",ORG[Branded Gaming]="Yes")
> ```
> ![image](https://user-images.githubusercontent.com/65394762/113258671-d18d9f00-92fe-11eb-9221-96811e20b21f.png)


# 如何实现SQL中的left join方法
## 通过DAX语言进行实现（Summriazed）
``` python
Table_summ = SUMMARIZE(ORG,ORG[Model Name],ferda_models[GPU Model],"sum_unit",sum(ORG[Units]) )
```
等同于mysql中的left join语句
``` mysql
select ORG.Model Name, ferda_models.GPU Model , sum(ORG.units) as "sum_units"
from ORG
left join ferda_models
on ORG.key = ferda_models.key
group by Model Name, GPU Model
```

## 通过合并查询功能进行实现
PQ中的合并查询功能可以满足这个要求
- 第一步
![image](https://user-images.githubusercontent.com/65394762/116352914-7b8e1780-a828-11eb-9c5f-2be80f9218de.png)

-第二步
![image](https://user-images.githubusercontent.com/65394762/116352790-4e416980-a828-11eb-9acd-a7c4aa40413c.png)

## 


