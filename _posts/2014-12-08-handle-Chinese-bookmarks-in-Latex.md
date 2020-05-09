---
layout: redirect
comments: true
title: "Handle Chinese bookmarks in Latex"
excerpt: ""
date: 2014-12-08 11:00:00
mathjax: true
redirect: https://blog-tommyhu.github.io/2014/12/08/Handle-Chinese-bookmarks-in-Latex/
---

We often encounter problems of showing Chinese bookmarks during using Latex, take the following MWE, i.e. `main.tex` as an example:

```tex
\documentclass{article}
\usepackage[english]{babel}
\usepackage{CJK}
\usepackage[CJKbookmarks]{hyperref}
\begin{document}
\begin{CJK*}{GBK}{song}
\section{测试}
基于CJK的可以搜索、复制、有书签的中文PDF。
\end{CJK*}
\end{document}
```

When using `pdflatex` to compile, the Chinese bookmarks will not show correctly, though there will be no problem showing Chinese in the body text.

Correct way to handle is:
1. Download and extract `gbk2uni.exe` from https://bitbucket.org/herohuyongtao/files/downloads/gbk2uni.zip to the Latex source folder.
2. Before final run of `pdflatex` command, dos run `gdk2uni.exe main.out`
3. Run `pdflatex` again. Done!

---
It's possible to simplify the 2nd step by adding a one-click button to the Latex editor, i.e. WinEdt's toobar. The steps are:
1. Copy `gbk2uni.exe` to `C:\CTEX\MiKTeX\miktex\bin`.
2. Create file `gbk2uni.bat` with the following content:

    ```bat
    gbk2uni %1.out
    pause
    ```
3. Put this file to `..\Ctex\WinEdt\Bin\` (create `Bin` folder if not existing).
4. In **WinEdt**: `Options –> Option Interface… –> Menu and Toolbar… –> MainMenu –>` double click to open `MainMenu.ini` file `–>` add the following content before `END="&Accessories" –>` right click `MainMenu –> load script (F9)`.

    ```ini
    ITEM="GBK to Unicode"
        CAPTION="gbk2uni"
        IMAGE="Notepad"
        SAVE_INPUT=1
        MACRO=:Run('%B\Bin\gbk2uni.bat %N');
        REQ_FILTER="%P\%N.out"
    ```
5. In **WinEdt**: `Options –> Option Interface… –> Menu and Toolbar… – > Toolbar –>` double click to open `toolbar.ini` file –> add `BUTTON="GBK to Unicode"`, e.g. before `BUTTON="MetaFont" –>` right click `Toolbar –> load script (F9)`.
6. Done! There will be a `gbk2uni` button (with a **Notepad** icon, change in step-4 if you want to use other icons) before `MetaFont` button.

Up to now, the way to generate the PDF with correct Chinese bookmarks can be, i.e ***all by simply clicking the coresponding buttons***: `pdflatex -> pdflatex –> gdk2uni -> pdflatex`.

---
## References:
1. Example from: http://bbs.ctex.org/forum.php?mod=viewthread&tid=64270
2. Adding `gbk2uni` button to WinEdt is based on: http://blog.sina.com.cn/s/blog_48f3a1cd0101mon3.html
