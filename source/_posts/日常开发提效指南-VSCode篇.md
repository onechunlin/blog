---
title: 日常开发提效指南-VSCode篇
date: 2025-04-26 18:13:12
tags:
---

# 前言
VSCode 作为前端最常用的编辑器，分享一些日常开发中 VSCode 的提效技巧

# 快捷键
这里有个记忆技巧，光标跳转时 option 一般和单词相关，command 和整个文件相关

## 光标移动
```bash
command + left/right       #移动到行首/尾
command + up/down          #移动到文件顶部/底部
option + left/right        #（以单词为单位）向左/右移动
 
command + 鼠标左键  # 转到定义
ctrl -           # 后退到光标前一个位置
ctrl shift -     # 前进到光标前一个位置
```

## 选中区域
```bash
command + L                 #选中当前行
command + option + up/down  #同时选中多行
option + 鼠标左键            # 手动选择多个光标 
shift + left/right          #（以单个字符为单位）向左/右选中
shift + option + left/right #（以单词为单位）向左/右选中
shift + up/down             #（以行为单位）向上/下选中
shift + command + up/down   #从当前位置选中到文件顶/底部
```

## 复制、移动内容
```bash
option + 上/下键            #将当前代码移动到上/下一行
option + shift + up/down   #复制当前行到上方/下方
```

## 删除内容
```bash
shift + command + k          #删除当前行：
command + backspace          #删除光标前所有字符
```

## 文件相关
```bash
command + f          # 查找
command + shift + f  # 全局查找
 
command + p          # 快速打开最近打开的文件
ctrl + r             # 快速打开最近打开的文件夹/项目
```

# 自定义代码片段

## 添加 Snippets
1. 进入首选项，选择配置用户代码片段（Code => 首选项 => 配置用户代码片段）
![](/assets/daily-efficiency/shouxuanxiang.png)
2. 选择代码片段作用类型，这里有**全局/项目/语言** 3 种，这里建议选择全局，便于各个项目使用，语言层面可以使用 snippets 的语法来限制（后面会讲到）
![](/assets/daily-efficiency/daimakuai1.png)
3. 选择后进入 Snippets 的配置文件，这里以一个常见的函数组件模版为例
![](/assets/daily-efficiency/daimakuai2.png)

## 语法解释
- 上例中配置文件的 key 为输入前缀时右侧的提示信息
![](/assets/daily-efficiency/daimakuai3.png)
- prefix 是触发的前缀，可以使用数组指定多个
- body 是插入到编辑器中的内容，注意换行需要使用一个空字符串
- description 是描述
![](/assets/daily-efficiency/daimakuai4.png)
- scope 是生效的语言，不指定的话就是所有语言都生效

## 使用变量

### 光标位置
上面的例子实际的使用效果
![](/assets/daily-efficiency/gbwz1.gif)
初始化出来的组件名 Demo 很多时候都需要改，这里可以使用 ${数字} 来指定光标的位置
![](/assets/daily-efficiency/gbwz2.png)
![效果](/assets/daily-efficiency/gbwz3.gif)

### Snippet 变量
除了使用数字来指定光标位置外，还支持以下变量
1. 文档相关：
| 变量 | 变量含义 |  
| :---: | :---: |  
| TM_SELECTED_TEXT | 当前选定的文本或空字符串 |  
| TM_CURRENT_LINE | 当前行的内容 |  
| TM_CURRENT_WORD | 光标下的单词内容或空字符串 |  
| TM_LINE_INDEX | 基于零索引的行号 |  
| TM_LINE_NUMBER | 基于单索引的行号 |  
| TM_FILENAME | 当前文档的文件名 |  
| TM_FILENAME_BASE | 当前文档没有扩展名的文件名 |  
| TM_DIRECTORY | 当前文档的目录 |  
| TM_FILEPATH | 当前文档的完整文件路径 |  
| CLIPBOARD | 剪贴板的内容 |  
| WORKSPACE_NAME | 已打开的工作空间或文件夹的名称 |


2. 当前日期和时间：
| 变量 | 变量含义 |  
| :---: | :---: |  
| CURRENT_YEAR | 当前年份 |  
| CURRENT_YEAR_SHORT | 当前年份的最后两位数 |  
| CURRENT_MONTH | 月份为两位数（例如'02'） |  
| CURRENT_MONTH_NAME | 月份的全名（例如'June'）（中文语言对应六月） |  
| CURRENT_MONTH_NAME_SHORT | 月份的简称（例如'Jun'）（中文语言对应是6月） |  
| CURRENT_DATE | 这个月的哪一天 |  
| CURRENT_DAY_NAME | 当天是星期几（例如'星期一'） |  
| CURRENT_DAY_NAME_SHORT | 当天是星期几的简称（例如'Mon'）（中文对应周一） |  
| CURRENT_HOUR | 24小时时钟格式的当前小时 |  
| CURRENT_MINUTE | 当前分 |  
| CURRENT_SECOND | 当前秒 |

3. 要插入行或块注释，请遵循当前语言：
| 变量 | 变量含义 |  
| :---: | :---: |  
| BLOCK_COMMENT_START | 输出：PHP `/*` 或 HTML 格式 `<!--` |  
| BLOCK_COMMENT_END | 输出：PHP `*/` 或 HTML 格式 `-->` |  
| LINE_COMMENT | 输出：PHP `//` 或 HTML 格式（HTML单行注释通常使用`<!-- -->`，没有专门的单行注释符号，这里可能是指直接写在HTML标签中的注释） |


## 使用正则
大多时候初始化一个组件时，组件名称和当前文件名都相关，如 components/Divider.tsx 下的组件名规范写法就是 Divider
![](/assets/daily-efficiency/zz1.png)
![效果展示](/assets/daily-efficiency/zz2.gif)


语法格式像下面这样：
**${«variable or placeholder»/«regex»/«replacement string»/«flags»}**
  
很像JavaScript中的 [String.protoype.replace()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace) 。其中参数的作用如下:
- «regex»: 这是一个与变量或占位符的值匹配的正则表达式。支持JavaScript regex语法。
- «replacement string»: 这是用来替换匹配到的内容的字符串。它可以引用«regex»中的捕获组，执行大小写的格式化（使用特殊的标记函数：/upcase, /downcase, 和 /capitalize），以及执行条件插入。查看 TextMate Replacement String Syntax 了解更多信息。
- «flags»: Flags是传递给正则表达式使用的。可以使用 JavaScript regex flags 中的标志:
g：全局搜索
i：大小写敏感
m：多行搜索
s：允许 . 匹配新行的字符

# 推荐插件
## Code Spell Checker
检查代码单词拼写https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker

## Better Comments
可以高亮注释
https://marketplace.visualstudio.com/items?itemName=aaron-bond.better-comments
![](/assets/daily-efficiency/cj1.png)

## Auto Rename Tag
自动重命名 tag
https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag
![](/assets/daily-efficiency/cj2.gif)


## Turbo Console Log
自动添加 console 
![](/assets/daily-efficiency/cj3.gif)

使用方式：
选中要打印的变量
ctrl + alt/option + L
https://marketplace.visualstudio.com/items?itemName=ChakrounAnas.turbo-console-log