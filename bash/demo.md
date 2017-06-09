---
title: Bash Script Demo
---

# Bash Script Demo

## Change Wallpaper

The following script generates a wallpaper picture with black background,
and put the specified text (from command line) in the center.
Then set it to be the current wallpaper.

```bash
#!/bin/bash

tn=tmp_text
fn=tmp_wallpaper
p=/tmp
path1=$p$tn.jpg
path2=$p$fn.jpg
size=1366x768

convert -background black -fill white -pointsize 24 label:@$1 $path1
convert -size $size xc:black $path2
composite -gravity center $path1 $path2 $path2

gsettings set org.gnome.desktop.background picture-uri file://$path2
rm $path1
```

## Text to PDF

If you installed `texlive-xelatex`, this following script helps you quickly transform a text file into PDF.
Replace the font name with the one you like.

```
#!/bin/bash

header='\documentclass[11pt]{book}
\usepackage{xeCJK}
\setCJKmainfont{FZYingBiKaiShu-S15S}
\setmainfont{FZYingBiKaiShu-S15S}
\begin{document}
'
tmpfile=`mktemp -u`.tex

footer='
\end{document}
'

if [ $# -gt 0 ]; then
	cat > $tmpfile << XXX#End
	$header
XXX#End
	sed 's/\(^.*$\)/\1\n/;s/ /\\ /g;s/%/\\%/g' $1 >> $tmpfile
	cat >> $tmpfile << XXX#End
	$footer
XXX#End
	pushd /tmp > /dev/null
	xelatex $tmpfile >> /dev/null
	popd > /dev/null
	mv ${tmpfile/tex/pdf} ${1/txt/pdf}
else
	print 'Usage: txt2pdf filename';
	exit 1
fi
```
