# 《C++面向对象软件设计》(Object-Oriented Software Design in C++) —— LaTeX 工程(AI翻译)

## 目录结构

```
latex_source/
├── main.tex                    根文件：读入导言区 + \subfile{book/index.tex}
├── build.bat                   一键编译脚本（纯 ASCII，勿加中文注释）
├── README.md                   本文件
├── .gitignore                  排除 _minted/、*.aux 等编译垃圾，仓库只留源码+图片
├── book/
│   ├── ccs.tex                 导言区：字体、版式、全部自定义命令与环境  ← 改版式改这里
│   ├── index.tex               封面、扉页、目录、全书章节顺序  ← 调章节顺序改这里
│   ├── manning-pygments-style.tex   minted 代码块配色（注册为 manning 样式）
│   └── content/
│       ├── front/              02-title + 03-copyright 版权页（EPUB 里被拆成上下两半：
│       │                       Manning 标识、liveBook 讨论入口、版权与无酸纸声明、
│       │                       编辑名单、ISBN）、05-preface 前言、06-acknowledgments 致谢、
│       │                       07-about-this-book 关于本书、08-about-the-author 关于作者、
│       │                       09-about-the-cover 关于封面插图
│       ├── part/               p1–p5：五个部分的扉页（「第 N 部分」灰带 + 导语）
│       ├── main/               01–16：正文十六章
│       └── back/               01-index：索引（双栏排版）
├── fonts/                      JetBrainsMonoNL 四款字重（Consolas 的兜底字体 + ↪ 字形回退）
└── images/                     cover.jpg 封面 + 255 张插图（CH<章>_…_Mak.png）
                                + Manning_M_small.png / Manning_copyright.png / Mak_Author-Photo.png
```

> 本书没有附录，正文之外只有一篇索引（`back/01-index.tex`，双栏）。
> 章节顺序由 `book/index.tex` 逐行控制：`\myPart`（部分页）/ `\myChapter`（编章）
> / `\myFront`（进目录的前置与索引）/ `\myFrontNoToc`（不进目录的版权页）；
> 这些命令的版式（灰带、巨型章号、钢蓝横线…）都在 `ccs.tex` 里定义。

全书按 **5 个部分、16 章** 组织，与英文原版一致：

| 部分 | 标题 | 对应章节 |
| --- | --- | --- |
| 第 1 部分 | 引言 | 第 1–2 章 |
| 第 2 部分 | 设计正确的应用 | 第 3–4 章 |
| 第 3 部分 | 把应用设计好 | 第 5–7 章 |
| 第 4 部分 | 设计模式解决应用架构问题 | 第 8–14 章 |
| 第 5 部分 | 其他设计技术 | 第 15–16 章 |

> 正文前另有 5 篇前置文字：前言、致谢、关于本书、关于作者、关于封面插图。
> 上游生成流程（EPUB → 抽取译文单元 → 分批翻译 → 生成 `.tex`）放在项目根的
> `../work/` 下，脚本见 `../work/tools/`；本目录只保存排版工程本身。

## 编译

需要 **TeX Live 2026**（自带 `latexminted` 0.7+）与 **Python 3**（`latexminted` 是一个被
`xelatex` 调起的 Python 脚本，代码块用它与 Pygments 做语法高亮）。

正文中文使用**思源宋体（Source Han Serif SC）**，需自行安装
（[官方发布页](https://github.com/adobe-fonts/source-han-serif/releases)下载 Windows 版 OTF 即可）；
未安装时 `ccs.tex` 里的 `\IfFontExistsTF` 会自动回退到系统自带的华文宋体（STSong），
编译不会中断，仅字形与字重略有差异。

正文代码（行内 `\texttt` 与代码清单）使用 **Consolas**（Windows 自带，无需安装）。
Consolas 缺少 `↪`（续行标记）与 `㉑–㉚`，这两处分别回退到随工程分发的 JetBrains Mono NL
和微软雅黑；若在非 Windows 机器上、连 Consolas 也没有，`\IfFontExistsTF` 会自动整体回退到
JetBrains Mono NL，同样不会中断编译。

```bat
build.bat                          REM Windows 双击即可
```

> 如果环境配置遇到问题或者编译失败可以直接到[releases](https://github.com/jnulzl/SoftwareDesignCpp-CN/releases)下载最新的pdf文件

或手工执行（**必须加 `-shell-escape`**；目录与书签需连跑三遍才完全稳定）。
**注意 `TEXMF_OUTPUT_DIRECTORY` 必须设成项目根的绝对路径**，否则代码块会全部高亮失败

```bat
set TEXBIN=D:\ProgramData\texlive\2026\bin\windows
set PYTHON=D:\anaconda3
set PATH=%PYTHON%;%TEXBIN%;%PATH%
set PYTHONPATH=
set SELFAUTOLOC=%TEXBIN%

REM 【关键】必须设成【项目根的绝对路径】（这里按你的实际路径改）
set TEXMF_OUTPUT_DIRECTORY=$ABS_ROOT_PATH\SoftwareDesignCpp-CN
set TEXMFOUTPUT=%TEXMF_OUTPUT_DIRECTORY%

xelatex -shell-escape -interaction=nonstopmode -synctex=1 main.tex
xelatex -shell-escape -interaction=nonstopmode -synctex=1 main.tex
xelatex -shell-escape -interaction=nonstopmode -synctex=1 main.tex
```

> 用 `build.bat` 时这些都不用手动设——脚本里用 `%~dp0` 自动填好了。

> ⚠ **`build.bat` 是纯 ASCII 文件，请勿在其中加入中文注释。**
> 批处理由 `cmd.exe` 逐行解析，而 cmd 按「当前代码页」（简体中文 Windows 默认
> GBK/936）读取文件。若文件是 UTF-8，中文注释会变成乱码，且乱码行会被当作
> 命令执行，报出一堆
> `'锛歍eX' 不是内部或外部命令，也不是可运行的程序或批处理文件`。
> 所以该脚本的注释一律用英文，中文说明放在本 README 里。


## 官方代码

[SoftwareDesignCpp](https://github.com/RonMakBooks/SoftwareDesignCpp)