

# 场景分配
## 如何计算某非重复项的数目
- Distincount(): DISTINCTCOUNT(<column>)  
- 返回： column中非重复值的数量
- 语法和例子：

> ``` ruby
> gaming_model_nub = CALCULATE(DISTINCTCOUNT(ORG[Model Name]), ORG[Quarter]=="2020Q2",ORG[Branded Gaming]="Yes")
> ```
> ![image](https://user-images.githubusercontent.com/65394762/113258671-d18d9f00-92fe-11eb-9221-96811e20b21f.png)
