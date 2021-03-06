# **keyCount 编程作业**
软件工程第一次编程作业 keyCount

| 个作业属于哪个课程 | https://bbs.csdn.net/forums/fzuSoftwareEngineering2021 |
| ----------------- |--------------- | 
| 这个作业要求在哪里| https://bbs.csdn.net/topics/600574694 |
| 这个作业的目标 | 制定个人编程规范、C 或 C++文件关键字提取 |
| 学号 | 081900223 |

# **目录**
* [keyCount 作业过程](#keyCount作业过程)
	* [PSP表格](#PSP表格)
	* [commits信息](#commits信息)
	* [需求分析&解题思路](#需求分析and解题思路)
	* [代码迭代](#代码迭代)
		* [输入格式](#输入格式)
		* [文本预处理](#文本预处理)
		* [关键词处理](#关键词处理)
		* [输出模式](#输出模式)
	* [性能分析](#性能分析)
		* [样例结果&单元测试](#样例结果and单元测试)
		* [运行时间](#运行时间)
	* [学习心得](#学习心得)
		* [困难与认识](#困难与认识)
		* [正则表达式](#正则表达式)


# **keyCount作业过程**
# PSP表格

| Personal Software Process Stages | 预估耗时(分钟) | 实际耗时(分钟) |
 :- | :-: | :-:
| Planning（计划）|  |  |
| · Estimate（估计时间） | - | - |
| Development（开发） |  |  |
| · Analysis（需求分析&学习新知识） | 180 | 300 |
| · Design Spec（生成设计文档） | - | - |
| · Design Review（设计复审） | - | - |
| · Coding Standard（代码规范 ） | 120 | 120 |
| · Design（具体设计） | - | - |
| · Coding（具体编码） | 60 | 80 |
| · Code Review（代码复审） | 20 | 30 |
| Test（测试） | 10 | 10 |
| · Test Report（测试报告） | 15 | 20 |
| · Size Measurement（计算工作量） | - | - |
| · Postmortem & Process Improvement Plan<br>（事后总结, 并提出过程改进计划） | 60 | 120 |
| Total（合计） | 465 | 680 |

# commits信息
![image](./image/commits_message.png)

#  需求分析and解题思路  
### 输入格式：python keyCount.py key.c mode  
> + 需进行 ***提取输入信息、进行文件获取、文本化、文本预处理***    
> + 将文本内容预处理为列表
> + 使用 用到 ***sys*** 和 ***re*** 包

### 基础要求：输出关键字统计信息  
> + 需要初始化 ***关键词字典信息*** ，应设置为元组常数

### 进阶要求：输出有几组 switch case 结构，同时输出每组对应的case个数  
### 拔高要求：输出有几组 if else 结构  
### 终极要求：输出有几组 if，else if，else 结构  
> + 需要一个 ***关键词理函数*** ，对列表元素进行进行分类处理
> + 应该根据 ***难度要求 mode*** 选择不同 ***输出模式***

# 代码迭代
>&#160; &#160; &#160; &#160;刚开始的时候未考虑到被编程规范等要求，虽能实现其功能，但较为杂乱无章，第二天紧急补课了编程规范后对程序进行了一轮大改，将其从一段一团乱麻拯救成井然有序的面向函数，限于 markdown 能力有待提高，部分格式还未能完全在下方展示出
## **初始化参数**
>建立初始化计数变量、if&elseif堆栈、关键字词典

```python
# 初始化计数变量 count
key_count = 0
witch_count = 0
case_count = []	# 以列表储存 case 数量
if_else_count = 0
if_elif_else_count = 0

# 初始化 堆栈 if_stack
if_stack=[]

# 初始化关键字元组 KEYWORDS
KEYWORDS = (
		    'auto', 'break', 'case', 'char', 'const', 'continue','default',
			'do', 'double', 'else', 'enum', 'extern','float', 'for', 'goto',
			'if', 'int', 'long', 'register', 'return', 'short', 'signed',
			'sizeof', 'static','struct', 'switch', 'typedef', 'union', 
			'unsigned', 'void', 'volatile', 'while'
)
```
## **文本预处理**

>&#160; &#160; &#160; &#160;读入文件并不能直接被关键字处理函数接受，通过预处理剔除无关字符，并将其分裂为列表   
&#160; &#160; &#160; &#160;考虑到可能存在有 <font color='pink'>***冗余空格***</font> 对 elseif 判断逻辑的干扰，在第 0.1.2 版本中增加了缩减冗余不可见字符  
&#160; &#160; &#160; &#160;考虑到可能存在有 <font color='pink'>***「注释、字符串」***</font> 对 关键词计数的干扰，在第 0.1.3 版本中增加了 删除注释块、删除注释行、删除字符串功能    
&#160; &#160; &#160; &#160;考虑到可能存在有 <font color='pink'>***「char a=‘ ” ’ 单字符」***</font> 对 关键词计数的干扰，在第 0.1.4 版本中增加了 删除 '"'(即 「"」的单字符)功能 
```python
# 文件预处理重点
data_shorted = re.sub(r"\/\*([^\*^\/]*|[\*^\/*]*|[^\**\/]*)*\*\/",
                          "", data)    # 删除注释块
data_shorted = re.sub(r"\/\/[^\n]*", "", data_shorted)      # 删除注释行
data_shorted = re.sub(r"\'\"\'","",data_shorted)            # 删除 '"'(即 「"」的单字符)
data_shorted = re.sub(r"\"(.*)\"", "", data_shorted)        # 删除字符串
data_shorted = re.sub(r"[ \f\r\t\v]+"," ",data_shorted)     # 缩减多余不可见字符
data_shorted = re.sub(r"[\n]+","  ",data_shorted)           # 改回车为双空格
data_listed = re.split(r"\W", data_shorted)   # 转化为列表
```
## **关键词处理**
>&#160; &#160; &#160; &#160;这个函数写得比较一气呵成，逻辑上没有出现问题，由于该函数较长，进行程序规范化修改过程中花费时间较长，给我敲响了警钟。实现方法上而言并无困难，过程中也是重温了一下 Python 的一些基础语法，同时也解决了一些之前学习 Python 时遇到的小困惑。  

><font color='OrangeRed'>**问：**</font>只有一个元素的列表可以索引<font color='OrangeRed'>**[-1]**</font>吗？  
<font color='CornflowerBlue'>**答：**</font>可以，同样索引倒数第一个元素，在只有一个元素的列表中索引的就是唯一那个元素。 

```python
# 关键字处理重点
if word == 'switch':  		# 若为 switch 则 switch_count++ 
    switch_count = switch_count + 1 
    case_count.append(0)  	# 且 case_count 增加一个元素  
            
elif word == 'case':		# 若是 case
	case_count[-1] += 1		# case_count 对应元素加1

elif word == 'if':   		# 若是 if 则压入堆栈
	if_stack.append('if')

elif word == 'else' :   			# 出现 else

	if data_listed[i+1] == 'if':   	# 当为 elseif
		if_stack.append('elif')  	# 压栈 elif
        key_count = key_count + 1  	# 计数下一个 if 
        data_iter.__next__()  		# 跳过下一个 if

    else :  # 若只是 else

        elifFlag = False  	# 初始化 elifFlag 标志

		while if_stack[-1] == "elif":
        	elifFlag =True
            if_stack.pop()  # elif 出栈
			if_stack.pop()  	# if 出栈

        	if elifFlag :   	# 根据 elifFlag 增加计数
                if_elif_else_count += 1
		    else :
		        if_else_count +=  1
```
## **输出模式**
><font color='OrangeRed'>**问：**</font>如何不适用循环打印出列表且不带方括号?  
<font color='CornflowerBlue'>**答：**</font>print(*ListName, seq=' ')

```python
def final_print(mode):
	if mode >= 1:
    	print("total num:", key_count)
	if mode >= 2:
    	print("switch num:", switch_count)
    	print("case num:", end=' ')
    	if switch_count > 0:
        	print(*case_count, sep=' ') 
    	else:
        	print(0)
	if mode >= 3:
    	print('if-else num:', if_else_count)
	if mode >= 4:
    	print('if-elseif-else num:', if_elif_else_count)
```

# 性能分析
## 样例结果and单元测试
>&#160; &#160; &#160; &#160;测试用例使用老师给出 key.c，并进行了一定修改，包含有 “代码换行不规范”（甚至完全不换行）、“代码不安规范缩进”（包括全部代码贴边写），“char字符有「"」”等各类特殊情况，对于覆盖率高达 <font color="orangered">99%</font> 还是有点小兴奋的。   

![image](./image/coverage_report.png)

## 运行时间
>&#160; &#160; &#160; &#160;采用 line_profiler 对 test_keyCount.py 主函数部分进行测试，结果在意料之内，预处理函数 data_pretreat 时间占比最大，推测原因是这段函数中包含了文件输入、格式转换和大量re正则表达式函数等复杂函数调用

![image](./image/time_test.png)

# 学习心得
## 困难与认识
>&#160; &#160; &#160; &#160;本次编程学习过程中主要遇到了三个问题。    
&#160; &#160; &#160; &#160;首先是规范化编程意识不强，导致编码一会，改码半天的情况，最初的代码又丑又不利于维护，根据PEP8编码规范形成自己的风格后终于使得代码可以拿出来见人了，也方便检查代码纰漏。    
>&#160; &#160; &#160; &#160;其次就是认识到准确理解客户需求的重要性，如果曲解或者一知半解地开始编码，很多时候只是在做无用功，比如我想了一两个晚上没有else的情况下如何判断if-else和if-elseif-else的结束。    
>&#160; &#160; &#160; &#160;最后也是最重要的，要熟练使用好已经有的工具，Python 是是一个功能强大的编程语言，本身携带的包和工具已经足以实现大多数功能，本次编程中就使用到了 re 正则表达式包。
## 正则表达式
>推荐正则表达式学习网站：[菜鸟教程 python 正则表达式教程](https://www.runoob.com/python/python-reg-expressions.html)    
正则表达式在线检测工具：[菜鸟工具 正则表达式在线检测](https://c.runoob.com/front-end/854/)