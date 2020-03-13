---
title: 很牛掰的格式转换器pandoc
date: 2015-04-08 13:59
Author: Neo
categories: 技术流
tags:
 - linux
permalink: linux-command-pandoc
---

----

今天在查找html转markdown工具时发现出pandoc，真是不用不知道，一用吓一跳。
相关介绍可以看一看它的wiki<http://zh.wikipedia.org/zh-cn/Pandoc>
咱这里只说一说它的用法吧。
先安装`sudo apt-get install pandoc`
看看帮助：
```
pandoc -h
pandoc [OPTIONS] [FILES]
Input formats:  native, json, markdown, markdown+lhs, rst, rst+lhs, docbook,
                textile, html, latex, latex+lhs
Output formats: native, json, html, html5, html+lhs, html5+lhs, s5, slidy,
                slideous, dzslides, docbook, opendocument, latex, latex+lhs,
                beamer, beamer+lhs, context, texinfo, man, markdown,
                markdown+lhs, plain, rst, rst+lhs, mediawiki, textile, rtf, org,
                asciidoc, odt, docx, epub
Options:
  -f FORMAT, -r FORMAT  --from=FORMAT, --read=FORMAT
  -t FORMAT, -w FORMAT  --to=FORMAT, --write=FORMAT
  -o FILENAME           --output=FILENAME
                        --data-dir=DIRECTORY
                        --strict
  -R                    --parse-raw
  -S                    --smart
                        --old-dashes
                        --base-header-level=NUMBER
                        --indented-code-classes=STRING
                        --normalize
  -p                    --preserve-tabs
                        --tab-stop=NUMBER
  -s                    --standalone
                        --template=FILENAME
  -V KEY[:VALUE]        --variable=KEY[:VALUE]
  -D FORMAT             --print-default-template=FORMAT
                        --no-wrap
                        --columns=NUMBER
                        --toc, --table-of-contents
                        --no-highlight
                        --highlight-style=STYLE
  -H FILENAME           --include-in-header=FILENAME
  -B FILENAME           --include-before-body=FILENAME
  -A FILENAME           --include-after-body=FILENAME
                        --self-contained
                        --offline
  -5                    --html5
                        --ascii
                        --reference-links
                        --atx-headers
                        --chapters
  -N                    --number-sections
                        --no-tex-ligatures
                        --listings
  -i                    --incremental
                        --slide-level=NUMBER
                        --section-divs
                        --email-obfuscation=none|javascript|references
                        --id-prefix=STRING
---
  -T STRING             --title-prefix=STRING
  -c URL                --css=URL
                        --reference-odt=FILENAME
                        --reference-docx=FILENAME
                        --epub-stylesheet=FILENAME
                        --epub-cover-image=FILENAME
                        --epub-metadata=FILENAME
                        --epub-embed-font=FILE
                        --latex-engine=PROGRAM
                        --bibliography=FILENAME
                        --csl=FILENAME
                        --citation-abbreviations=FILENAME
                        --natbib
                        --biblatex
  -m[URL]               --latexmathml[=URL], --asciimathml[=URL]
                        --mathml[=URL]
                        --mimetex[=URL]
                        --webtex[=URL]
                        --jsmath[=URL]
                        --mathjax[=URL]
                        --gladtex
                        --dump-args
                        --ignore-args
  -v                    --version
  -h                    --help
```

瑞士军刀真不是白叫的，各种类型之间的转换，真是强大.
它之间的对应关系网上有图在这里
<http://johnmacfarlane.net/pandoc/diagram.png>

```
#将html转成markdown 如下:
pandoc -f html -t markdown -o test.md test.html
```
大家可以试试，非常不错.

