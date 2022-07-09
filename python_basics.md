# learn python as a C++ programmer

- <https://myprogrammingnotes.com/learn-python-c-programmer.html>
- <https://www.programmerall.com/article/1875127412/>

*************************************

## Index

1. [basics](#basics)
2. [object-oriented in Python](#object-oriented-in-python)
3. [调用路径](#calling_path) \
   [3.1 sys.path](#sys.path) 

As a C++ programmer, you do not need to buy a python book to learn it from the 
beginning. Bearing in your mind some differences between C++ and python is 
enough for you to read the source code written in python.


## 1 basics

### - code block
Instead of form a code block using brackets, python uses indent to form a code 
block,i.e.,the code lines with the same indent in front of the lines form a 
code block. The indent can be space(s), tab(s), or both, but they need to be 
same for all lines in the code block.

### - comment
Python use “#” to begin a comment, use ”’ for multi-line comments.

### - string
Python use ‘, “, or ”’ to denote strings. ”’ can be used to quote multi-line 
string including new lines in it.

### - end with :
if, while, def, etc. end with :

### - list, tuple, dict
list [1,2,3] is the counterpart of array in C++, but can contain different types 
of data.

tuple (1,2,3) is almost the same as list, but is read-only.

dict {1:”a”,2:”b”,3:”c”} is like map in C++

part of string(sub-string), list, tuple, can be obtained using the form like 
a[2:6], which means the third to the sixth elements,i.e., a[2],a[3],a[4],a[5], 
not including a[6]

### - module
A module (a .py file) contains variables and functions, which can be accessed 
in other scripts. To access the variables and functions in a module, import 
the module first(import modulename), then access with modulename.functionname 
or modulename.variablename.

From modulename import functionname will introduce functionname to the current 
script, so you can directly use function name without the modulename prefix.

import can be also used on package, i.e., import packagename. This will 
load/execute the file `__init__.py` in the folder named packagename. 
`__init__.py` usually imports other modules(.py files) in that folder.

### - variable
Python variables do not have a type so they do not need delaration. They can 
store any type of value. A variable can have a value of one type this time and 
have a value of another type that time.

The member variables of object need not be declared when defining the class. 
They are defined using object.varname=value or self.varname=value. If the 
variable name begins with `__(self.__varname=value)`, the variable is a private 
one and can not be accessed outside class. The variables and methods with `__` 
both before and after have special meanings.

The variables declared when defining the class are the same as the static 
members in C++, and they are initialed when declared.

### - class
All methods in a class must have “self” as their first parameter.

contructor of a class has the fixed name `__init__`, the descontructor also 
has the fixed name `__del__`

create an instance of a class using obj=classname();

### - function
Like javascript, python allows to define function inside function, so called closure.

### - iteration
Python does not have “for(i=0;i<100;i++)”. The only form of for loop is 
“for var in list”. You can iterate an array by 
```python
for i in range(len(myarray))
    print myarray[i]
```

### - condition
The expression in if statement is not enclosed by curl brackets. There is a 
semicolon at the end of if, while statements.



[Back to index](#index)


## 2 Object-oriented in Python

<https://www.programmerall.com/article/1875127412/>

[Back to index](#index)


## 3 调用路径 <span id="calling_path"></span>

```python
test
    |--test1
        |--python1.py
            import os
            import sys
            project_path = os.path.dirname(os.getcwd()) # 获取包的根目录
            sys.path.append(project_path)   # 将根目录加入 sys.path
            sys.path.append(os.getcwd())

            from test2.python2 import print2    # 这样，就能从任何路径引入任何项目包了
            from test3.test31 import python31

            python31.print31()
            print2()

    |--test2
        |--python2.py
            def print2():
                print("this is python2")
    |--test3
        |--test31
            |--python31.py
                def print31():
                    print("this is python31")
```

[Back to index](#index)


### 3.1 sys.path

Sys is a built-in Python module that contains parameters specific to the system i.e. it 
contains variables and methods that interact with the interpreter and are also governed 
by it. 

#### sys.path
sys.path is a built-in variable within the sys module. It contains a list of directories 
that the interpreter will search in for the required module. 

When a module(a module is a python file) is imported within a Python file, the interpreter 
first searches for the specified module among its built-in modules. If not found it looks 
through the list of directories(a directory is a folder that contains related modules) 
defined by **sys.path**.

#### Initializing sys.path 
There are three ways to specify a path :

- **DEFAULT** - By default, the interpreter looks for a module within the current directory. 
To make the interpreter search in some other directory you just simply have to change the 
current directory. The following example depicts a default path taken by the interpreter:
```python
# importing module
import sys
  
# printing all directories for 
# interpreter to search
sys.path
```

**Output:**
```batch
['', 'd:\\ProgramData\\Anaconda3\\python39.zip', 'd:\\ProgramData\\Anaconda3\\DLLs', 
'd:\\ProgramData\\Anaconda3\\lib', 'd:\\ProgramData\\Anaconda3', 
'C:\\Users\\jiche\\AppData\\Roaming\\Python\\Python39\\site-packages', 
'd:\\ProgramData\\Anaconda3\\lib\\site-packages', 
'd:\\ProgramData\\Anaconda3\\lib\\site-packages\\win32', 
'd:\\ProgramData\\Anaconda3\\lib\\site-packages\\win32\\lib', 
'd:\\ProgramData\\Anaconda3\\lib\\site-packages\\Pythonwin']
```

- **THROUGH ENVIRONMENT VARIABLES** - An environment variable that contains the path an interpreter 
can take while looking for modules can be employed. Once set, it hints interpreter with directories 
to locate a module.  The following example shows how this can be done.

Input in the Anaconda Prompt:
```batch
Set PYTHONPATH=pathFolder
```
上面这行是把当前目录下的 Desktop 目录设置为PYTHONPATH, 下面我们假设当前目录为 D:

Input in the Anaconda Prompt:
```batch
python
```
this will start up python environment, and we type:

```python
# importing module
import sys
  
# printing all directories for 
# interpreter to search
sys.path
```

**Output:**
```batch
['', 'D:\\pathFolder', 
'd:\\ProgramData\\Anaconda3\\python39.zip', 'd:\\ProgramData\\Anaconda3\\DLLs', 
'd:\\ProgramData\\Anaconda3\\lib', 'd:\\ProgramData\\Anaconda3', 
'C:\\Users\\jiche\\AppData\\Roaming\\Python\\Python39\\site-packages', 
'd:\\ProgramData\\Anaconda3\\lib\\site-packages', 
'd:\\ProgramData\\Anaconda3\\lib\\site-packages\\win32', 
'd:\\ProgramData\\Anaconda3\\lib\\site-packages\\win32\\lib', 
'd:\\ProgramData\\Anaconda3\\lib\\site-packages\\Pythonwin']
```
我们注意到当前目录为 D: 时，PYTHONPATH 中加上了 'D:\\pathFolder'。

- **APPENDING PATH** - append() is a built-in function of sys module that can be used with path 
variable to add a specific path for interpreter to search. The following example shows how this can 
be done.

```python
# importing module
import sys
  
# appending a path
sys.path.append('C:/Users/Vanshi/Desktop')
  
# printing all paths
sys.path
```

**Output:**
```batch
['', 'd:\\ProgramData\\Anaconda3\\python39.zip', 'd:\\ProgramData\\Anaconda3\\DLLs', 
'd:\\ProgramData\\Anaconda3\\lib', 'd:\\ProgramData\\Anaconda3', 
'C:\\Users\\jiche\\AppData\\Roaming\\Python\\Python39\\site-packages', 
'd:\\ProgramData\\Anaconda3\\lib\\site-packages', 
'd:\\ProgramData\\Anaconda3\\lib\\site-packages\\win32', 
'd:\\ProgramData\\Anaconda3\\lib\\site-packages\\win32\\lib', 
'd:\\ProgramData\\Anaconda3\\lib\\site-packages\\Pythonwin', 
'C:/Users/jiche/Desktop']
```

Note that the first string returned by path is always empty this is to indicate the 
interpreter to check in the current directory.

[Back to index](#index)

