# Jupyter

文件后缀名：`.ipynb`（IPython Notebook）

可在 VS code 内使用。

Ctrl + Shift  + P 搜索 jupyter 新建文件即可。

分为 命令模式 和 编辑模式。

可点击图标打开变量预览器。

## 基本操作

Cell 命令模式目前支持的常用 Jupyter Notebook 快捷键

| 快捷键 | 含义 |
| - | - |
| Esc | 进入 Cell 命令模式 |
| Enter | 转入编辑模式 |
| Shift + Enter | 运行本单元，选中或插入（最后一个Cell的时候）下个单元 |
| Ctrl + Enter | 运行本单元 |
| Alt + Enter | 运行本单元，在其下方插入新单元 |
| y | 单元转入代码状态 |
| m |单元转入markdown状态 （目前尚不支持 R 原生状态） |
| Up, k | 选中上方单元 |
| Down, j | 选中下方单元 |
| a | 在上方插入新单元 |
| b | 在下方插入新单元 |
| dd | 删除选中的单元 |
| Shift + L | 显示行号 |
| f | 查找替换 |
| z | 撤销编辑 |

## 杂项

- 隐藏单元输出

	双击输出内容左侧。

- 清除单元输出

	在输出内容左侧的选项中寻找该功能。

- 运行 Shell 命令

	- `!`  + Shell 命令。
	
	  在新的 shell 进程中执行。
	
	- `%`  + Shell 命令。
	
	  在当前 shell 进程执行。（`%cd dir`才能切换当前目录）
