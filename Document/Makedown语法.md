# 标题
## 标题2
### 标题3
#### 标题4 .....

段落这里是第一个段落. 
 这里是一个段落的第二句话<br> 这里是换行语句1  
 换行语句2，两个空格加回车  
 **加粗语法**<br>
 *斜体语法*

 
 这里是第二个段落
 >这里是引用语法,引用是和段落绑定到一起的
 >
 >> 这里是第二集合的应用<br>

 引用结束

> 加上引用
> 1. First item
> 2. Second item
> 3. 有序列表语法
 
 - First
 - 无序列表

~~~json
{
    "fist":"Json",
    "second":"smith"
}   
~~~

~~~
这里是代码块
~~~

    </head>
    这里是第二种代码块表示方法

>这里是分割线 三种

>***

>---
>___



["超链接"](https://www.baidu.com "超链接的Title")

![这里是图片的超链接](./MDPic/bnq_7.png)

用两个空格表示首行缩进

## 第一种用法
```mermaid
flowchart LR
    ViewFreeMatch 
    --> ViewMatchPrepare
    --> ViewMatchConfirmation
    --> ViewMatchSelectRole
    --> 战斗开始
    --> ViewBattleGuide
    --> ViewShowMvp
    --> ViewBattleResult_和_ViewBattleAward
    --> ViewBattleResult_和_ViewBattleBalance
```

## 第二种用法
```mermaid
graph TD
    A[发现BUG] -->B(Unity中修改BUG) -->C(对修改代码打上Patch标记) -->D(执行Fix操作生成Patch) 
	-->E(发布热更) -->F(客户端正常运行)
```
