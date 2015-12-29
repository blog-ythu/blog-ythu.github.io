---
layout: post
comments: true
title: "DOS useful commands"
excerpt: "Several DOS useful commands to make life easier. :)"
date: 2012-04-06 11:00:00
mathjax: true
---

<!-- add TOC here -->
<div id="genTocHere"></div>

## Upgrade the structure of all files in current folder (including sub-folders and sub-sub-folders...) by one level
```bat
for /r %x in (*.*) do move "%x" "%x"/../..
```

## Get all file names (absolute paths) in current folder to txt
```bat
Dir /s /b *.* > list.txt
```

PS: Remove `/s` if only want to keep file names (not the full paths).
