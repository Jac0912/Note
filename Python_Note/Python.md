# Python

pip install +包名

## 行和缩进

Python代码块不需要使用**{}**控制类、函数、逻辑判断,用缩进来写模块

**\ **可将一行语句分为多行显示

**[]、{}、（）**不需用多行连接符

可以用**'  '、"  "、"""   """**表示字符串

```python
word = 'word'
sentence = "这是一个句子。"
paragraph = """这是一个段落。
包含了多个语句"""              (可由多行组成，作为注释)
```

单行注释由**#**开头

多行注释：

```python
'''
这是多行注释，使用单引号。
这是多行注释，使用单引号。
这是多行注释，使用单引号。
'''

"""
这是多行注释，使用双引号。
这是多行注释，使用双引号。
这是多行注释，使用双引号。
"""
```

## venv

```shell
python -m venv venv #创建虚拟环境
source activate    #激活虚拟环境
deactivate         #退出虚拟环境
pip list           #列出所有包的情况
pip freeze > requirements.txt                                                              #Output installed packages in requirements format.
pip install -r requirements.txt
                   #别人安装相同的包
```

## pip

```shell
#windows下更新pip
python.exe -m pip install --upgrade pip
```

