---
title: gdb 相关命令
date: 2017-11-23 01:50:04
tags: [linux,gdb]
---

参考教程：[Linux gdb调试器用法全面解析](http://blog.csdn.net/21cnbao/article/details/7385161)，[gdb断点（四）删除](http://blog.csdn.net/yangzhongxuan/article/details/6901477)，[gdb: how to print the current line or find the current line number?](https://stackoverflow.com/questions/14581837/gdb-how-to-print-the-current-line-or-find-the-current-line-number)，[GDB Error: "The program is not being run"](http://www.gdbtutorial.com/gdb-error-program-not-being-run)

编译时：

```shell
gcc -g test.c -o test
```

进入调试：

```shell
gdb test.out
```

## list 命令

缩写 l

- 跟行号
- 跟函数名（run时停止在函数首行）

<!--more-->

## run 命令

缩写 r

- 停止在断点处
- 指定参数路径等

## break 命令

缩写 b

- 跟行号

- 跟函数名

- b +*offset* / -*offset*，在当前行的前面或后面 offset 行停止

- break *file*:*line_num*，在源文件的某行停止

- break *add，在 add 内存地址停止

- break ... if *condition* ，条件断点，中间...代表是上述命令

---

- 查看断点

  info break *break_num*

  缩写 `i b`

- 删除断点

  delete *break_num*

  缩写 d *n*

- 删除行断点

  clear *func*	**clear 无缩写**

  clear *file*:*line_num*

  clear *line_num*

## 单步命令

### next 命令

缩写 n

- next *count*，执行 count 条指令

- **<span style="color: #d94f8a"> 重复上一条命令：按下回车 </span>** 。即每一次重复执行上一条指令时，不需要重复输入，只需回车即可。

  #### 附：报错"The program is not being run."

  程序没有自动开始运行，此时输入 `start` 即可开始调试，注意不要使用 `run` 命令。


### step 命令

缩写 s

- 可以进入函数
- step *count*

### finish 命令

缩写 f

- 执行程序至当前函数完成，打印堆栈信息和返回值等

### until 命令

缩写 u

- 运行程序至推出循环体

### stepi 和 nexti 命令

缩写分别为 si 和 ni

- 单步执行机器指令


## frame 命令

缩写 f

- 显示当前所在的行以及相关信息


## continue 命令

缩写 c

- 程序停止后，continue 执行到结束或断点处，或进行 watch 的变量变化处
- continue *ignore-break-num*，忽略多少断点

## print 命令

缩写 p

- print /\<f\> *exp*，由 f 指定输出格式

  - x 十六进制
  - d 十进制
  - u 十六进制无符号整型
  - o 八进制
  - t 二进制
  - c 字符格式
  - f 浮点格式

- 动态数组 

  ```c
  int *array = (int *)malloc (len * sizeof (int))
  ```

  查看该动态数组的值：`p *array@len`

- p \{*type*\} *add* ，查看地-址 add 处 type 类型的值

- **display** 命令，设置程序停止时自动显示的变量

- print x = 4，修改变量值

## watch 命令

缩写 w

- 观察的变量变化时停止程序
- watch *exp* ，观察表达式或变量
- rwatch *exp*，表达式或变量被读取时，停止程序
- awatch *exp*，表达式或变量被读或写时，停止程序
- info watchpoints，列出所有观测点

