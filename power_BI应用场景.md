
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
