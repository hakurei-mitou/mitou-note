# IntelliJ IDEA

## 阅读快捷键

- `Alt + <-` 

	返回上一个文件位置。（粒度大）

- `Ctrl + Alt + <-` 

	返回上一个光标位置。（粒度小）
	
- `Ctrl + Alt + B`

  跳转到方法或接口的实现。

## 提示操作快捷键

 `Alt + Enter`:

- 警告，错误提示位置按

	可弹出候选操作选项，快速操作。

- 输入函数后，在行末按

	可快速接收返回值。

- 类名按

	可快速选择实现某个方法。

Ctrl + p ：

弹出参数提示。

## 多光标编辑

IDEA 里的 IdeaVIM 插件的 Ctrl + V（块选择模式）不能插入，可用多光标编辑替代：

- 连续行选中

	按住 Alt + 鼠标左键滑动（可通过 Ctrl + right 等方式进一步控制各光标的位置）。

- 多处选中

	按住 Ctrl + Shift + Alt + 左键点击。

## 快捷输出

```txt
sout
Prints a string to System.out

soutv
Prints a value to System.out

souf
Prints a formatted string to System.out

soutm
soutm Prints current class and method names to System.out

soutp
Prints method parameter names and values to Syste.out
```

## 代码结构修改快捷键

- 代码包裹

	Ctrl + Alt + t ，将选中的代码行包裹 try catch ，if else 等包裹块中。
	
- 抽取代码为方法

  Ctrl + Alt + m，将选中的代码抽取到一个方法中。
