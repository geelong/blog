1、删除classes.dex META-INF 等不一致的信息

2、解压

3、修改所有解压文件的lastModifys熟悉（ touch -t 01010000 ）

4、重新压缩 zip -rX a .  (去除额外属性)