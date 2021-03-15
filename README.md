# Transfer-fasta-format-to-phylip

使用paml软件里的mcmctree功能需要phylip格式的比对结果,找了以下,发现用R包phylotools转化最方便，另外提醒一下要用mcmctree的朋友，我发现mcmctree的序列名字长度不能超过50个字符，如果超出的话，建议先改名字再转化。

先安装依赖的包（要先装BioConductor）：
```
install.packages("BiocManager")
BiocManager::install('devtools')
BiocManager::install('phylotools')
```
假设fasta文件名为: aligned_fasta.fasta 读取fasta文件，转化：
```
library(devtools)
library(phylotools)
dat <- read.fasta("aligned_fasta.fasta")
dat2phylip(dat, outfile = "out.phy")
```
结果文件为out.phy
注意:生成out.phy里,第一列序列名和第二列序列只有一个空格,而mcmctree要求两个以上,所以需要人工加多一个空格.

也可以用shell命令生成:
```
cat SpeciesTreeAlignment.fa |tr '\n' '\t'|sed 's/>/\n/g' |sed 's/\t/      /'|sed 's/\t//g'| awk 'NF > 0' > supergene.phy.tmp

awk '{print "  "NR"  "length($2)}'  supergene.phy.tmp|tail -n 1 | cat -  supergene.phy.tmp >  supergene.phy


在括号内说明每一步的作用:
cat SpeciesTreeAlignment.fa |tr '\n' '\t' (将换行符替换为制表符) | sed 's/>/\n/g' (将每个序列名前面的>符号替换为换行符) |sed 's/\t/      /' (将每行第一个的制表符替换为多个空格) |sed 's/\t//g' (删除剩余的制表符,使序列连成一条线) | awk 'NF > 0' (删除空行)> supergene.phy.tmp (临时保存)

awk '{print "  "NR"  "length($2)}'  supergene.phy.tmp (计算序列长度)| tail -n 1 (返回最后一行,包括了行数与序列长度) | cat -  supergene.phy.tmp (-为上一管道数据) >  supergene.phy(最终文件)
```
