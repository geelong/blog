getwidth f方法包括了paddingLeft 和paddingRight的长度，不包括marginLeft和marginRight



 
###   public View inflate(int resource, ViewGroup root)

root为null的话，resource顶层的layoutparams是不会生成的


### public View inflate(int resource, ViewGroup root, boolean attachToRoot)

root非空，attachToRoot=false，顶层view的layoutparams可以生成
