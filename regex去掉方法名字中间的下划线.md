原来

(public.*?)_(.)

替换成

$1\u$2

\u 转出大写
\L 转出小写

替换前
public long getOpeningCeremony_time() 

替换后

public long getOpeningCeremonyTime() 
