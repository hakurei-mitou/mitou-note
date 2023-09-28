# Jupyter

文件后缀名：`.ipynb`（IPython Notebook）（IPython 表示 Interactive Python）

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

## 魔法命令

（magic command）

- 运行 Shell 命令

  - `!`  + Shell 命令。

    在新的 shell 进程中执行。

  - `%`  + Shell 命令。

    在当前 shell 进程执行。（切换当前目录需要使用 `%cd dir`）
  
- 修改工作目录

```python
%pwd  # look at the current work dir
%cd   # change to the dir you want 
```

- 在 python 文件中，可以使用 `# %%` 指定从当前行开始作为一个 notebook Cell 。

## 显示视频

```python
# 将 mp4 编码为 base64 然后用 HTML 显示。

def show_mp4(url, width):

    from IPython.display import HTML
	from base64 import b64encode
    
    mp4 = open(url,'rb').read()
    data_url = "data:video/mp4;base64," + b64encode(mp4).decode()

    HTML("""
    <video width=400 controls autoplay loop>
          <source src="%s" type="video/mp4">
    </video>
    """ % data_url)
```

## 文件转换

- 利用 nbconvert ：

```shell
# 安装 nbconvert
pip install nbconvert

# 转换为 .py 脚本（其它语言也可以转换为对应的单个脚本文件）
jupyter nbconvert --to script notebook.ipynb

# 转换为 html 格式
jupyter nbconvert --to html notebook.ipynb

# 其它格式：
--to markdown
--to latex
```

- 利用 IDE 提供的导出功能

	在界面上找到 Export 选项。

	- 导出为 python 脚本。
	- 导出为 html 。
	- 导出为 pdf 。

## 从命令行执行

```shell
# 执行 notebook.ipynb 
jupyter nbconvert --execute notebook.ipynb

# 执行 notebook.ipynb ，并将结果保存到 output.ipynb
jupyter nbconvert --to notebook --execute notebook.ipynb --output output.ipynb

# 用结果替换当前文件
jupyter nbconvert --to notebook --execute --inplace notebook.ipynb

# 使用 ipython
ipython -c "%run notebook.ipynb"

# 使用 runipy
runipy notebook.ipynb
```

## 杂项

- 隐藏单元输出

	双击输出内容左侧。

- 清除单元输出

	在输出内容左侧的选项中寻找该功能。

- debug

	点击单元格左上角 debug cell。
